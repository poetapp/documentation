# Po.et File API

> The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED",  "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](https://www.ietf.org/rfc/rfc2119.txt).


Po.et is the decentralized protocol suite for content *ownership,* *discovery,* and *monetization*. The Po.et File API is a facade layer to provide protocol-level extensibility to swap out the archive and claim storage layers with alternative file system implementations.


### Goals

* File system extensibility (the ability to support any file system for which an adapter exists)
* Support for entirely private, proprietary storage mechanisms
* Extensibility for file system authentication and access controls

### Data Privacy on Po.et

Data privacy is an important issue for content creators, publishers, and users of the Po.et network. Po.et plans to support data privacy in a number of ways.

##### Personally Identifiable Information

Initially, Po.et only supports immutable, permanent, public claim storage on IPFS. Such immutable, permanent storage solutions should not be used to store personally identifying information.

For privacy reasons, identity claims on the Po.et Network do not contain any personally identifiable information, such as names, addresses, birth dates, etc. Claims about such data can be made and verified, but the data itself is stored outside public, immutable storage by decentralized Identity Providers (IDPs) such as the Frost API. Personally identifying profile data is optional and user-controlled. It can be modified or deleted directly by the user at any time.

In the future, Po.et may support more private claim data types via similar mechanisms. Such claim data will be verifiable because data will still be cryptographically hashed, signed, and anchored to the blockchain.

Claims about mutable data may be invalidated if the data is changed. For example, an age verification service may provide evidence that the user is over 21 years old, which makes the user eligible to order alcohol. If the user later corrects their birthdate, that eligibility claim may be invalidated.

##### Creative Work Privacy

Creative works are referenced by URL, so the Po.et protocol should support the use of any URL addressable storage solution which meets the minimum requirements.

It should be possible for partners to host their own creative work files without exposing them to the threat of early leaks or theft.

> Warning: Hosting creative works outside the Po.et-supported defaults incurs significant risks to future claim validity:
>
> * Creative work storage must be permanent for the life of the work claim.
> * Creative work files should be stored in immutable storage, and never changed.  A change of even a single byte will completely invalidate a creative work claim. Any file changes should be stored as new, separate files and recorded in Po.et as revisions, or alternate file formats.
> * URL addressing must be permanent. If a domain name expires or a file gets moved, all related claims will be irreversibly invalidated.
>
> Reliance on centralized services (including the domain name system), or vendor specific file locations (such as AWS S3 buckets) could lead to irreversible loss of claim verifications if those systems are ever changed, or if their services fail to continue working.

### FILE API Motivation

The Po.et protocol suite MUST support immutable persistence in order to facilitate each of these goals:

* Ownership
* Discovery
* Reputation
* Monetization
* Privacy & Access Control

**Ownership** — (Attribution) Po.et works by hashing creative works and anchoring those hashes to the blockchain, which forms a permanent, immutable record for the purpose of attribution evidence. By hashing a work to the blockchain, a content owner can prove that they had possession of a creative work at a specific date in time: a valuable piece of evidence in a court case.

In order to prove attribution, the user must have access to a perfect bit-for-bit copy of the file that was originally hashed, which means that the file must be stored in a permanent, immutable archive to prevent loss of archival evidence. Without that archive, the Po.et protocol cannot prove attribution automatically.

Additionally, the claims themselves must be stored in a permanent, immutable, high-availability archive storage system.

**Discovery** — In order to make content discoverable, the Po.et protocols need access to the archived file in order to index it and generate metadata for it. Much of that work may involve making the file contents searchable. Prior to Google, many search engines based searches entirely on the headers and metadata associated with web pages. Google indexed the full text of every web page, allowing Google to produce much better search results than the competition, which helped it rise to dominance and ubiquity. Without the permanent, immutable archive, Po.et protocols can’t allow full content indexing of creative works on the Po.et network.

**Reputation** — Po.et allows claims about content (attestations, notarizations, reputation staking). Without access to the file, the Po.et protocol layer can’t prove that the person or organization making an attestation about a creative work has ever had access to the work, and is thus qualified to make an attestation about its contents. With blockchain proof of file access, we can add evidence that the user knows what they’re talking about, similar to the verified buyer check in Amazon reviews.

**Monetization** — In a licensing marketplace, it’s generally considered a core feature to allow licensees access to the files being licensed. Without protocol support for file addressing, each marketplace will have to implement that feature in a proprietary way, and there would be no automatically provable way that the content they are licensing is actually the content hashed on the Po.et network.

**Privacy & Access Control** — Standard support for file features including privacy, encryption, deletion, and so on are made easier if extension points for proprietary systems are built into the protocol and claims layers because they provide a standard API that privately implemented file storage solutions (such as private enterprise intranet systems) can plug into to ensure interoperability with other software and libraries designed to interface with the Po.et network.

In other words, it allows the Po.et protocols and software to officially support private file storage schemes including offline or gated file archives. The alternative is that organizations working to create such systems would be left to reinvent the wheel, and each independently invented wheel would be incompatible with the next. Users would frequently run into cases where they want to use software designed to work with Po.et that does not work with their proprietary implementation because there was no standard file storage API built into the claims and protocols.


### Storage Implementation Agnostic

Each Po.et Node MUST support a single standard API for file persistence designed to work across a variety of underlying persistence mechanisms, including private/third party persistence implementations.

A standard protocol-level API allows the protocols and software using the protocols to be agnostic about the details of the storage layer implementation. Po.et Nodes should not assume that all files are stored on IPFS, Filecoin, Storj, or any other specific implementation. Instead, files are addressed by URLs which specify the protocol used to access the file.

Users of Po.et Nodes should be able to configure the persistence mechanism they want to use to store their own files. For example, content creators may want to stamp their works in progress without sharing unfinished works with the rest of the world.

With a file API built into the protocol layer, they can do so and add extension points that will enable their private nodes running public-open source software to work with proprietary features built to suit the needs of particular organizations.

### Challenges

Because most of the useful functions of the Po.et protocol suite depend on file persistence to work, files hashed to the Po.et network will need to be stored in permanent, immutable storage.

While it will be possible to offload a fraction of that work to private storage implementations, the bulk of value from the Po.et network will likely come from the publicly accessible files. For example, [scientific research is more valuable when it’s publicly available](https://www.wired.com/2017/04/tearing-sciences-citation-paywall-one-link-time/).

Marketplaces that aggregate Po.et data for discovery and licensing can do so more effectively when more of the content is available for analysis and indexing. A service that aggregates Po.et video could run speech recognition on the audio to make it text-searchable, for instance, but only if the contents of the file are available to the marketplace.

Google could not be Google if it couldn’t index the full contents of every web page without permission. Likewise, Po.et can’t be the Google of content value if it can’t index the full contents of public files on the Po.et network.

Po.et recognizes that there is value in both public and private file storage solutions on the Po.et network. Like the web, Po.et defaults to public file access, but allows authentication and access permissions to be employed where such measures are appropriate.

### File Storage Implementation Requirements

Implementations of the Po.et File Storage API MUST support:

* File uploads of arbitrary size.
* URL-addressable files. The API is protocol agnostic, and depends on the Po.et client understanding how to interface with the protocol. Claims using a protocol the client does not understand can not be validated by the client.
* Durable, immutable file addressing. We strongly recommend using decentralized identifiers for file locators.
* Guaranteed immutability. If even a single bit is changed, the file hash will no longer validate, and its related claims will become invalid.
* Guaranteed availability to the file for the duration of the claim’s active lifespan. If a file can’t be accessed, its related claims can’t be verified, and many protocol interactions related to the file will fail.

In addition, persistence layers MAY optionally support:

* Deletion requests, signed by an identity authorized to request removal (e.g., the same private key that requested the upload)
* Access control (e.g., to allow only authorized users to download a file)
