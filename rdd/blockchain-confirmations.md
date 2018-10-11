# Blockchain Confirmations

Bitcoin transactions can only be considered safe after they have a certain amount of confirmations. 

The node needs to know how many confirmations transactions have and fail-safe in case one or more transactions aren't getting confirmed as expected.

## Required Confirmation Count

Due to how Proof of Work works, there isn't a defined line that separates a safe from an unsafe amount of confirmations. How few confirmations one is willing to accept, or how many require, is a decision that needs to be weighted for each use case. 

It is generally considered that three confirmations is safe for transactions moving a low amount of funds, while for larger transactions one may want to wait for six.

TODO: add references to this

## Cases

- Transaction is in mempool for too long, never gets in a block (low fee)
- Transaction disappears from mempool and never gets in a block (most likely discarded after too long in mempool)
- Transaction gets into a block and sometime after that block becomes stale (reorg)

## Measuring Time in Blocks

It makes sense to track at what block height the transaction was created and how many blocks have been mined/generated since, rather than time passed.

Measuring blocks instead of time is a blockchain specific solution (bitcoin generates blocks every ten minutes, ethereum every eight to twelve seconds), but that's perfectly fine.

## Mempool

Can use [getrawmempool](https://bitcoin.org/en/developer-reference#getrawmempool) to get a list of transaction ids that are in the mempool but haven't made it to any block

## Functional Testing

Testing the different scenarios can be a bit complicated and require some orchestration.

I haven't decided or tried anything yet, but here's some information and ideas:

### Never Confirmed

This one is easy: just never `generate`.

### Mempool Clearing

Clearing the mempool requires restarting the node with [-zapwallettxes](https://github.com/bitcoin/bitcoin/blob/ae1cc010b88dd594d2a27b2717cfe14ef04ec852/src/wallet/wallet.cpp#L3882). I haven't found a way to do this without restarting. And I'm not 100% sure -zapwallettxes is enough.

### Reorg Simulation

- Having two bitcoin-core instances communicating, sharing the shame blockchain, then cutting off communication, growing one of the chains one block and the other two (different) blocks and then enabling communication again. 
- Using the hidden [invalidate and reconsider block](https://github.com/bitcoin/bitcoin/blob/b8edb9810a699015e997e2098dddb2a6cfacbed6/src/rpc/blockchain.cpp#L1486-L1559) calls. Maybe [preciousblock](https://bitcoincore.org/en/doc/0.17.0/rpc/blockchain/preciousblock/) can help, too.

Connecting and disconnecting to/from the network seems to be possible with the RPC calls [setnetworkactive](https://bitcoincore.org/en/doc/0.17.0/rpc/network/setnetworkactive/), [addnode](https://bitcoincore.org/en/doc/0.17.0/rpc/network/addnode/) and [disconnectnode](https://bitcoincore.org/en/doc/0.17.0/rpc/network/disconnectnode/).

### Higher Level Orchestration

Given that all testing scenarios require interacting with dependencies, restarting them, passing arguments to them, etc, I believe it makes sense to consider these tests to live in a higher layer, over the application itself.

Maybe a good use case for docker-in-docker. 

For example (not ideal, just an example):

```
$ node first-part-of-test.js
$ ./disable-bitcoind-communication.sh
$ node second-part-of-test.js
# etc
```

## Don't Build Over Unconfirmed Data

Reattempting transactions means we can't guarantee claim order until they are confirmed, which means you should only refer to claims that have been confirmed. This is pretty standard in the blockchain world - one should never rely on unconfirmed data.

## Changes

### BlockchainReader.ClaimController.scanBlock
- rename and modify `PoetBlockAnchorsDownloaded(matchingAnchors)` to `BlockDownloaded(blockHeight, matchingAnchors)` ,
- remove `if (matchingAnchors.length)` - publish BlockDownloaded for all blocks

### BlockchainWriter

#### Router / Controller

```js
consume(BlockDownloaded(blockHeight, matchingTransactionIds)) => 
  const setBlockHeight = dao.setBlockHeight(blockHeight) 
  await matchingTransactionIds.map(setBlockHeight)
  const oldBlocklessTransactions = await dao.findOldBlocklessTransactions({ currentBlockHeight: blockMined.blockHeight, maximumBlockAge })
  // logger.warning / messaging.publish / dao.reattempt
```

#### DAO

```js
  findBlocklessTransactions = maximumBlockAge => currentBlockHeight => db.blockchainWriter.find({ 
    tx: { $not: null }, 
    creationBlockHeight: { $lt: currentBlockHeight - maximumBlockAge }, 
    blockHeight: null 
  })
  
  setBlockHeight = blockHeight => txid => db.blockchainWriter.update({ txid }, { $set: { blockHeight } })
```

## References
1. [Chain Reorganization](https://en.bitcoin.it/wiki/Chain_Reorganization)
1. [Orphan / Stale Block](https://en.bitcoin.it/wiki/Orphan_Block)
1. [How many confirmations do I need to ensure a transaction is successful?](https://bitcoin.stackexchange.com/questions/8360/how-many-confirmations-do-i-need-to-ensure-a-transaction-is-successful)
