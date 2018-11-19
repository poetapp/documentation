# Po.et Glossary

The **Glossary of Confirmed Terms** describes the Po.et Network as it exists today. The **Terms in Development** are work in progress and may be subject to change as the Po.et Protocol is further developed.

## Glossary of Confirmed Terms

### Claim, Verifiable Claim
A **Claim** (or "Po.et Claim") is a collection of data about an Entity on the Po.et Network. Po.et Claims are batched for scalability reasons and anchored to a blockchain, which proves that the Claim existed at the time the block was created. A Claim should not be confused with a fact, and may require additional proof/attestation (i.e., Claims from other trusted Identities) to be trusted. Claims in the Po.et Network ecosystem are Verifiable Claims by default.

### Claim Batching
Claim batching is the process by which many (potentially unlimited) verifiable claims are grouped together into one blockchain transaction. This allows the Po.et network to scale to millions of users while avoiding the transaction limitations of the Bitcoin blockchain. 

### Claim Type
A reference to a schema for a particular type of Claim (e.g., Creative Work or Identity).

### Creative Work
A Po.et Entity representing an original creative work (which could be any digital file such as a text file, a PDF, an image, a video, etc.), presumably created by the Entity registering the Creative Work Claim Type on the Po.et Network.

### Creative Work Claim
A Claim Type describing a Creative Work. It must contain a hash of the work, an archive URL to access the file, and a digital signature. The archive URL can be any URL. It must be present, but access to the file does not have to be public. It can refer to private network-attached storage, for instance, or require authentication to download.

### Entity
The subject of a Po.et Claim. Claims are made about a particular individual or thing (e.g., a license claim). The subject refers to the Entity that the Claim describes. For example, a Creative Work Claim describes a Creative Work Entity. An Identity Claim describes a Person or Organization, and so on. An Entity is the canonical sum of all claims made about it.

### Hash
A hash is a cryptographic operation (e.g., the SHA256 algorithm) that generates a string of numbers from an input file which then acts like a unique fingerprint, identifying the specific creative work file from which it was generated.

### JSON-LD
**[JSON-LD](https://www.w3.org/TR/json-ld/)** is a JSON-based Serialization for Linked Data used to describe Po.et Claims. Unlike JSON, JSON-LD allows for deterministic canonicalization of claims for verifiable signing, and allows implementors to adapt the claim vocabulary to match various application domains by supplying a `context` property linking to a corresponding schema.

### Key Custody
**Key Custody** refers to managing cryptographic keys for users, giving those users the ability to securely control their digital assets. Key management is one of the hardest problems with decentralized applications, and will require specialized providers who can build and maintain secure key management software and hardware. 

### Metadata
Metadata is data that describes data. For example, if you wrote a book, that is the data itself. If you printed off a sheet that stated how many pages your book was, what the genre was and the name of the author and editor, that would be the metadata: data which describes the data set. Metadata about a creative work file on the Po.et Network would include author, time it was submitted, file type, file hash, etc.

### Organization
Any logical legal entity such as a business, non-profit, government, church, association, NGO, etc.

### Person
Any living/sentient being.

### Po.et API
Po.et's official key-custody agent and Identity Provider. It makes it easier to work with Po.et by hiding the complex details of key and identity management from Po.et application developers. In the future, other custody agents and Identity Providers may provide similar services to Po.et Network users.

### Po.et Network
The sum total of all the claims that have been processed according to the Po.et Protocol.

### Po.et Node
A Po.et Node refers to software that receives creative works and produces claims according to the Po.et Protocol.

### Po.et Protocol
The Po.et Protocol is a [metadata schema](https://en.wikipedia.org/wiki/Metadata_standard) along with methods for storing, retrieving and verifying metadata and its related data. Metadata is secondary information that describes the main information and gives it context.

### Proof of Existence
Storing the hash of a creative work file on the blockchain so it can be referenced later, proving the existence of that file at a specific point in time.

## Terms in Development

### Identity
An **Identity** is a Po.et Entity representing a [Person](https://schema.org/Person) or [Organization](https://schema.org/Organization). An Identity must have a public key to verify its claim signatures. These are provided automatically by the Po.et API.

### Identity Claim
A Claim Type describing a Person or Organization. Identity claims do not contain personally identifiable information about Identities. Instead, they refer to an optional profile in the user's control. A profile may be modified, updated, or deleted at any time. For legal compliance and respect of privacy, if a Po.et application discovers that the user's Identity Profile has been updated, it should update all local copies to match, including the deletion of previously supplied profile information.

### Identity Provider
An **Identity Provider** is a service which provides decentralized identity management to users of the Po.et Network. Identity Providers should aim to allow users to selectively share, update, or delete personally identifying information across the Po.et Network ecosystem.

### Reputation
Built on claims and validation, reputation is at the core of the Po.et Network and the concept of Web 3.0. Reputation represents a collection of signals that reflect an author’s true ranking. An author of a creative work is directly linked to claims about that work which can be verified via the Po.et Protocol.
