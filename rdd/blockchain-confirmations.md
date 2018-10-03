Blockchain Confirmations
- Blockchain Writer needs to know if too much time has passed since a transaction has been created if it hasn't been seen in any block
- makes sense to track at what block height the transaction was created and how many blocks have been mined/generated since, rather than time
- measuring blocks instead of time is a blockchain specific solution (bitcoin generates blocks every ten minutes, ethereum every eight to twelve seconds), but that's ok
- can use `bitcoin-cli getrawmempool` to get a list of transaction ids that are in the mempool but haven't made it to any block
- can test by destroying the bitcoin-regtest container after the transaction has been created and before it has been included in a block
- reattempting transactions means we can't guarantee claim order until they are confirmed, 
- which means you should only refer to claims that have been confirmed

- BlockchainReader.ClaimController.scanBlock
- - rename and modify PoetBlockAnchorsDownloaded(matchingAnchors) to BlockDownloaded(blockHeight, matchingAnchors) ,
- - remove `if (matchingAnchors.length)` - publish BlockDownloaded for all blocks

- consume(BlockDownloaded(blockHeight, matchingTransactionIds)) => 
- - const setBlockHeight = dao.setBlockHeight(blockHeight) 
- - await matchingTransactionIds.map(setBlockHeight)
- - const oldBlocklessTransactions = await dao.findOldBlocklessTransactions({ currentBlockHeight: blockMined.blockHeight, maximumBlockAge })
- - // logger.warning / messaging.publish / dao.reattempt

- dao
- - findBlocklessTransactions = db.blockchainWriter.find({ tx: { $not: null }, creationBlockHeight: { $lt: currentBlockHeight - maximumBlockAge }, blockHeight: null })
- - setBlockHeight = blockHeight => txid => db.blockchainWriter.update({ txid }, { $set: { blockHeight } })
