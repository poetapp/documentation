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

Each step will be its own pull request.

---- 

### Adjust `Work` claim

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

### Adjust frost-api for both use cases

The node will now require signed verifiable claims. Signed verifiable claims have a few requirements that frost-api will need to be adjusted to handle. The goal is to leave the frost-api untouched, and automate the transition behind the scenese to these new signed verifiable claims.

Requirements of signed verifiable claims that need to be addressed by frost api:

#### Issuer

We need to setup frost-api to have its own Identity claim, and public/private keys that pertain to that identity. Frost will then use the private key in url format as the issuer of the verifiable claim.

##### Questions:

* I just realized that issuer being the private key means the private key is not so private, should the issuer be the public key?

#### Signed

The claim now needs to be signed by and Identity claims privateKey.


In order to accommodate the use case and avoid breaking the Frost API we need frost-api to handle both the `content` version and the `hash` and `archiveUrl` version of Work claims.

* content will be a reserved property of claims for backwards compatibility

The first step will be to adjust frost-api to take claims with a content property as it currently does. The content property will be uploaded to ipfs as a file, and then the resulting ipfs hash will be used to generate `archiveUrl` and `hash`. The `content` property will then be removed from the claim.




A verifiable claim will then need to be created. A verifiable claim will require an Identity url. For now a default identity for the account will be used. Identity management features will be added later.


At a later point the frost-api will also need to support uploading of files separate from the claim itself.

At that time if the user wants us to store the file for them in IPFS:
  * They will use the `content` property, and
  * The frost-api will upload the value of the content property to the node and create a claim with the `hash` and `archiveUrl`.
* If the user wants to handle the storage of the file himself (whether in IPFS or some other file storage system):
  * They will just provide the `hash` and `archiveUrl` properties.


---- 

### Explorer Web

Explorer web may need to be updated to show any properties a claim contains instead of fixed properties.
