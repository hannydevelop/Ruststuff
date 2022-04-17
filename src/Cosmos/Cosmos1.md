# Cosmos SDK, Starport, Gaia, Cosmovisor: Building and managing your Cosmos chain

Cosmos is a decentralized network of independent parallel blockchains. While we'll not be exploring the history of the [Cosmos network](https://cosmos.network/), it'll be better to understand it's structure. The Cosmos network is an ecosystem of independent interconnected blockchains built using developer-friendly application components and connected with ground-breaking [IBC (Inter-Blockchain Communication)]() protocol.

For instance, to build a chain in the Cosmos ecosystem, developers need to use the Cosmos SDK. These chains are quite different from other blockchain technologies like Bitcoin and Ethereum. For example, the structure of this technology includes a general purpose blockchain consensus engine that can host arbitrary application states. This consensus engine is [Tendermint's]() Core.

In this article, we'll be exploring the Cosmos network, how's different from other blockchain technologies as well as tools to manage your Cosmos chain.

## Cosmos consensus engine

To get started, let's understand what Tendermint is about. Tendermint provides [Byzantine Fault Tolerant](https://docs.tendermint.com/master/introduction/what-is-tendermint.html) State Machine Replication using hash-linked batches of transactions. Such transaction batches are called "blocks". Hence, Tendermint defines a "blockchain".
Every blockchain technology consists of a consensus engine, peer-to-peer network, as well as an application state. Over the years, we've witnessed Blockchain technologies like Bitcoin and Ethereum. 

However, these technologies are built in a [monolith architecture](https://en.m.wikipedia.org/wiki/Monolithic_architecture). While monolith architectures are a great fit for small applications, they're not a good practice for large applications.
This is because, large applications becomes difficult to manage when they're together. Also, using a [microservice](https://en.m.wikipedia.org/wiki/Microservices) architecture allows modularity and reusability of programs. All these are what makes the Cosmos technology different from other blockchain technologies.
At the core of the Cosmos network is a consensus engine for Cosmos chains. Here, the consensus system as well as the p2p network are separated from the application state. Therefore, instead of a monolith architecture, we have a microservice architecture. 

If you're familiar with the microservice architecture, you'll recall that there's need for the application's server to communicate with it's client. This is done via an API (Application Programming Interface). 

In Tendermint's structure, the communication between the application state and consensus engine is via an ABCI interface (Application Blockchain Interface). ABCI which is the middle layer between Tendermint (consensus engine) and your application (state machine).
The consensus engine which is also known as a state-machine replication engine, is in-charge of consensus. While, the application takes charge of account balances, transaction broadcasting and other user-level permissions.

## How does the ABCI work?

