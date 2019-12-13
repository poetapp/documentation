# Registries

There are two types of registries, which provide different functionalities.

## Anchor Registry

Anchor registries are colored coins. The prefix is optional and can be left empty.

Note: _anchoring_ is also known as _timestamping_ in the cryptography world. See [OTS](https://opentimestamps.org/) for example.

Anchor registries MUST run on an immutable, secure distributed ledger such as Bitcoin or Ethereum. Bitcoin is our recommendation for anchoring due to its overall stability and accumulated PoW, making it more secure than others.

## Object Registry

Object registries are lists in which each entry is a group of claims about a single subject and their anchor receipts. 

Anchor receipts are JSON files with all the information needed to locate the claim's anchor in an anchor or broadcast registry.

In practice each entry is an IPFS directory that itself has a collection of IPFS directories inside, each having two IPFS files: the claim and its anchor receipt.

Object registries can be smart contracts, offchain databases and even plain files.

## Implementation

Offer write access to object registries through Frost. 


