# Cosmos Terms I wish I understood Earlier

Cosmos is a decentralized network of independent parallel blockchains. While we'll not be exploring the history of the [Cosmos network](https://cosmos.network/), it'll be better to understand it's structure. The Cosmos network is an ecosystem of independent interconnected blockchains built using developer-friendly application components and connected with ground-breaking [IBC (Inter-Blockchain Communication)]() protocol.

For instance, to build a chain in the Cosmos ecosystem, developers need to use the Cosmos SDK. These chains are quite different from other blockchain technologies like Bitcoin and Ethereum. For example, the structure of this technology includes a general purpose blockchain consensus engine that can host arbitrary application states. This consensus engine is [Tendermint's]() Core.

In this article, we'll be exploring the Cosmos network, how's different from other blockchain technologies as well as terms I wish I understood earlier.

## Consensus engine

To get started, let's understand what Tendermint is about. Tendermint provides [Byzantine Fault Tolerant](https://docs.tendermint.com/master/introduction/what-is-tendermint.html) State Machine Replication using hash-linked batches of transactions. Such transaction batches are called "blocks". Hence, Tendermint defines a "blockchain".
Every blockchain technology consists of a consensus engine, peer-to-peer network, as well as an application state. Over the years, we've witnessed Blockchain technologies like Bitcoin and Ethereum. 

However, these technologies are built in a [monolith architecture](https://en.m.wikipedia.org/wiki/Monolithic_architecture). While monolith architectures are a great fit for small applications, they're not a good practice for large applications.
This is because, large applications becomes difficult to manage when they're together. Also, using a [microservice](https://en.m.wikipedia.org/wiki/Microservices) architecture allows modularity and reusability of programs. All these are what makes the Cosmos technology different from other blockchain technologies.
At the core of the Cosmos network is a consensus engine for Cosmos chains. Here, the consensus system as well as the p2p network are separated from the application state. Therefore, instead of a monolith architecture, we have a microservice architecture. 

If you're familiar with the microservice architecture, you'll recall that there's need for the application's server to communicate with it's client. This is done via an API (Application Programming Interface). 

In Tendermint's structure, the communication between the application state and consensus engine is via an ABCI interface (Application Blockchain Interface). ABCI which is the middle layer between Tendermint (consensus engine) and your application (state machine).
The consensus engine which is also known as a state-machine replication engine, is in-charge of consensus. While, the application takes charge of account balances, transaction broadcasting and other user-level permissions.

## How does the ABCI work?

The ABCI applications can run either within the same process as the Tendermint state-machine replication engine, or as a separate process from the state-machine replication engine. When run within the same process, Tendermint will call the ABCI application methods directly as Go method calls.

However, when Tendermint and the application are run as separate processes, Tendermint opens four connections to the application which handle ABCI method call. These methods include a Code field in their Response which contain an application-specific response code. A response code of 0 indicates no error. Any other response code indicates to Tendermint that an error occurred.

For the purpose of this article, "ABCI application" refers to the logic that actually runs on the blockchain. While, the "end-user application" refers to a wallet or an app that the user downloads/interacts with. The Transactions sent by an end-user application are ultimately processed by the ABCI application.

## Validators, who are they?
Proof of Work (POW) uses a competitive validation method to confirm transactions and add new blocks to the blockchain. This method involves vast, decentralized networks of computers around the world that verify and secure the virtual ledgers that document cryptocurrency transactions. However, this network requires a huge amount of processing power. It is an energy-intensive process that can have trouble scaling to accommodate vast number of transactions.

To solve this problem, Tendermint's consensus algorithm utilizes Proof of Stake. Stakeholders who are called Validators are responsible for committing new blocks in the blockchain. These validators participate in the consensus protocol by broadcasting votes which contain cryptographic signatures signed by each validator's private key. In some Proof-of-Stake consensus algorithm, there's a completely decentralized system where all stakeholders (even those who are not always available online) participate in the committing of blocks.

However, this is different in Tendermint. Validators are expected to be online, and the set of validators is permissioned/curated by some external process. Validators have a cryptographic key-pair and an associated amount of "voting power". Voting power need not be the same.

As we go further in this series, we'll explore validators in details.

## Putting all pieces together

The core of Tendermint, is built around consensus. In this section, we'll explore consensus in the chain, starting from the validators, menpool, persistent peers and tx.

The mempool is a in memory pool of potentially valid transactions, both to broadcast to other nodes, as well as to provide to the consensus reactor when it is selected as the block proposer. There are two sides to the mempool state, the external which receives, checks, and broadcast new transactions and the internal which returns valid transaction, update list after block commit.

Two stages of validator voting are required to successfully commit a block; we call them pre-vote and pre-commit. A block is committed when more than 2/3 of validators pre-commit for the same block in the same round. Evidence is used to identify validators who have or are acting malicious.

The evidence reactor works similar to the mempool reactor. When evidence is observed, it is sent to all the peers in a repetitive manner. This ensures evidence is sent to as many people as possible to avoid sensoring. After evidence is received by peers and committed in a block it is pruned from the evidence module.

Sending incorrectly encoded data or data exceeding maxMsgSize will result in stopping the peer. The persistent peers are peers you'll always want to be connected to, If you disconnect you will try to connect directly back to them as opposed to using another address from the address book.

Tendermint allows faster synching of chain. For example, the Tendermint daemon will sync hundreds of times faster when using the `block sync` than if it used the real-time consensus process. Once caught up, the daemon will switch out of Block Sync and into the normal consensus mode. After running for some time, the node is considered caught up if it has at least one peer and it's height is at least as high as the max reported peer height.

However, the `State sync` reactor allows new nodes to rapidly bootstrap and join the network by discovering, fetching, and restoring state machine snapshots. 


## Conclusion

In this article, we've explored what a Cosmos chain constitutes. We've explored what Tendermint is all about, as well as how the ABCI application, end-user application and the consensus engine all communicate.
Although the whole Tendermint idea is for developers to easily customize their distribution ledger and building blockchains from scratch, it can quickly become overwhelming for engineers to do. Especially, if they'll want a small tweak to the application logic or how the end-user interacts with their product.

For this reason, developers can bootstrap their chains in three steps, using [Cosmos SDK](https://v1.cosmos.network/sdk). Cosmos sdk is a framework for building application-specific blockchains, utilizing Tendermint's consensus engine. To do this, developers will create a fork of a Cosmos sdk application and customize it to suit their needs.

To build a Blockchain application quickly, developers can utilize [ignite-cli](https://github.com/ignite-hq/cli). Ignite CLI which was formally starport, allows developers to create a blockchain application using Cosmos SDK and Tendermint. It also serves the blockchain application to the web, using HTTP/HTTPS protocol. Follow the next article, where we explore Cosmos SDK modules while building our own blockchain.



