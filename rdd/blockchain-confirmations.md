# Blockchain Confirmations

Bitcoin transactions can only be considered safe after they have a certain amount of confirmations. 

The node needs to know how many confirmations transactions have and trigger a retry mechanism in case one or more transactions aren't getting confirmed as expected.

## Index
- [Related Issues](#related-issues)
- [Cases](#cases)
  - [Transaction Lost](#transaction-lost)
  - [Transaction Stuck](#transaction-stuck)
  - [Chain Reorganization](#chain-reorganization)
- [Implementation](#implementation)
- [Functional Testing](#functional-testing)
- [Notes For Clients](#notes-for-clients)
- [RPCs](#rpcs)
- [References](#references)

## Related Issues
- https://github.com/poetapp/node/issues/46

## Cases

There are three different cases the node needs to be aware of and know how to respond to.

### Transaction Lost

Transaction disappears from mempool and never gets into a block. 

Most likely discarded after being stuck in the mempool for too long, though there is no way to know for sure.

#### Solution

This should seldom happen. If it does, the node should discard the lost broadcasted transaction and create and broadcast a new one.

### Transaction Stuck

Transaction is in mempool for too long, never gets into a block. 

Probably due to low fee, though there is no way to know for sure.

Could also be a symptom of Bitcoin scaling issues.

#### Solution

Though there's no guarantee of this working, the best attempt it to replace the transaction with one with higher fee, basically creating a double-spend. Miners should choose the transaction with higher fee, rendering the one with lower fee invalid.

If the root cause is more related to Bitcoin scalability than Po.et, there is nothing to be done.

We can use [getrawmempool](https://bitcoin.org/en/developer-reference#getrawmempool) and [getmempoolentry](https://bitcoincore.org/en/doc/0.17.0/rpc/blockchain/getmempoolentry/) to know whether a transaction is in the mempool or not.

Issues may arise due to the mempool not being decentralized: a transaction existing in Node A's mempool may have been discarded from Node B's mempool.

##### New Fee

The fee is currently determined by Bitcoin Core. Initially, we wil want to let Bitcoin Core determine the new fee, too, and hope for it to be high enough. 

In a future iteration, we can 
- use [estimatesmartfee](https://bitcoincore.org/en/doc/0.17.0/rpc/util/estimatesmartfee/) to get the Bitcoin Core Wallet's recommended `feeRate`, 
- manually increase it by a configurable percentage,
- pass it to [fundrawtransaction](https://bitcoincore.org/en/doc/0.17.0/rpc/rawtransactions/fundrawtransaction/)
- and set the `replaceable` option of `fundrawtransaction` to `true`.

### Chain Reorganization

Transaction gets into a block and sometime after that block becomes stale, no longer being part of the active chain.

This is a completely normal event in Bitcoin, Ethereum and other blockchains. It is also the most complicated case to handle.

#### Solution

Miners will usually move the transactions from the staled/orphaned block back to the mempool, which should solve the issue.

If this does not happen, there are various approaches to address it manually:

- Keeping track of the `previousBlockHash` of every block, re-validate N last blocks on new block, signal reorg from BlockchainReader and let other modules adapt
- Watching the info returned by [getchaintips](https://bitcoincore.org/en/doc/0.17.0/rpc/blockchain/getchaintips/).

A reorg may mean that data that was valid no longer is. This does not affect our current system, as updates are not supported and Work is the only claim type in use, but the introduction of Entities, Identity Claims, Licences, etc, will require more attention.

## Implementation

### BlockchainReader.ClaimController.scanBlock
- `interface LightBlock { hash: string, previousHash: string, height: number }`
- rename and modify `PoetBlockAnchorsDownloaded(matchingAnchors)` to `BlockDownloaded(lightBlock, matchingAnchors)` ,
- remove `if (matchingAnchors.length)` - publish BlockDownloaded for all blocks
- `if (highestBlockInDB.hash !=== lightBlock.previousHash) logger.warn({ lightBlock, }, 'Reorg detected')`
- Detect reorgs but do nothing more than logging a warning for them for now.

### BlockchainWriter

#### Router / Controller

```js
consume(BlockDownloaded(lightBlock, matchingAnchors)) => 
  const setBlockHeightAndHash = dao.setBlockHeightAndHash(lightBlock.height, lightBlock.hash) 
  const transactionIds = matchingAnchors.map(_ => _.transactionId)
  await transactionIds.map(setBlockHeightAndHash)
  const oldBlocklessTransactions = await dao.findOldBlocklessTransactions({ currentBlockHeight: lightBlock.height, maximumBlockAge })
  logger.warning({ maximumBlockAge, oldBlocklessTransactions }, 'Discarding unconfirmed transactions')
  await dao.clearTransactions(oldBlocklessTransactions)
```

#### DAO

```js
  findBlocklessTransactions = maximumBlockAge => currentBlockHeight => db.blockchainWriter.find({ 
    tx: { $not: null }, 
    creationBlockHeight: { $lt: currentBlockHeight - maximumBlockAge }, 
    blockHeight: null 
  })
  
  setBlockHeightAndHash = (blockHeight, blockHash) => txid => db.blockchainWriter.update({ txid }, { $set: { blockHeight, blockHash } })
```

## Functional Testing

Testing the different scenarios can be a bit complicated and require some orchestration.

I haven't decided or tried anything yet, but here's some information and ideas:

### Never Confirmed

Starting the node with `-blockmintxfee=<amt>` wih a high `amt` allows us to `generate` blocks that do not include our transaction.

### Mempool Clearing

Clearing the mempool requires restarting the node with [-zapwallettxes](https://github.com/bitcoin/bitcoin/blob/ae1cc010b88dd594d2a27b2717cfe14ef04ec852/src/wallet/wallet.cpp#L3882). I haven't found a way to do this without restarting. And I'm not 100% sure -zapwallettxes is enough.

### Reorg Simulation

- Having two bitcoin-core instances communicating, sharing the same blockchain, then cutting off communication, growing one of the chains one block and the other two (different) blocks and then enabling communication again. 
- Using the hidden [invalidate and reconsider block](https://github.com/bitcoin/bitcoin/blob/b8edb9810a699015e997e2098dddb2a6cfacbed6/src/rpc/blockchain.cpp#L1486-L1559) calls. Maybe [preciousblock](https://bitcoincore.org/en/doc/0.17.0/rpc/blockchain/preciousblock/) can help, too.

Connecting and disconnecting to/from the network seems to be possible with the RPC calls [setnetworkactive](https://bitcoincore.org/en/doc/0.17.0/rpc/network/setnetworkactive/), [addnode](https://bitcoincore.org/en/doc/0.17.0/rpc/network/addnode/) and [disconnectnode](https://bitcoincore.org/en/doc/0.17.0/rpc/network/disconnectnode/).

### Higher Level Orchestration

Given that all testing scenarios require interacting with dependencies, restarting them, passing arguments to them, etc, I believe it makes sense to consider these tests to live in a higher layer, over the application itself.

A naive example of such orchestration:

```
$ alias dc=docker-compose
$ dc up -d
$ dc exec tests node create-10-claims.js node-a
$ dc exec bitcoind1 bitcoin-cli setnetworkactive false
$ dc exec tests bash node second-part-of-test.js
$ dc exec bitcoind1 bitcoin-cli setnetworkactive true
$ dc exec tests node third-part-of-test.js
$ dc stop bitcoind1
$ dc -f bitcoin-zap-wallet up -d bitcoind1
$ dc exec tests node fourth-part-of-test.js
```

We could achieve such control with Docker [exposing the Docker socket to the test orchestration container](https://jpetazzo.github.io/2015/09/03/do-not-use-docker-in-docker-for-ci/). 

## Notes For Clients

### Build Over Confirmed Data

_Reattempting transactions and reorgs mean claim order or validity cannot be guaranteed for claims with no or few confirmations._

When creating new claims that depend on or refer to previous claims, clients should make sure the previous claims have _enough_ confirmations.

Due to how Proof of Work works, there isn't a defined line that separates a safe from an unsafe amount of confirmations. How few confirmations one is willing to accept, or how many require, is a decision that needs to be weighted for each use case. 

It is generally considered that three confirmations is safe for transactions moving a low to medium amount of funds, while for larger transactions one may want to wait for six.

To stay safe from reorgs to number could be as high as five, although less should usually suffice, while more may be required on ocassion in the future. 

Special attention should be given to claims that involve money, such as purchases or sales of licenses. 

## RPCs
1. [getrawmempool](https://bitcoin.org/en/developer-reference#getrawmempool)
1. [getmempoolentry](https://bitcoincore.org/en/doc/0.17.0/rpc/blockchain/getmempoolentry/)
1. [getchaintips](https://bitcoincore.org/en/doc/0.17.0/rpc/blockchain/getchaintips/)
1. [setnetworkactive](https://bitcoincore.org/en/doc/0.17.0/rpc/network/setnetworkactive/)
1. [addnode](https://bitcoincore.org/en/doc/0.17.0/rpc/network/addnode/)
1. [disconnectnode](https://bitcoincore.org/en/doc/0.17.0/rpc/network/disconnectnode/)

## References
1. [Chain Reorganization](https://en.bitcoin.it/wiki/Chain_Reorganization)
1. [Orphan / Stale Block](https://en.bitcoin.it/wiki/Orphan_Block)
1. [How many confirmations do I need to ensure a transaction is successful?](https://bitcoin.stackexchange.com/questions/8360/how-many-confirmations-do-i-need-to-ensure-a-transaction-is-successful)
1. [Why is 6 the number of confirms that is considered secure?](https://bitcoin.stackexchange.com/questions/1170/why-is-6-the-number-of-confirms-that-is-considered-secure)
1. [Using Docker-in-Docker for your CI or testing environment? Think twice.
](https://jpetazzo.github.io/2015/09/03/do-not-use-docker-in-docker-for-ci/)
1. [What is the longest blockchain fork that has been orphaned to date?](https://bitcoin.stackexchange.com/questions/3343/what-is-the-longest-blockchain-fork-that-has-been-orphaned-to-date/4638)
1. [How does the mempool work? What happens to the mempool when there are two equal length chains on the network?](https://bitcoin.stackexchange.com/questions/69448/how-does-the-mempool-work-what-happens-to-the-mempool-when-there-are-two-equal?rq=1)
1. [Valid fork in regtest - Change blockchain via RPC?](https://bitcoin.stackexchange.com/questions/37340/valid-fork-in-regtest-change-blockchain-via-rpc)
1. [How to detect reorganization from bitcoind via ZMQ?](https://bitcoin.stackexchange.com/questions/52891/how-to-detect-reorganization-from-bitcoind-via-zmq)
1. [How to detect a fork with bitcoin-cli?](https://bitcoin.stackexchange.com/questions/44437/how-to-detect-a-fork-with-bitcoin-cli?rq=1)
