# Separate File Uploads

## Related Issues
- https://github.com/poetapp/random/issues/98
- https://github.com/poetapp/node/issues/260

## Possible Conflicts

## Goals
 * Add the ability for users to hash and create claims without storing the files on our IPFS nodes.
 * Enable both node and frost-api to accommodate this use case.
 * Avoid breaking frost-api.

## Claim Structure

This will continue the transformation of the `Work` claim towards the new [Creative Work](https://github.com/poetapp/random/blob/master/claim-types/creative-work.md).

Claims of verifiable claims will no longer have content/text; it will be replaced with archiveUrl and hash of the content.

## Steps

Each step will be its own pull request.

---- 

### Adjust `Work` claim

In poet-js we need to adjust the `Work` claim type. We need to add adjust the Context to contain `archiveUrl` and `hash` instead of `text`.

----

### Add file upload endpoint to the node

Create an API endpoint that allows file to be uploaded directly to IPFS. This endpoint will return an IPFS hash. It may also return an `archiveUrl` if it make sense to do so. The node should store the IPFS hash in the database.

The endpoint needs to support all types of file formats: audio, video, image, and text. More research will need to be done on this sepecifically for its own PR.

---- 

### Update node's `/works` endpoint

Update the node's poet-js dependency to allow the new version of `Work` claims.

---- 

### Adjust frost-api for both use cases

In order to accommodate the use case and avoid breaking the Frost API we need frost-api to handle both the `content` version and the `hash` and `archiveUrl` version of works.

* If the user wants us to store the file for them in IPFS:
  * He will use the `content` property, and
  * The frost-api will upload the value of the content property to the node and create a claim with the `hash` and `archiveUrl`.
* If the user wants to handle the storage of the file himself (whether in IPFS or some other file storage system):
  * He will just provide the `hash` and `archiveUrl` properties.


---- 

### Explorer Web

Explorer web may need to be updated to show any properties a claim contains instead of fixed properties.
