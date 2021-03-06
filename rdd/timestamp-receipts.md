# Timestamp Receipts

Timestamp Receipts are used to verify that a given file existed at a point in time by checking the hash of the timestamped file against a blockchain block.

## Related Issues 
- https://github.com/poetapp/node/issues/178
- https://github.com/poetapp/node/issues/112
- https://github.com/poetapp/node/issues/293

## Receipt Generation

Receipts are actual files a user requests, downloads and stores. These receipts contain all the data necessary to cryptographically prove that a file existed at a point in time.

This is achieved by storing all the data necessary to regenerate the hash that was stored in the blockchain: the file that we want to validate and the hashes of other necessary files of the same batch.

In practice, the verification algorithm requires: 
- the hash of the block at which the timestamp was stored,
- the Signed Verifiable Claim that we want to validate, 
- the work addressed by the signed Signed Verifiable Claim,
- and the hashes of other necessary files of the same Verifiable Claim batch.

## Receipt Verification

The verification algorithm will need to follow these steps:
- Ensure the Signed Verifiable Claim's id is correct, using Po.et-JS
- Calculate the hash of the work and ensure it matches the one provided in the Signed Verifiable Claim
- Create a Merkle Tree with the id of the Signed Verifiable Claim provided, along with the ids of the Verifiable Claims provided in the receipt
- Download the timestamp from the blockchain, given the blockHash provided in the receipt
- Compare the downloaded timestamp against the Merkle Root

Creating the Merkle Tree out of the Verifiable Claim Ids deterministically will require storing the position of the Verifiable Claim Id we are generating the receipt for in the receipt itself and sorting the ids deterministically.

## Merkle Trees

Using a Merkle Tree provide a deterministic way to reduce a set of hashes to a single hash and also allows us to reduce the amount of hashes that need to be stored and calculated. The latter is an optimization rather than a requirement, but it is a standard in the industry.

## Implementation

### Approach 1 — IPFS Hash as Receipt

We should be able to deterministically generate the same multihash IPFS generates for a given file or directory.

To calculate the hash of a file we would need to store the [different variables that IPFS uses to calculate the hash](https://discuss.ipfs.io/t/how-to-calculate-file-directory-hash/777) in the timestamp receipt to be able to validate the timestamp. 

We can use [js-ipld-dag-pb](https://github.com/ipld/js-ipld-dag-pb) to manually create a DAGNode and [js-ipfs](https://github.com/ipfs/js-ipfs) to create an IPFS object. This is equivalent to the operation `ipfs object put`.

IPFS files are merkledags of IPFS objects.

Calculating the hash of a directory requires further research. 

See [IPFS Hash Tests](https://github.com/poetapp/ipfs-hash-tests).

#### Pros
- No modification to our timestamping process
- No extra cost in fees.

### Cons
- Requires further experimentation and research.
- Requires low level understanding of IPFS internals.
- Documentation on the subject is lacking at best.

### Approach 2 — Add Timestamp to Anchor

We can store the Merkle/Patricia Root of the Verifiable Claims included in the Verifiable Claim Batch after the IPFS Directory Hash in the OP_RETURN data for the receipt to be storage-protocol independent. 

OP_RETURN seems to support [up to 83 bytes](https://bitcoin.org/en/developer-guide#null-data) of data (was 40 for a while). sha256 uses 32 bytes. We could RIMEMD160 it to reduce it to 20 bytes, as OTT does.

At [5 satoshis per byte](https://bitcoinfees.earn.com/), the extra 32 bytes to store the proof would cost approximately 0.001 USD. For a delay of < 30min, 200 satoshis per byte would mean an extra 0.37 USD per transaction. That would be 0.23 USD if we did 20 bytes instead of 32.

### Pros
- Almost no research needed. 
- Pretty straight forward. No unknowns should come up while implementing.
- Is storage protocol agnostic. We could add support for different storage mechanisms without having to upgrade the receipt logic.

### Cons
- Modification to our timestamping process
- Extra cost in fees.
- More space taken in OP_RETURN. If it gets cut back to 40 bytes our transactions would get rejected.

## References
- [Trusted Timestamping](https://en.wikipedia.org/wiki/Trusted_timestamping)
- [OpenTimeStamps](https://opentimestamps.org/)
- [How OpenTimestamps Works — Notaries and Time Attestations](https://petertodd.org/2016/opentimestamps-announcement#notaries-and-time-attestations)
- [How OpenTimestamps Works — Scalability Through Aggregation](https://petertodd.org/2016/opentimestamps-announcement#scalability-through-aggregation)
- [ChainPoint's whitepaper](https://github.com/chainpoint/whitepaper/blob/master/chainpoint_white_paper.pdf)
- [Merkle Tree](https://en.wikipedia.org/wiki/Merkle_tree)
- [Patricia Tree](https://github.com/ethereum/wiki/wiki/Patricia-Tree)
- [Trie, Merkle, Patricia: A Blockchain Story](https://hackernoon.com/trie-merkle-patricia-a-blockchain-story-d8f20efc98d4)
- [RIPEMD160](https://en.bitcoin.it/wiki/RIPEMD-160)
