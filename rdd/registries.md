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

In practice each entry is an IPFS directory that itself has a collection of IPFS directories inside, each having two IPFS files: the claim and its anchor receipt.

Object registries can be smart contracts, offchain databases and even plain files.

TBD: _Curation Registry_ seems like a more accurate name for this description.

## Node Review

Things the node does or should:
- Allow users to submit a verifiable claim for anchoring.
  - Once anchored, save the anchor receipt automatically in IPFS?
- Allow users to submit one or several verifiable claims, along with their anchor receipts, as a bundle to a registry.
  - All claims in an entry should have the same `about`, but this won't be implemented at node level due to complexity and need of better definition. Implement only in Explorer.
  - Allow users to submit claims and/or anchor receipts that are not known to the node. Verifications can be added later and on reading/listening side.
- Expose a view of all claims known to the node.
  - Claims submitted to the node.
  - Claims downloaded from registries the node follows.

## Implementation

`POST /registries/:registryId/claims` dispatches to the appropriate microservices that know how to deal with the registry. It can be an anchor or an object registry. If the registry is an anchor registry, the submitted claim gets anchored. If it’s an object registry, it gets added to the object registry. 

`registryId` could be the hash of the arguments of the registry. 

`GET /claims` returns claims from every registry. `GET /registries/:registryId/claims` is equal to `GET /claims?registryId=:registryId`. `POST /claims` not supported.

`GET /claims` problem: same claim will exist in different registries, of different types. How do we render a claim that has been added to an anchor and to an object registry?

TODO: entries of an anchor registry are different from entries of an object registry.
