# Separate File Uploads

## Related Issues
- https://github.com/poetapp/random/issues/98
- https://github.com/poetapp/node/issues/260

## Possible Conflicts

## Goals
 * Add the ability for users to hash and create claims without storing the files on our IPFS nodes.
 * Enable both node and frost-api to accommodate this use case.
 * Avoid breaking frost-api.

## Steps

Each step can be 1 or more pull requests.

---- 

### Adjust `Work` claim ( completed )

In poet-js we need to add adjust the verifiable claims of type `Work` to contain `archiveUrl` and `hash` instead of `content`.

See: [Creative Work](https://github.com/poetapp/random/blob/master/claim-types/creative-work.md).

Completed: https://github.com/poetapp/poet-js/pull/150

----

### Add file upload endpoint to the node

Create an API endpoint that allows files to be uploaded directly to IPFS. This endpoint will return an IPFS hash. It may also return an `archiveUrl` if it make sense to do so. The node should store the IPFS hash in the database.

The endpoint needs to support all types of file formats: audio, video, image, and text. More research will need to be done on this sepecifically for its own PR.

---- 

### Update node's `/works` endpoint

Update the node's poet-js dependency to allow the new version of signed verifiable `Work` and `Identity` claims.

The endpoint should validate the signed verifiable claims.

The node should reject signed verifiable Work claims where the Work claim's `archiveUrl` does not match `hash`.

---- 

### Adjust frost-api to hide changes of the node

The node will now require signed verifiable claims. Signed verifiable claims have a few requirements that frost-api will need to be adjusted to handle. The goal is to leave the frost-api untouched, and automate the transition behind the scenes to these new signed verifiable claims.

#### Signed Verifiable Claim Requirements

Once these requirements are met we can send the signed verifiable claim to the node.

##### Issuer

Frost will need to provide an `issuer` property. The issuer will be created from frost-api's privateKey that is related to [Frosts Identity Claim](#frost-identity-claim). poet-js provides the [createIssuerFromPrivateKey](https://github.com/poetapp/poet-js/blob/master/src/util/KeyHelper.ts#L106) function to create an issuer from a privateKey.

##### Author

https://github.com/poetapp/poet-js/blob/master/src/Interfaces.ts#L66

Frost will need to provide an `author` property that now resolves to an author. This means we need to [generate default Identities claims](#generating-default-user-identity) for users to avoid breaking changes.

`author` will be a url to the users default Identity claim.

##### archiveUrl & hash

Replace `content` with `archiveUrl` & `hash` by uploading the value of content to ipfs and using the resulting hash from ipfs. `archiveUrl` for now will just be the ipfs url of the file: `ipfs.io/ipfs/{HASH}`

`content` will become a reserved property of claims for frost-api in order to maintain backwards compatible. Content being a reserved property will not respected by the node, the node does not need to know anything about content being reserved for frost-api backwards compatibility.

##### Signing

Frost will need to sign the verifiable claim it creates with its privateKey that is related to [Frosts Identity Claim](#frost-identity-claim). poet-js provides the [configureSignVerifiableClaim](https://github.com/poetapp/poet-js/blob/master/src/VerifiableClaimSigner.ts#L48) function to sign claims.

----

### adjust frost-api to allow separate file uploads ( future )

At a later point the frost-api will also need to support uploading of files separate from the claim itself.

In order to accommodate the use case and avoid breaking the Frost API we need frost-api to handle both the `content` version and the `hash` and `archiveUrl` version of Work claims.

At that time if the user wants us to store the file for them in IPFS:
  * They will use the `content` property, and
  * The frost-api will upload the value of the content property to the node and create a claim with the `hash` and `archiveUrl`.
* If the user wants to handle the storage of the file himself (whether in IPFS or some other file storage system):
  * They will just provide the `hash` and `archiveUrl` properties.


---- 

### Explorer Web

Explorer web may need to be updated to show any properties a claim contains instead of fixed properties.

----

## Frost Public/Private Key

... details of the frost public and private key 

## Frost Identity Claim

... details of the frost identity claim

## Generating Default User Identity

In order to prevent breaking changes, we will need to provide users with a default Identity Claim. These default identity claims will contain no information except for their api token as a way to link their identity to their frost-api account.

Because we already have users, we will need to generate default Identity claims for existing users and generate default Identity claims at user sign up. More research will need to be done into these things, because generating a default Identity claim is easy enough, but we need to post it the node 


... details of default user identity generation for new and current users.

### Current Users

### New Users





