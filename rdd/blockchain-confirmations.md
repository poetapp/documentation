# Blockchain Confirmations

Bitcoin transactions can only be considered safe after they have a certain amount of confirmations. 

The node needs to know how many confirmations transactions have and trigger a retry mechanism in case one or more transactions aren't getting confirmed as expected.

## Related Issues
- https://github.com/poetapp/node/issues/46

## Cases

There are three different cases the node needs to be aware of and know how to respond to.

### Transaction Lost

Transaction disappears from mempool and never gets in a block. 

Most likely discarded after being stuck in the mempool for too long, though there is no way to know for sure.

#### Solution

This should seldom happen. If it does, the node should be able to update the blockchainWriter collection, emptying the `transactionId` field for the corresponding entries, which will trigger a new transaction creationg and broadcast.

### Transaction Stuck

Transaction is in mempool for too long, never gets in a block. 

Probably due to low fee, though there is no way to know for sure.

Could also be a sympton of Bitcoin scaling issues.

#### Solution

Though there's no guarantee of this working, the best attempt it to replace the transaction with one with higher fee, basically creating a double-spend. Miners should choose the transaction with higher fee, rendering the one with lower fee invalid.

If the root cause is more related to Bitcoin scalability than Po.et, there is nothing to be done.

We can use the [getrawmempool](https://bitcoin.org/en/developer-reference#getrawmempool) and [getmempoolentry](https://bitcoincore.org/en/doc/0.17.0/rpc/blockchain/getmempoolentry/) to know whether a transaction is in the mempool or not.

Issues may arise due to the mempool not being decentralized: a tranasction existing in Node A's mempool may have been discarded from Node B's mempool.

### Reorg

Transaction gets into a block and sometime after that block becomes stale.

This is a completely normal even in Bitcoin, Ethereum and other blockchains. It is also the most complicated case to handle.

#### Solution

There are various approaches to this issue.

- Keep track of the `previousBlockHash` of every block, re-validate last blocks on new block, signal reorg from BlockchainReader and let other modules adapt
- Watching the info returned by [getchaintips](https://bitcoincore.org/en/doc/0.17.0/rpc/blockchain/getchaintips/).

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

## Functional Testing

Testing the different scenarios can be a bit complicated and require some orchestration.

I haven't decided or tried anything yet, but here's some information and ideas:

### Never Confirmed

This one is easy: just never `generate`.

### Mempool Clearing

Clearing the mempool requires restarting the node with [-zapwallettxes](https://github.com/bitcoin/bitcoin/blob/ae1cc010b88dd594d2a27b2717cfe14ef04ec852/src/wallet/wallet.cpp#L3882). I haven't found a way to do this without restarting. And I'm not 100% sure -zapwallettxes is enough.

### Reorg Simulation

- Having two bitcoin-core instances communicating, sharing the same blockchain, then cutting off communication, growing one of the chains one block and the other two (different) blocks and then enabling communication again. 
- Using the hidden [invalidate and reconsider block](https://github.com/bitcoin/bitcoin/blob/b8edb9810a699015e997e2098dddb2a6cfacbed6/src/rpc/blockchain.cpp#L1486-L1559) calls. Maybe [preciousblock](https://bitcoincore.org/en/doc/0.17.0/rpc/blockchain/preciousblock/) can help, too.

Connecting and disconnecting to/from the network seems to be possible with the RPC calls [setnetworkactive](https://bitcoincore.org/en/doc/0.17.0/rpc/network/setnetworkactive/), [addnode](https://bitcoincore.org/en/doc/0.17.0/rpc/network/addnode/) and [disconnectnode](https://bitcoincore.org/en/doc/0.17.0/rpc/network/disconnectnode/).

### Higher Level Orchestration

Given that all testing scenarios require interacting with dependencies, restarting them, passing arguments to them, etc, I believe it makes sense to consider these tests to live in a higher layer, over the application itself.

Maybe a good use case for [fake docker-in-docker](https://jpetazzo.github.io/2015/09/03/do-not-use-docker-in-docker-for-ci/). 

For example (not ideal, just an example):

```
$ node first-part-of-test.js
$ ./disable-bitcoind-communication.sh
$ node second-part-of-test.js
# etc
```

Comment by Warren:

> Shutting down a container from within another container is problematic. We would have to mount docker.socket inside the container running the tests. This is considered an insecure practice but may be ok for testing.

## Notes

### Measuring Time in Blocks

It makes sense to track at what block height the transaction was created and how many blocks have been mined/generated since, rather than time passed.

Measuring blocks instead of time is a blockchain specific solution (bitcoin generates blocks every ten minutes, ethereum every eight to twelve seconds), but that's perfectly fine.

### Required Confirmation Count

Due to how Proof of Work works, there isn't a defined line that separates a safe from an unsafe amount of confirmations. How few confirmations one is willing to accept, or how many require, is a decision that needs to be weighted for each use case. 

It is generally considered that three confirmations is safe for transactions moving a low to medium amount of funds, while for larger transactions one may want to wait for six.

Five seems to be the required number to stay safe from reorgs, although that happened rarely and it's not guaranteed to be the maximum.

### Don't Build Over Unconfirmed Data

Reattempting transactions means we can't guarantee claim order until they are confirmed, which means you should only refer to claims that have been confirmed. This is pretty standard in the blockchain world - one should never rely on unconfirmed data.

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
