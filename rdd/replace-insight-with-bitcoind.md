# Replacing Insight API with Bitcoin Core

We want to replace the Insight API dependency of the Po.et node with a standard Bitcoin Core dependency.

## Related GitHub Issues
- https://github.com/poetapp/node/issues/24
- https://github.com/poetapp/node/issues/25
- https://github.com/poetapp/node/issues/22
- https://github.com/poetapp/node/issues/106
- https://github.com/poetapp/poet-js/issues/51
- https://github.com/poetapp/poet-js/issues/52
- https://github.com/poetapp/poet-js/issues/53

## Background

We originally coded the node to use Insight API because it was simpler and we already had the required know-how in the team. It would get us faster to the release of the PoC.

To make things even quicker we chose to use the Insight API instance publicly and freely offered by Bitpay rather than running our own.

After working with it for a couple of months we found out that instance would often experience long periods of downtime. This was not only a problem to our production environment - in many cases it would completely prevent us from testing our work, thus blocking us from developing altogether.

We then decided running our own Insight API was the highest priority and started working on it, but found out it [wasn't as simple as we imagined](https://github.com/poetapp/poet/wiki/Running-a-Full-Bitcore-Node-(Testnet)).

Further analysis surfaced more reasons not to use Insight API, or any Bitpay product for that matter.

## Reasons

- Insight API doesn't seem to be maintained any more.
- Insight API is rather complex to set up, which makes deployment, maintenance and integration testing much harder.
- Insight API needs a bitcoin-compatible backend. It can be configured to use Bitcoin Core. It uses [BCoin](https://github.com/bcoin-org/bcoin) by default. We're going to have to maintain a bitcoind (or alternative) client even if we keep Insight API.
- It's an extra layer of things that can break.
- Even though it can run with bitcoind as a backend, there are user reports of it being incompatible with the latests versions.
- It's poorly documented.

## Challenges

- bitcoind doesn't index the whole UTXO set by address (unlike Insight API which does, making the only requirement to create transactions having a private key), so we need to use the bitcoind wallet, letting it manage the keys for us and controlling it via RPC.
- I don't know whether using a Bitcoin Core Wallet in production is safe or recommended.

## Non-Challenges

### One Wallet per Instance

Gavin Andresen stated that a Bitcoin Core wallet should not be used by more than one Bitcoin Core instance. 

> Cloning your wallet to do anything besides backing it up is a bad idea.
>
> It might work perfectly for a while... but you are very likely to get weird behavior from bitcoin sooner or later, because doing that is not tested or supported.
> 
> Where 'weird behavior' is one clone of the wallet shows one balance, the other clone shows another, and you might end up with bitcoins that you can spend from one wallet but not the other (or, worst case, end up with bitcoins that neither wallet is able to spend).
>
> If you REALLY REALLY REALLY want to do this... then get the latest code from git, pull sipa's 'Spent per txout' patch, and then spend a bunch of time testing to find out what happens when your cloned wallets eventually start using different keys from the keypool.

This is a message by Gavin Andresen, then development leader of Bitcoin Core, [from April 02, 2011, 07:24:19 PM](https://bitcointalk.org/index.php?topic=5324.msg77896#msg77896). 

Assumming this reasoning is still valid after seven years, scaling by having several Bitcoin Core instances behind a Load Balancer would require each instance to have its own wallet.

This isn't a problem since Claim Batching allows us to only broadcast a single transaction every ten or more minutes no matter how much user activity we have.

Heavier load can happen in read operations, which require no wallet.

So we can do with a single instance having a wallet, maybe another as backup should the first one fail, and any amount of wallet-less instances used for read operations.

## Advantages of using a Bitcoin Core Wallet

### No Private Keys

The node won't need to know any private keys, which should reduce the attack surface.

Instead of directly managing keys, the node will use RPC auth and delegate key management and signing to the wallet.

If an attacker gets access to the bitcoind RPC auth, they still need access to the bitcoind instance to do anything with it.

### HD Wallets

With Bitcoin Core Wallet we get out-of-the-box HD addresses. 

## Required Changes

### BlockchainWriter

The [src/BlockchainWriter/ClaimController#timestamp](https://github.com/poetapp/node/blob/45fa1002cf765de6ce755a83ffa38a55d0aa7e3e/src/BlockchainWriter/ClaimController.ts#L76-L136) function will need to be updated.

It's currently using Insight to get UTXO...

```ts
const utxo = await this.insightHelper.getUtxo(this.configuration.bitcoinAddress)
```

... Bitcore to create the transaction with the OP_RETURN output...

```ts
const data = Buffer.concat([
  Buffer.from(this.configuration.poetNetwork),
  Buffer.from([...this.configuration.poetVersion]),
  Buffer.from(ipfsDirectoryHash),
])
const tx = new bitcore.Transaction()
  .from(topUtxo)
  .change(this.configuration.bitcoinAddress)
  .addData(data)
  .sign(this.configuration.bitcoinAddressPrivateKey)
```

... and Insight again to broadcast the transaction.

```ts
const txPostResponse = await this.insightHelper.broadcastTx(tx)
```

There's an example of how to implement this in [bitcoind-client](https://github.com/poetapp/bitcoind-client/blob/master/src/Blockchain.js).

It basically boils down to:

```ts
import * as BitcoinCore from 'bitcoin-core'

const configuration = {
  port: 8332,
  username: "user",
  password: "password"
}

const bitcoinCore = new BitcoinCore(configuration)

const utxos = await bitcoinCore.listUnspent()
const utxo = utxos[0] // Just an example. See current implementation in the node for smarter UTXO selection

if (!utxo) // Just an example, this validation isn't good enough
  throw new Error('No balance in wallet')

const address = utxo.address
const inputs = [{ txid: utxo.txid, vout: utxo.vout }]
const data = Buffer.from('POETVERSIONipfsDirectoryHashEtc').toString('hex')
const outputs = { data, [address]: 0 } // Tx change TBD
const rawTx = await bitcoinCore.createRawTransaction(inputs, outputs)
const tx = await bitcoinCore.signRawTransaction(rawTx)
return tx.hex // or tx.id
```

#### Creating Transaction Client-Side

Alternatively, the transaction could be created on the node by using BitcoinJS or any similar library.

In this case, we could either still use the wallet and keep the call to `signRawTransaction` or stick with the current method of having the node know a private key and an address and using `importaddress` so the wallet is aware of the address and `listunspent` works as expected.


The code would roughly look like this:

```ts
import { ECPair, opcodes, script, Transaction, TransactionBuilder, networks } from 'bitcoinjs-lib'

const address = 'mgxVT9fzHwYDsgEGJSZekKgYbAyrBkqdpi'

export function createTransaction(): Transaction {
  const key = ECPair.fromWIF('034b468e3a16afe247b3479fd45866c6636a609173ae7883a2b6df2757b09e1797', networks.testnet)
  const txb = new TransactionBuilder(networks.testnet)
  
  const utxoTx = '61d520ccb74288c96bc1a2b20ea1c0d5a704776dd0164a396efec3ea7040349d' // Use listunspent
  const utxoVout = 0
  
  txb.addInput(utxoTx, utxoVout)
  txb.addOutput(address, 15000)
  txb.addOutput(createDataOutput(Buffer.from('Testing Data')), 0)

  txb.sign(0, key)

  return txb.build()
}

function createDataOutput(data: Buffer) {
  return script.compile([
    opcodes.OP_RETURN,
    data,
  ])
}
```

##### Pros
- Could be written as a pure function, completely unit testable

##### Cons
- More CPU, less IO. [1]
- Adds another library to the mix

### BlockchainReader

We'll need to update the [src/BlockchainReader/ClaimController#scanBlock](https://github.com/poetapp/node/blob/45fa1002cf765de6ce755a83ffa38a55d0aa7e3e/src/BlockchainReader/ClaimController.ts#L36-L92) function.

`scanBlock` uses Insight to retrieve a block's hash by its height...

```ts
const blockHash = await this.insightHelper.getBlockHash(blockHeight)
```

... and to get the block by its hash.

```ts
const block = await this.insightHelper.getBlock(blockHash)
```

This should be relatively straightforward, since both functions are supported by Bitcoin Core out of the box.

```ts
import * as BitcoinCore from 'bitcoin-core'

const configuration = {
  port: 8332,
  username: "user",
  password: "password"
}

const bitcoinCore = new BitcoinCore(configuration)

const blockHeight = 5 // just an example
const blockHash = await bitcoinCore.getblockhash(blockHeight)
const block = await bitcoinCore.getblock(blockHash)
```

### Bitcoin Helper

We'll need to update [all three functions in this helper](https://github.com/poetapp/node/blob/45fa1002cf765de6ce755a83ffa38a55d0aa7e3e/src/Helpers/Bitcoin.ts).

Parsing the OP_RETURN output is straight forward, but we may use BitcoinJS to simplify code and reduce need for unit tests.

## New dependencies
- Rui Marinho's [bitcoin-core](https://github.com/ruimarinho/bitcoin-core), a simple wrapper over the Bitcoin Core RPC
- Rui Marinho's [Bitcoin Core Docker Image](https://github.com/ruimarinho/docker-bitcoin-core) for local development
- [BitcoinJS](https://github.com/bitcoinjs/bitcoinjs-lib)

## Bitcoin Core RPC Calls
- [getblockhash](https://bitcoin.org/en/developer-reference#getblockhash)
- [getblock](https://bitcoin.org/en/developer-reference#getblock)
- [listunspent](https://bitcoin.org/en/developer-reference#listunspent)
- [createrawtransaction](https://bitcoin.org/en/developer-reference#createrawtransaction)
- [signrawtransaction](https://bitcoin.org/en/developer-reference#signrawtransaction)
- [importaddress](https://bitcoin.org/en/developer-reference#importaddress)

## Prior Work

- https://github.com/poetapp/poet/wiki/Running-a-Full-Bitcore-Node-(Testnet)
- https://github.com/poetapp/bitcoind-docker
- https://github.com/poetapp/bitcoind-client

## Notes
- 1: Roughly speaking, NodeJS is great at IO and bad at computation. C and C++, the other way round.
