# Registries

There are two types of registries, plus a subtype, which provide different functionalities.

## Anchor Registry

DT defines this as a registry that is an object registry with anchoring capabilities, basically the special case of using colored coins in bitcoin, AFAICT. 

But if we instead define this as a registry that provides anchoring only, and define bitcoin colored coins as a _broadcast registry_, I feel everything becomes more intuitive, easier to understand and implement.

In this document I follow my definition over DT's. Consensus TBD.

Note: _anchoring_ is also known as _timestamping_ in the cryptography world. See [OTS](https://opentimestamps.org/) for example.

Anchor registries MUST run on an immutable, secure distributed ledger such as Bitcoin or Ethereum. Bitcoin is our recommendation for anchoring due to its overall stability and accumulated PoW, making it more secure than others.

### Broadcast Registry

If using Bitcoin with the `OP_RETURN` op, no prefix is needed as the transaction is only there to serve as a timestamp or anchor, but if a prefix is given it then becomes a _broadcast registry_. In other words, broadcast registries are colored coins.

If using smart contracts, they must implement the Broadcast Registry smart contract interface, which allows unrestricted, unauthenticated addition.

Broadcast registries are permissionless and unrestricted, as anyone can add entries to them. 

This is the only type of registry that has been supported by Po.et up to now.

## Object Registry

Object registries are lists in which each entry is a group of claims about a single subject and their anchor receipts. 

Anchor receipts are JSON files with all the information needed to locate the claim's anchor in an anchor or broadcast registry.

In practice each entry is an IPFS directory that itself has a collection of IPFS directories inside, each having two IPFS files: the claim and its anchor receipt.

Object registries can be smart contracts, offchain databases and even plain files.

TBD: _Curation Registry_ seems like a more accurate name for this description.

## Node Review

Things the node does or should:
- Allow users to submit a verifiable claim for anchoring.
  - Once anchored, save the anchor receipt automatically in IPFS?
- Allow users to submit one or several verifiable claims, along with their anchor receipts, as a bundle to an object registry.
  - All claims in an entry should have the same `about`, but this won't be implemented at node level due to complexity and need of better definition. Implement only in Explorer.
  - Allow users to submit claims and/or anchor receipts that are not known to the node. Verifications can be added later and on reading/listening side.
- Expose a view of all claims known to the node.
  - Claims submitted to the node to any kind of registry.
  - Claims downloaded from object and broadcast registries the node follows.

## Implementation

`POST /registries/:registryId/claims` dispatches to the appropriate microservices that know how to deal with the registry. It can be an anchor, broadcast or object registry. If it is an anchor or broadcast registry, the submitted claim gets anchored. If it is an object registry, it gets added to the object registry. 

`registryId` could be the hash of the arguments of the registry. 

`GET /claims` returns claims from every registry. `GET /registries/:registryId/claims` is equal to `GET /claims?registryId=:registryId`. `POST /claims` not supported.

## Challenge: Choosing Registries?

A node could be configure to follow certain registries. For example, it could be configured to follow the broadcast registry `(bitcoin, prefix=POET)` and the object registry `(ethereum, contractAddress=...)`.

But clients of the node can't choose what registries to follow. Which means Frost users can't choose what registries to follow, all users would follow the same registries, the ones the Po.et Foundation decided. 

The same applies for appending to registries of any kind: since the node needs a private key to access it (be it the private key that has access to a contract or a private key with funds to create a transaction), Frost users can't choose which registries are available to them, they can only choose one of the ones configured by the Po.et Foundation.
