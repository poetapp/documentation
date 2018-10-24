# Updating to Signed Verifiable Claims and Separate File Uploads

## Related Issues
- https://github.com/poetapp/random/issues/98
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
* User Identity Mangement
* User Private/Public Key Management

## Steps

Each step can be 1 or more pull requests.

---- 

### poet-js: Adjust `work` Claim (Completed)

https://github.com/poetapp/poet-js/pull/150

In poet-js we need to add adjust the verifiable claims of type `Work` to contain `archiveUrl` and `hash` instead of `content`.

See: [Creative Work](https://github.com/poetapp/random/blob/master/claim-types/creative-work.md).

----

### node: Add File Upload Endpoint to the Node

https://github.com/poetapp/node/issues/611

Create an API endpoint that allows files to be uploaded directly to IPFS. This endpoint will return an IPFS hash. It may also return an `archiveUrl` if it make sense to do so. The node should store the IPFS hash in the database.

The endpoint needs to support all types of file formats: audio, video, image, and text. More research will need to be done on this sepecifically for its own PR.

For debugging purposes it would be good to store the resulting hash in the mongo db.

---- 

### node: Update to New poet-js (Completed)

https://github.com/poetapp/random/issues/197

Update the node's poet-js dependency to allow the new version of signed verifiable `Work` and `Identity` claims.

The endpoint `/works` should validate the signed verifiable claims.

> Q: Should this validate only on POST or on retrieval as well?

---- 

### frost-api: Hide Node Changes

The node will now require signed verifiable claims. Signed verifiable claims have a few requirements that frost-api will need to be adjusted to handle. The goal is to leave the API exposed by Frost untouched, and automate the transition behind the scenes to these new signed verifiable claims.

#### Signed Verifiable Claim Requirements

Once these requirements are met we can send the signed verifiable claim to the node.

##### Issuer

Frost will need to provide an `issuer` property that is a uri which resolves to an issuer.  The `issuer` of a Work claim 
is the account/profile that submits the claim. `issuer` will be a data uri until we have Identity Claim functionality. 
Currently since we are signing with ED25519, we will need to generate an ED25519 publicKey/privateKey pair for each account.

The `issuer` data uri will be generated from the submitter's privateKey using poet-js's [createIssuerFromPrivateKey](https://github.com/poetapp/poet-js/blob/master/src/util/KeyHelper.ts#L106) function.

> Note: despite the name of the function, no data about the actual private key is leaked in the data url. 
A public key is generated from it instead, and that is stored in the data url, along with the information required to 
resolve the verification.

In the future `issuer` will be a uri that resolves to an Identity Claim.

##### Author

https://github.com/poetapp/poet-js/blob/master/src/Interfaces.ts#L66

https://schema.org/author

https://github.com/poetapp/poet-js/issues/197

Frost will need to provide an `author` property that is a uri which resolves to an author. If the submitted work claim 
does not designate a url for `author`, frost will generate a data uri based on the string value of `author`.

> I do not think author should ever resolve to an identity claim unless the issuer submits the value. We should minimize 
modifying the `claim` portion of a SignedVerifiableClaim as much as possible.

Previous claims took an `author` property which was the authors name, for backwards compatability we will take the provided
 `author` will become a `name` property of the generated data uri so that generated data uri acts like a `schema.org/author`.

##### `archiveUrl` & `hash`

Replace `content` with `archiveUrl` & `hash` by uploading the value of content to ipfs and using the resulting hash from ipfs. `archiveUrl` for now will just be the ipfs url of the file: `ipfs.io/ipfs/{HASH}`

`content` will become a reserved property of claims for frost-api in order to maintain backwards compatible. Content being a reserved property will not respected by the node, the node does not need to know anything about content being reserved for frost-api backwards compatibility.

##### Signing

Frost will need to sign any verifiable claim it creates with the issuer's private key that will be eventually be related 
to the issuer's Identity Claim in the future. poet-js provides the [configureSignVerifiableClaim](https://github.com/poetapp/poet-js/blob/master/src/VerifiableClaimSigner.ts#L48) function to sign claims.

----

### frost: Identity Public/Private Key

https://github.com/poetapp/frost-api/issues/527

The key pair for Frost should be generated using [@poet/poetjs:generateED25519Base58Keys](https://github.com/poetapp/poet-js/blob/master/src/util/KeyHelper.ts#L141).

The key pair will be stored in mongo db and will be encrypted/decrypted by the vault. If the key pair is not loaded into the app correctly the app should throw/crash.

### frost: User Default Public/Private Key
https://github.com/poetapp/frost-api/issues/528

User key pairs will be stored in mongo db and will be encrypted/decrtyped by the vault. The key pairs should be generated on
account creation using [@poet/poetjs:generateED25519Base58Keys](https://github.com/poetapp/poet-js/blob/master/src/util/KeyHelper.ts#L141). 
Some sort of solution will need implemented to create key pairs for accounts that are already created.

---- 

### explorer-web

Explorer web may need to be updated to show any properties a claim contains instead of fixed properties.

----

### frost-api: (future) Add Ability to Upload Files Separately 

At a later point the frost-api will also need to support uploading of files separate from the claim itself.

In order to accommodate the use case and avoid breaking the Frost API we need frost-api to handle both the `content` version and the `hash` and `archiveUrl` version of Work claims.

At that time if the user wants us to store the file for them in IPFS:
* They will use the `content` property, and
  * The frost-api will upload the value of the content property to the node and create a claim with the `hash` and `archiveUrl`.
* If the user wants to handle the storage of the file oneself (whether in IPFS or some other file storage system):
  * They will just provide the `hash` and `archiveUrl` properties.

----

### node: (future) Add archiveUrl/Hash Validation

When `archiveUrl` points to an IPFS file, the validation can be skipped entirely since IPFS content is immutable.

For `archiveUrl`s pointing to mutable storage, clients of the node should verify that the hash of the contents dereferenced from `archiveUrl` does match `hash`.

Frost will only support IPFS initially, so there is no need to address this more complex scenario for mainnet.
