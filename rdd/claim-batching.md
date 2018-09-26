# Claim Batching

Instead of timestamping individual claims as they come into the node, we want to group them and timestamp a batch of claims only once every `n` minutes.

## Requirements

- Batch `n` claims per transaction
- Each claim must have its own IPFS hash
- Prevent claims from being lost in the batched timestamping process

## Batching with IPFS Directories

IPFS allows multiple files to be wrapped in a directory, which will have a hash of its own.

Directories are immutable, so each time we push a new file to the directory we create a new directory that contains the previous claims and the new claim and a new directory hash.

## Creating a Timestamping Transaction

As claims come in, we push them to IPFS and get a hash back. Then we add each claim hash to the database. 

After `n` minutes, we grab all of the waiting claim hashes from the database and push them to an IPFS directory and timestamp the directory hash onto the blockchain.

### Flow

- When a claim comes into the node, we push it immediately to IPFS and get a hash back.
- We store the claim IPFS hash in the database in a collection of 'claims awaiting timestamping'.
- When its time to create a timestamping transaction, we grab all of the available claim hashes and add them to a directory synchronously using promises or something similar.
- We then create a bitcoin transaction with the resulting directory hash, which is ultimately written to the blockchain.
- After confirmation of the blockchain transaction, we remove or mark as completed in the 'claims awaiting timestamping' collection the claims we just wrote to an IPFS directory.

### Benefits

- Claims can continuously flow in and be added to the 'claims awaiting timestamping' collection.
- Claims are immediately stored in IPFS.
- At the moment of timestamping, we are only linking claim hashes to the directory, not doing all the work of loading each full claim into IPFS.
- If an error occurs or an accidental or purposeful shutdown of the node happens, claims hashes remain in the database until a successful blockchain transaction occurs.
- We don't have timing issues where two claims may be added to the directory at one time.

## Syncing Nodes

In order to sync nodes, we will need to adjust the process slightly for directories.

In the current code, the node scans each block for Poet transactions. Each transaction has a hash that relates to an individual claim. The only difference now is that the hash may be a directory instead of an individual claim. 

We can either handle this by a version bump (new version is always directory, old version is always single file) or simply ignore transactions that don't point to directories (thus dropping all previous data).

### Handling a file

If the hash is for an individual claim, we use the current flow just like now.

### Handling a directory

If the hash is a directory, we simply grab all the claim hashes from it and add them to the DB entry collection via the `Storage/ClaimControllers/download` method to be downloaded individually. 

We then mark the directory's entry as complete so we don't keep retrying.

## Testing

_**QUESTION:** How can we use automated testing to verify that this works and continues to work?_

Note by Lautaro: testing that syncing of nodes works well would cover this. 
