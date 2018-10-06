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

## Testing

Can test by destroying the bitcoin-regtest container after the transaction has been created and before it has been included in a block

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
