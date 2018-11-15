# Po.et Glossary

### Claim, Verifiable Claim
A **Claim** (or "Po.et Claim") is a collection of data about an Entity on the Po.et Network. Po.et Claims are batched for scalability reasons and anchored to a blockchain, which proves that the Claim existed at the time the block was created. A Claim should not be confused with a fact, and may require additional proof/attestation (i.e., Claims from other trusted Identities) to be trusted. Claims in the Po.et Network ecosystem are Verifiable Claims by default.

### Claim Type
A reference to a schema for a particular type of Claim (e.g., Creative Work or Identity).

### Creative Work
A **Creative Work** is a Po.et Entity representing an original creative work, presumably created by the Entity registering the Creative Work Claim Type on the Po.et Network.

### Creative Work Claim Type
A Claim Type describing a Creative Work. It must contain a hash of the work, an archive URL to access the file, and a digital signature. The archive URL can be any URL. It must be permanent, immutable, and always available to the Po.et Network for verification.

### Entity
The subject of a Po.et Claim. Claims are made about a particular individual or thing (e.g., a license claim). The subject refers to the Entity that the Claim describes. For example, a Creative Work Claim describes a Creative Work Entity. An Identity Claim describes a Person or Organization, and so on. An Entity is the canonical sum of all claims made about it.

### Po.et API
The **Po.et API** is Po.et's official key-custody agent and Identity Provider. It makes it easier to work with Po.et by hiding the complex details of key and identity management from Po.et application developers. In the future, other custody agents and Identity Providers may provide similar services to Po.et Network users.

### Identity
An **Identity** is a Po.et Entity representing a [Person](https://schema.org/Person) or [Organization](https://schema.org/Organization). An Identity must have a public key to verify its Claim signatures. These are provided automatically by the Po.et API.

### Identity Claim Type
A Claim Type describing a Person or Organization. Identity claims do not contain personally identifiable information about Identities. Instead, they refer to an optional profile in the user's control. A profile may be modified, updated, or deleted at any time. For legal compliance and respect of privacy, if a Po.et application discovers that the user's Identity Profile has been updated, it should update all local copies to match, including the deletion of previously supplied profile information.

### Identity Provider
An **Identity Provider** is a service which provides decentralized identity management to users of the Po.et Network. Identity Providers should aim to allow users to selectively share, update, or delete personally identifying information across the Po.et Network ecosystem.

### JSON-LD
**[JSON-LD](https://www.w3.org/TR/json-ld/)** is a JSON-based Serialization for Linked Data used to describe Po.et Claims. Unlike JSON, JSON-LD allows for deterministic canonicalization of claims for verifiable signing, and allows implementors to adapt the claim vocabulary to match various application domains by supplying a `context` property linking to a corresponding schema.

### Key Custody
**Key Custody** refers to managing cryptographic keys for users, giving those users the ability to securely control their digital assets. Key management is one of the hardest problems with decentralized applications, and will require specialized providers who can build and maintain secure key management software and hardware. 

### Organization
Any logical legal entity such as a business, non-profit, government, church, association, NGO, etc.

### Person
Any living/sentient being.
