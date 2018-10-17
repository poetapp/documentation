# Updating to signed verifable claims and separate file uploads

## Related Issues
- https://github.com/poetapp/random/issues/98
- https://github.com/poetapp/node/issues/260

## Possible Conflicts

## Goals

* Add the ability for users to hash and create claims without storing the files on our IPFS nodes.
* Enable both node and frost-api to accommodate this use case.
* Avoid breaking frost-api.
* Update to new signed verifiable claims
 
## Out of scope

* Frost Identity Claim
* User Identity Claims
* User Identity Mangement
* User Private/Public Key Management

## Steps

Each step can be 1 or more pull requests.

---- 

### poet-js: adjust `Work` claim ( completed )

In poet-js we need to add adjust the verifiable claims of type `Work` to contain `archiveUrl` and `hash` instead of `content`.

See: [Creative Work](https://github.com/poetapp/random/blob/master/claim-types/creative-work.md).

Completed: https://github.com/poetapp/poet-js/pull/150

----

### node: add file upload endpoint to the node

Create an API endpoint that allows files to be uploaded directly to IPFS. This endpoint will return an IPFS hash. It may also return an `archiveUrl` if it make sense to do so. The node should store the IPFS hash in the database.

The endpoint needs to support all types of file formats: audio, video, image, and text. More research will need to be done on this sepecifically for its own PR.

For debugging purposes it would be good to store the resulting hash in the mongo db.

---- 

### node: update to new poet-js

Update the node's poet-js dependency to allow the new version of signed verifiable `Work` and `Identity` claims.

The endpoint `/works` should validate the signed verifiable claims.

----

### node: add archiveUrl/hash validation

The node should reject signed verifiable Work claims where the Work claim's `archiveUrl` does not match `hash`.

This should be probably be async as the file we need to download from archiveUrl may be large.

---- 

### frost-api: hide node changes

The node will now require signed verifiable claims. Signed verifiable claims have a few requirements that frost-api will need to be adjusted to handle. The goal is to leave the API exposed by Frost untouched, and automate the transition behind the scenes to these new signed verifiable claims.

#### Signed Verifiable Claim Requirements

Once these requirements are met we can send the signed verifiable claim to the node.

##### Issuer

Frost will need to provide an `issuer` property that is a uri which resolves to an issuer. `issuer` will be a data uri until we have Identity Claim functionality. The `issuer` data uri will be generated from frost-apis privateKey using poet-js's [createIssuerFromPrivateKey](https://github.com/poetapp/poet-js/blob/master/src/util/KeyHelper.ts#L106) function.

> Note: despite the name of the function, no data about the actual private key is leaked in the data url. A public key is generated from it instead.

In the future `issuer` will be a uri that resolves to an Identity Claim.

##### Author

https://github.com/poetapp/poet-js/blob/master/src/Interfaces.ts#L66

Frost will need to provide an `author` property that is a uri which resolves to an author. `author` will be a data uri until we have Identity Claim functionality. The `author` data uri will be generated from the users privateKey using poet-js's `createAuthorFromPrivateKey` ( yet to be created ).

Previous claims took an `author` property which was the authors name, for backwards compatability we will take the provided `author` will become a `name` property of the generated data uri so that generated data uri acts like an Identity claim.

##### archiveUrl & hash

Replace `content` with `archiveUrl` & `hash` by uploading the value of content to ipfs and using the resulting hash from ipfs. `archiveUrl` for now will just be the ipfs url of the file: `ipfs.io/ipfs/{HASH}`

`content` will become a reserved property of claims for frost-api in order to maintain backwards compatible. Content being a reserved property will not respected by the node, the node does not need to know anything about content being reserved for frost-api backwards compatibility.

##### Signing

Frost will need to sign the verifiable claim it creates with its privateKey that is related to [Frosts Identity Claim](#frost-identity-claim). poet-js provides the [configureSignVerifiableClaim](https://github.com/poetapp/poet-js/blob/master/src/VerifiableClaimSigner.ts#L48) function to sign claims.

----

### frost-api: ( future ) add ability to upload files separately 

At a later point the frost-api will also need to support uploading of files separate from the claim itself.

In order to accommodate the use case and avoid breaking the Frost API we need frost-api to handle both the `content` version and the `hash` and `archiveUrl` version of Work claims.

At that time if the user wants us to store the file for them in IPFS:
* They will use the `content` property, and
  * The frost-api will upload the value of the content property to the node and create a claim with the `hash` and `archiveUrl`.
* If the user wants to handle the storage of the file themselves (whether in IPFS or some other file storage system):
  * They will just provide the `hash` and `archiveUrl` properties.


---- 

### explorer-web

Explorer web may need to be updated to show any properties a claim contains instead of fixed properties.

----

## Frost Identity Public/Private Key

The key pair will be stored in the vault. If the key pair is not loaded into the app correctly the app should throw/crash.

## Frost User Default Public/Private Key

User key pairs will be stored in the vault.

### Generating public and private key for new users
We can generate the users key pair at new account creation time.

### Generating public and private key for current users
When a user posts a claim, we load their private key, if one does not exist we generate one and save it.
