# Timestamp Receipts

Timestamp Receipts are used to verify that a given set of data existed at a point in time by checking the hash of the set of data against a blockchain block.

For reference, see OTT's How OpenTimestamps Works — [Notaries and Time Attestations](https://petertodd.org/2016/opentimestamps-announcement#notaries-and-time-attestations) and [Scalability Through Aggregation](https://petertodd.org/2016/opentimestamps-announcement#scalability-through-aggregation). ChainPoint's [whitepaper](https://github.com/chainpoint/whitepaper/blob/master/chainpoint_white_paper.pdf) also contains a section on Timestamp Receipts.

## Related Issues 
- https://github.com/poetapp/node/issues/178
- https://github.com/poetapp/node/issues/112
- https://github.com/poetapp/node/issues/293

## Solution 1 — IPFS Hash as Receipt

We'd need to store the [different variables that IPFS uses to calculate the hash](https://discuss.ipfs.io/t/how-to-calculate-file-directory-hash/777) in the timestamp receipt to be able to validate the timestamp. 

With Claim Batching this would include understanding how the hash of a directory is affected by its content.

See https://github.com/poetapp/node/issues/112 for related issues with generating IPFS hashes without the IPFS binary.

### Pros
- No modification to our timestamping process
- Would need to learn more about IPFS.
- No extra cost in fees.

### Cons
- Would require plenty of research.
- Unknown factor. Can't estimate effort due to research requirement.

## Solution 2 — MERKLE_ROOT(RIPEMD160(SHA256(verifiableClaimId)))

Alternatively we can store the Merkle Root of the verifiable claims included in the verifiable claim batch right along the IPFS Directory Hash in the OP_RETURN data for the receipt to be storage-protocol-independent. 

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

## Conclusion

For a first deliverable, going with Solution 2 is the best option. Solution 1 is an optimization we should keep in mind.

## References
- [Patricia Tree](https://github.com/ethereum/wiki/wiki/Patricia-Tree)
- [Trusted Timestamping](https://en.wikipedia.org/wiki/Trusted_timestamping)
- [OpenTimeStamps](https://opentimestamps.org/)
- [multihash](https://github.com/multiformats/multihash)
- [Trie, Merkle, Patricia: A Blockchain Story](https://hackernoon.com/trie-merkle-patricia-a-blockchain-story-d8f20efc98d4)
