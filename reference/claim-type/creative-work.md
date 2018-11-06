# Creative Work Claim Type

The Creative Work claim type is a cornerstone of the Po.et claims ecosystem. Most other claims in the ecosystem will reference a creative work.

The purpose of the Creative Work claim type is to anchor a particular creative work to the blockchain and (optionally) claim intellectual property rights to the work.

### Properties

* `hash`: Base58-encoded [MultiHash](https://github.com/multiformats/multihash) String - A one-way, deterministic cryptographic hash of the file contents.
* `archiveUrl`: URL String - The archive file location. Note: Files must be stored without changes for the life of the Creative Work Claim. Any alteration of a file could lead Po.et nodes to reject or ignore associated claims. `archiveUrl` downtime can also lead to claims being temporarily unverifiable.
* `name`?: String - The name of the creative work.
* `author`?: URI - A reference to the author of the creative work.
* `contributors`?: Array of URIs - References to contributors.
* `tags`?: Array of Strings - Indexable keywords related to the work.
* `copyrightHolder`?: URI - A reference to the copyright holder identity (a Person or Organization).
* `license`?: URI - A reference to license terms granted to users of the creative work.
* `dateCreated`?: ISO 8601 String - The date the work was created on.
* `datePublished`?: ISO 8601 String - The date the work was published on.
* `canonicalUrl`?: URL String - An "official" location to be used as a homepage for the Creative Work in links or citations.

### Example

```js
"claim": {
  "name": "The Murders in the Rue Morgue",
  "author": "po.et//claims/<Edgar Allen Poe's identity claim>",
  "license": "https://creativecommons.org/publicdomain/mark/1.0/",
  "tags": ["short story", "detective story", "detective"],
  "dateCreated": "1841-01-01T00:00:00.000Z",
  "datePublished": "1841-01-01T00:00:00.000Z",
  "archiveUrl": "po.et://archives/ipfs/<ipfs hash>",
  "canonicalUrl": "http://xroads.virginia.edu/~hyper/poe/murders.html",
  "hash": "<file hash>"
}
```

### References

* [Claims](../claims.md)
* [Schema.org/CreativeWork](https://schema.org/CreativeWork)
