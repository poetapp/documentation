# Po.et Node Microservices Refactor

It's time to finally break this guy down into proper microservices.

## Background

The current version of Po.et Node was built from the ground up with microservices in mind, but due to the business requirements at the time it was conceived, it was decided for it to be runnable as a single application, for greater ease of use.

At that time we had also decided not to use docker in production.

Maintaining the node as a single application made sense in that context, and the need to fully support microservices never arose.

Now that we have finally deployed a new infrastructure, running on Docker, this hybrid approach in which the Po.et Node behaves like a single application but requires RMQ for modules to communicate with each other is actually hindering  scalability. 

Completing the transition towards proper microservices will allow improved scaling of the Po.et Node. 

Since it is already split in modules which mostly behave as separate applications, this refactor will be mostly around bootstrapping, requiring little to no code change.

## Hybrid Approach

A full blown microservice refactor would mean each module becoming its own, independent application, each with its own `package.json`.

In order to avoid the extra maintenance and development cost this would incur, we are choosing a middle ground, in which all modules still live in the same code base, but live in different, isolated processes at run time.

## Scalability 

With the current half-microservices half-monolith approach, scaling the node is actually more complicated than if it was just a monolith.

Completing the move towards microservices, on the other hand, would allow improved scaling over both models.

For example, to improve throughput of the Po.et Node's API, we could have the API in an auto-scaling group behind an ELB, each instance reading from one read-replica of the database. This would allow production to run 10 instances of the API module if desired, all reading from a different database than the one used by the View module, thus not impacting the performance of other modules at all.

We would also be able to have as many API modules as we want running in parallel but keep BlockchainReader as only one instance, since it's basically a cron that is sleeping 99% of its life time.

Or distribute upload of files to IPFS between different instances of the StorageWriter, even to different instances of IPFS.

## Plan

1. Add `/src/${moduleName}/index.ts` to each module
1. Update package.json
1. Update docker-compose.yml
1. Helpers
1. Interfaces & Other Shared Files

### 1. Add `index.ts` to each module

Each module's `index.ts` will be similar to our current `index.ts`, except that it'll only instantiate that particular module instead of all of them.

This file will also be responsible for loading configuration, unlike our current approach in which `app` takes care of this. 

As the current `index.ts`, each `index.ts` will need to import side-effecting files (those under `Extensions`, for example).

### 2. Update `package.json`

The Node's `package.json` currently has a `bin` section that maps `poet-node` to `dist/babel/src/index.js`.

This is used by NPM at installation time, when running `npm i -g @po.et/node` or `cd node & npm i -g`, to set up a symbolic link that allows running `poet-node` in the shell, the same way we'd run `cat`, `vim`, `bitcoind` or any other program.

We'll want to add several entries to the `bin` section, one for each module, mapping module names to the newly created `module/index.ts` files. For example: `"poet-node-blockchain-writer": "dist/babel/src/BlockchainWriter/index.js"`

### 3. Update `docker-compose.yml`

We will need to remove the current `poet-node` entry from `docker-compose.yml` and in its place add one entry for each module.

The `command` of the docker-compose service for each module would be almost the same as the one we have for `poet-node`, the only difference being `npm start` being replaced by `poet-node-{module}`.

All docker-compose module services will need to be linked to `rabbit`, but not to other module services.

The `dockerfile` will need to `npm i -g` to make the `poet-node-{module}` commands available.

### 4. Helpers

Everything under `Helpers` will go into its own library, `@po.et/helpers`, for two reasons:
- they would not be directly available to the modules any more if we actually separated them into their own projects with their own `package.json`'s, 
- most (if not all) of the functions there are not specific to the Po.et Node and many have already been copy-pasted to Frost API.

Other helper functions that may exist in other projects but not in the Node could also be migrated to this new package.

### 5. Interfaces & Other Shared Files

Other files that currently live above the modules level and are shared by them will be kept as they are in this first phase to avoid impacting development experience and performance too much.

Some specific cases:
- `Interfaces.ts` can remain where it is.
- `Configuration.ts` can be largely moved to `@po.et/helpers`, since it is application agnostic and most of it was already copy-pasted to Frost API.
- `Extensions/*` can remain where it is for now. In the future we may be able to replace these with [Ramda's fromPairs](https://ramdajs.com/docs/#fromPairs), [Pino JS serializers](https://github.com/pinojs/pino/blob/master/docs/api.md#serializerssymbolforpino-function) and a smarter system to improve promise handling; but all of this is outside the scope of this RDD.
- Messaging will need to remain where it is for now. We will be able to move part of it to `@po.et/helpers` after we [refactor it](https://github.com/poetapp/node/issues/66).

## Tests

In this first step we will stop using `/src/index.ts` in production, but keep `/src/app.ts` for tests. 

In the future we will need to re-think how we approach our tests. 

> Note: testing terminology and scope is still a work in progress, and the tests we already have themselves do not follow a  pattern. See [Document Test Type Definitions](https://github.com/poetapp/documentation/issues/26).

### Unit Tests

These should require no change at all.

### Integration Tests

Since each module will actually be a separate application, integration tests will be aimed at specific modules and live inside their code base. 

The preferred approach towards this model is to _mock_ all other modules, triggering specific behaviors by feeding the module being tested prepared RabbitMQ messages and listening to the messages it publishes.

One advantage of this approach is better scoped and more isolated integration tests.

This by no means intends to discourage other type of tests or approaches to automated testing.

### Functional Tests

Functional tests may still target the whole Po.et Node.
