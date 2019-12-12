# Registries

Things the node does or should:
- Allow users to submit a verifiable claim for anchoring.
  - Once anchored, save the anchor receipt automatically in IPFS?
- Allow users to submit one or several verifiable claims, along with their anchor receipts, as a bundle to a registry.
  - All claims in an entry must have the same `about`.  -> Avoid implementing this at node level due to complexity. Implement only in Explorer.
  - Allow users to submit claims and/or anchor receipts that are not known to the node? -> Yes. Verifications can be added later and on reading/listening side.
- Expose a view of all claims known to the node.
  - Claims submitted to the node.
  - Claims downloaded from registries the node follows.

Idea: 

`POST /registries/:registryId/claims` dispatches to the appropriate microservices that know how to deal with the registry. It can be an anchor or an object registry. If the registry is an anchor registry, the submitted claim gets anchored. If it’s an object registry, it gets added to the object registry. 

`registryId` could be the hash of the arguments of the registry. 

`GET /claims` returns claims from every registry. `GET /registries/:registryId/claims` is equal to `GET /claims?registryId=:registryId`. `POST /claims` not supported.

`GET /claims` problem: same claim will exist in different registries, of different types. How do we render a claim that has been added to an anchor and to an object registry?
