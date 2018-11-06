# Po.et Glossary

### Claim, Verifiable Claim
A Po.et Claim is a collection of data which is cryptographically signed by the issuer (which makes it a Verifiable Claim). Po.et claims are batched for scalability reasons and anchored to a blockchain, which proves that the claim existed at the time the block was created. A claim should not be confused with a fact, and may require additional proof/attestation to be trusted.

### Claim Type
A reference to a schema for a particular type of claim.

### Creative Work Claim Type
A claim type describing a creative work. It must contain a hash of the work, an archive URL to access the file, and a signature. The archive URL can be any URL. It must be present, but access to the file does not have to be public. It can refer to private network attached storage, for instance, or require authentication to download.

### Entity
The subject of a Po.et claim. Claims are made about a particular subject. The subject refers to the entity that the claim describes. For example, a Creative Work Claim describes a Creative Work Entity. An Identity Claim describes a Person or Organization, and so on. An entity is the the canonical sum of all claims made about a particular subject.

### Frost API
The Frost API is Po.et's official key-custody agent and Identity Provider. It makes it easier to work with Po.et by hiding the complex details of key and identity management from Po.et application developers. In the future, other custody agents and identity providers may provide similar services to Po.et Network users.

### Identity
An identity is a Po.et entity representing a [Person](https://schema.org/Person) or [Organization](https://schema.org/Organization). An identity must contain a public key used to verify claim signatures. Identity claims do not contain personally identifiable information about Identities. Instead, they refer to an optional profile in the user's control. A profile may be modified, updated, or deleted at any time. For legal compliance and respect of privacy, if a Po.et application discovers that the user's Identity Profile has been updated, it should update all local copies to match, including the deletion of previously supplied profile information.

### Identity Provider
An identity provider is a service which provides decentralized identity management to users of the Po.et Network. Identity providers allow users to selectively share, update, or delete personally identifying information across the Po.et ecosystem.

### JSON-LD
[JSON-LD](https://www.w3.org/TR/json-ld/) is a JSON-based Serialization for Linked Data used to describe Po.et Claims. Unlike JSON, JSON-LD allows for deterministic canonicalization of claims for verifiable signing, and allows implementors to adapt the claim vocabulary to match various application domains by supplying a `context` property linking to a corresponding schema.

### Key Custody
Key Custody refers to a service provider's ability to manage cryptographic keys for users. Key management is one of the hardest problems with decentralized applications, because many signatures may be required to carry out a simple user workflow, which could cause UX inconvenience for users. Holding private keys also incurs the risk of losing keys, and with them, the ability to control the user's digital assets. Users should never share private keys between different services, because it increases the risk that keys may be stolen. If keys are stolen, ownership of digital assets or funds may be stolen. See also: Frost API.
