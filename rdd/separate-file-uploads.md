# Updating to Signed Verifiable Claims and Separate File Uploads

## Related Issues
- https://github.com/poetapp/node/issues/260

## Possible Conflicts

## Goals

* Add the ability for users to hash and create claims without storing the files on our IPFS nodes.
* Enable both node and frost-api to accommodate this use case.
* Avoid breaking frost-api public-facing API (breaking node's API is OK)
* Update to new signed verifiable claims

## Out of scope

* Frost Identity Claim
* User Identity Claims
* User Identity Management
* User Private/Public Key Management

## Steps

Each step can be one or more pull requests.

---- 

### poet-js: Adjust `work` Claim (Completed)

https://github.com/poetapp/poet-js/pull/150

In poet-js we need to add adjust the verifiable claims of type `Work` to contain `archiveUrl` and `hash` instead of `content`.

See: [Creative Work](https://github.com/poetapp/random/blob/master/claim-types/creative-work.md).

----

### node: Add File Upload Endpoint to the Node

https://github.com/poetapp/node/issues/611

Create an API endpoint that allows files to be uploaded directly to IPFS. This endpoint will return an IPFS hash. It may also return an `archiveUrl` if it make sense to do so. The node should store the IPFS hash in the database.

The endpoint needs to support all types of file formats: audio, video, image, and text. More research will need to be done on this specifically for its own PR.

For debugging purposes it would be good to store the resulting hash in the mongo db.

---- 

### node: Update to New poet-js

https://github.com/poetapp/random/issues/197

Update the node's poet-js dependency to allow the new version of signed verifiable `Work` and `Identity` claims.

The endpoint `/works` should validate the signed verifiable claims.

---- 

### frost-api: Hide Node Changes

https://github.com/poetapp/frost-api/issues/521

The node will now require signed verifiable claims. Signed verifiable claims have a few requirements that frost-api will need to be adjusted to handle. The goal is to remain compatible with the existing Frost API exposed to users, and automate the transition behind the scenes to these new signed verifiable claims.

#### Signed Verifiable Claim Requirements

Once these requirements are met we can send the signed verifiable claims to the node.

##### Issuer

Frost will need to provide an `issuer` property that is a URI which resolves to an issuer. `issuer` will be a data URI until we have Identity Claim functionality. The `issuer` data URI will be generated from [frost-api's private key](#frost-identity-publicprivate-key) using poet-js's [createIssuerFromPrivateKey](https://github.com/poetapp/poet-js/blob/master/src/util/KeyHelper.ts#L106) function.

> Note: despite the name of the function, no data about the actual private key is leaked in the data URL. A public key is generated from it instead.

In the future `issuer` will be a URI that resolves to an Identity Claim.

##### Author

https://github.com/poetapp/poet-js/blob/master/src/Interfaces.ts#L66

Frost will need to provide an `author` property that is a URI which resolves to an author. `author` will be a data URI until we have Identity Claim functionality. The `author` data URI will be generated from the [user's private key](#frost-user-default-publicprivate-key) using poet-js's `createAuthorFromPrivateKey` (yet to be created; see ticket below).

https://github.com/poetapp/poet-js/issues/197

Previous claims took an `author` property which was the author's name. For backwards compatibility the provided `author` will become a `name` property of the generated data URI so that generated data URI acts like an Identity claim.

##### `archiveUrl` & `hash`

Replace `content` with `archiveUrl` & `hash` by uploading the value of content to IPFS and using the resulting hash from IPFS. `archiveUrl` for now will just be the IPFS url of the file: `ipfs.io/ipfs/{HASH}`

`content` will become a reserved property of claims for frost-api in order to maintain backwards compatibility. Content being a reserved property will not be respected by the node; it does not need to know anything about `content` being reserved for frost-api backwards compatibility.

##### Signing

Frost will need to sign the verifiable claim it creates with its [private key](#frost-identity-publicprivate-key), which will be related to Frost's Identity Claim in the future. poet-js provides the [configureSignVerifiableClaim](https://github.com/poetapp/poet-js/blob/master/src/VerifiableClaimSigner.ts#L48) function to sign claims.

----

### frost: Identity Public/Private Key

https://github.com/poetapp/frost-api/issues/527

The key pair will be stored in mongo db and will be encrypted/decrypted by the vault. If the key pair is not loaded into the app correctly the app should throw/crash.

----

### frost: User Default Public/Private Key

https://github.com/poetapp/frost-api/issues/528

User key pairs will be stored in mongo db and will be encrypted/decrtyped by the vault. Key pairs should be generated on account creation. A solution will need to be implemented to create key pairs for existing accounts.

---- 

### explorer-web: Handle Claim Properties Dynamically

https://github.com/poetapp/explorer-web/issues/365

Explorer Web may need to be updated to show any properties a claim contains instead of fixed properties.

----

### frost-api: (future) Add Ability to Upload Files Separately 

At a later point the frost-api will also need to support uploading of files separate from the claim itself.

In order to accommodate the use case and avoid breaking the Frost API we need frost-api to handle both the `content` version and the `archiveUrl`/`hash` version of Work claims.

If the user wants us to store the file for them in IPFS:
* They will use the `content` property, and
* The frost-api will upload the value of the content property to the node and create a claim with the `archiveUrl` and `hash`.

If the user wants to handle the storage of the file oneself (whether in IPFS or some other file storage system):
  * They will just provide the `archiveUrl` and `hash` properties.

----

### node: (future) Add archiveUrl/Hash Validation

When the `archiveUrl` points to an IPFS file, the validation can be skipped entirely since IPFS content is immutable.

For `archiveUrl`s pointing to mutable storage, clients of the node should verify that the hash of the contents dereferenced from `archiveUrl` does match `hash`.

Frost API will only support IPFS initially, so there is no need to address this more complex scenario for the initial mainnet release.
