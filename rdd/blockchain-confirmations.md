# Blockchain Confirmations

The node needs to know if too much time has passed since a transaction has been created and it hasn't yet been seen in any block.

## Measuring Time in Blocks

It makes sense to track at what block height the transaction was created and how many blocks have been mined/generated since, rather than time passed.

Measuring blocks instead of time is a blockchain specific solution (bitcoin generates blocks every ten minutes, ethereum every eight to twelve seconds), but that's perfectly fine.

## Mempool

Can use [getrawmempool](https://bitcoin.org/en/developer-reference#getrawmempool) to get a list of transaction ids that are in the mempool but haven't made it to any block

## Testing

Can test by destroying the bitcoin-regtest container after the transaction has been created and before it has been included in a block

## Wait For It

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
