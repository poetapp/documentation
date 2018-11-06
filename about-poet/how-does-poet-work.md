# How Does Po.et Work?

Po.et is building the verifiable web: the decentralized protocol suite for content attribution, discovery, monetization and reputation.

Using digital signatures and blockchain technology, Po.et empowers content creators to sign, permanently record, and timestamp [claims](glossary.md#claims) about their content. Those claims, taken together, form something like a nutritional label for media content, which helps consumers and publishers discover and reward the content that best fits their needs.

To deliver the verifiable web to content creators and consumers, Po.et leverages four important technologies:
- Content hashing
- Digital signatures
- Decentralized storage
- Blockchain anchoring

### Enabling Technologies

#### Content Hashing

Cryptographic hashing functions provide the means to irrefutably identify content. Given the same input, a hashing function will always produce the same output (the hash), so a content creator can use the hash to identify a work he created. A hash can be generated for a file of any size, but like a fingerprint, the hash output will always be relatively small. The small size makes hashes a convenient and economical substitute for the file contents when all we really care about is uniquely identifying the content.

#### Digital Signatures

Digital signatures enable content creators to prove ownership of a piece of content (a Po.et [Creative Work Claim](glossary.md#creative-work-claim)), or another party's right to license it (a License Claim), or any number of other possible claims. Signatures provide cryptographic proof of who issued a claim. Every claim in Po.et is signed by its issuer, which means a random user can't publish claims on behalf of another user. Claims include the hash of the claim's subject along with other information provided by the claim creator. Combined with content hashing, digital signatures provide cryptographic proof of who said what, and the subject the claim is about.


#### Decentralized Storage

Po.et claims are recorded on IPFS, a peer-to-peer network for distributing content in a decentralized manner. Data on IPFS is stored on multiple computers owned by many different operators. By leveraging this decentralized storage model, the claims are distributed across a wide range of IPFS network participants, making the removal of any of those records extremely difficult.

The claims (IPFS files) are assembled into batches ([IPFS directories](https://discuss.ipfs.io/t/understanding-ipfs-directories/2219)). Each batch is also condensed down into a hash. This batching process enables the Po.et Network to handle a large volume of transactions, without being constrained by the size limit of blocks on the Bitcoin blockchain.

#### Blockchain Anchoring

Po.et leverages the Bitcoin blockchain - the largest and most secure distributed ledger in the world - to ensure that all records across the Po.et Network are provable and irreversible.

Po.et claims are recorded (we use the term “anchored”) on the blockchain by generating a special transaction that contains the hash of the batch to be anchored. Once that transaction is written into the blockchain, all of the claims are enshrined and can be referenced by anyone.

When a Po.et Node downloads blocks from the blockchain, it records for each claim the block number where the batch transaction was recorded. Anyone can verify the date and time that the claim, the associated content's hash, and the claim creator's digital signature on that claim were all recorded on the blockchain. This gives absolute proof about the timing of the claim and conclusively verifies the content itself.

### The Parts of the Po.et Engine

There are several pieces of software developed by Po.et that make up the engine that drives the Po.et Network, enabling the four technologies above to bring the verifiable web to life.

#### Node

The Po.et Node (a.k.a., node) is responsible for receiving new claims, storing them on IPFS, and finally batching them together and anchoring them on the blockchain. The node also responds to queries about claims through an API, providing information about the claim if it has a record of it.

By operating a node, the Po.et Network becomes more decentralized and node operators can have more control over how their content is published to the network. For example, a large content publisher may want to ensure that their content is published consistently and will not want to rely on a third party to maintain a working node. Therefore, they can simply deploy their own node and host it according to whatever standards and availability target they chose.

#### Frost

Frost is an easy to use interface that allows publishers to record their works on the Po.et Network. It is designed to manage the technical details while abstracting away the specialized knowledge necessary for implementing a public key infrastructure.

Frost is composed of two parts:
- An API that enables developers to interact with the Po.et Network programmatically
- A web front-end for creating user accounts and managing the private/public keys and API tokens linked to each account

The web front-end (a.k.a., [Frost Web](https://frost.po.et/)) uses the Frost API to provide a familiar account and API token management system to developers and end users, for example, WordPress bloggers.

#### WordPress Plugin

The [Po.et WordPress Plugin](https://github.com/poetapp/wordpress-plugin) makes it simple for WordPress users to record every blog post they create to the Po.et Network. Once they have created an API token in Frost Web, they simply install the plugin and add their API key, and their blogs are ready to record posts. The plugin interacts with the Frost API hosted by Po.et, so there is no need to host a node or run any other Po.et software.

#### Explorer Web

In order to easily view claims and content on the Po.et Network, [Explorer Web](https://github.com/poetapp/explorer-web) is a web front-end linked to a node that can retrieve and display those claims and works.

### The Future of Po.et

Po.et is still in its infancy, yet there are already the basic building blocks in place of its first layer, which is focused on enabling content creators to record and prove ownership of their works. This base layer is being improved upon with features that will make it more robust and scalable so that the Po.et Network can ultimately be the open platform for the world’s creative content: the verifiable web.

Once the base layer is completed, further innovations will be built on top of it so that content owners and consumers small and large can tap into the immense offering that a global network of content creators will unlock.

[Learn about our file API](poet-file-api.md)
