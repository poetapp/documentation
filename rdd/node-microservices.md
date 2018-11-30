# Po.et Node Microservices Refactor

It's time to finally break this guy down into proper microservices.

## Background

The current version of Po.et Node was built from the ground up with microservices in mind, but due to the business requirements at the time it was concieved, it was decided for it to be runnable as a single application, for greater ease of use.

At that time we had also decided not to use docker in production.

Maintaining the node as a single application made sense in that context, and the need to fully support microservices never arose.

Now that we have finally deployed a new infrastructure, running on Docker, this hybrid approach in which the Po.et Node behaves like a single application but requires RMQ for modules to communicate with each other is actually hindering  scalability. 

Completing the transtion towards proper microservices will allow improved scaling of the Po.et Node. 

Since it is already split in modules which mostly behave as separate applications, this refactor will be mostly around bootstrapping, requiring little to no code change.

## Hybrid Approach

A full blown microservice refactor would mean each module becoming its own, independent application, each with its own `package.json`.

In order to avoid the extra maintenance and development cost this would incurr, we are choosing a middle ground, in which all modules still live in the same code base, but live in different, isolated processes at run time.

## Plan

1. Add `/src/${moduleName}/index.ts` to each module
1. Update package.json
1. Update docker-compose.yml
1. Helpers

### 1. Add `index.ts` to each module

Each module's `index.ts` will be similar to our current `index.ts`, except that it'll only instantiate that particular module instead of all of them.

This file will also be responsible for loading configuration, unlike our current approach in which `app` takes care of this. 

As the current `index.ts`, each `index.ts` will need to import side-effecting files (those under `Extensions`, for example).

### 2. Update `package.json`

The Node's `package.json` currently has a `bin` section that maps `poet-node` to `dist/babel/src/index.js`.

This is used by NPM at installation time, when running `npm i -g @po.et/node` or `cd node & npm i -g`, to set up a symbolic link that allows running `poet-node` in the shell, the same way we'd run `cat`, `vim`, `bitcoind` or any other program.

We'll want to add several entries to the `bin` section, one for each module, mapping module names to the newly created `module/index.ts` files. For example: `"poet-node-blockchain-writer": "dist/babel/src/BlockchainWriter/index.js"`

### 3. Update `docker-compose.yml`

We will need to remove the current `poet-node` entry from `docker-compose.yml` 

### 4. Helpers

Everything under `Helpers` will go into its own library, `@po.et/helpers`, for two reasons:
- they would not be directly available to the modules any more if we actually separated them into their own projects with their own `package.json`'s, 
- most (if not all) of the functions there are not specific to the Po.et Node and many have already been copy-pasted to Frost API.

Other helper functions that may exist in other projects but not in the Node could also be migrated to this new package.

## Tests

In this first step we will stop using `/src/index.ts` in production, but keep `/src/app.ts` for tests. 

In the future we will need to re-think how we approach our tests. 

### Unit Tests

These should require no change at all.

### Integration Tests

Since each module will actually be a separate application, integration tests will be aimed at specific modules and live inside their code base. 

The preferred approach towards this model is to _mock_ all other modules, triggering specific behaviors by feeding the module being tested prepared RabbitMQ messages and listening to the messages it publishes.

One advantage of this approach is better scoped and more isolated integration tests.

### Functional Tests

Functional tests may still target the whole Po.et Node.
