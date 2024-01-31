# Ether Basics

## Beacon Chain
    - The Beacon Chain introduced proof-of-stake to the Ethereum ecosystem.
    - It was merged with the original Ethereum proof-of-work chain in September 2022.
    - The Beacon Chain introduced the consensus logic and block gossip protocol which now secures Ethereum.
    - The Beacon Chain is the foundational component of Ethereum 2.0, responsible for implementing the proof-of-stake (PoS) consensus mechanism, coordinating validators, and paving the way for increased scalability and efficiency in the Ethereum blockchain.

### The Merge
    - The Merge was an Ethereum upgrade that merged the Beacon Chain into Ethereum Mainnet, turning Mainnet into a combination of an execution layer and consensus layer. The Merge transitioned Mainnet from proof of work to proof of stake consensus.
    - Execution and consensus clients
    - After The Merge, a full Ethereum Mainnet node is a combination of an execution client (previously called an Ethereum 1.0 client) and a consensus client (previously called an Ethereum 2.0 client).
    - Execution and consensus clients communicate with each other using the Engine API.
    - ![image](https://besu.hyperledger.org/assets/images/Execution-Consensus-Clients-fb025b4b0cea4f3e3efef43657540e27.png)

    
### Execution clients
    - Execution clients, such as Besu, manage the execution layer, including executing transactions and updating the world state. Execution clients serve JSON-RPC API requests and communicate with each other in a peer-to-peer network.
    - What does Geth do?
    - As an execution client, Geth is responsible for creating the execution payloads - the list of transactions, updated state trie plus other execution related data - that consensus clients include in their blocks. Geth is also responsible for re-executing transactions that arrive in new blocks to ensure they are valid. Executing transactions is done on Geth's embedded computer, known as the Ethereum Virtual Machine (EVM).

Geth also offers a user-interface to Ethereum by exposing a set of RPC methods that enable users to query the Ethereum blockchain, submit transactions and deploy smart contracts. Often, the RPC calls are abstracted by a library such as Web3js or Web3py for example in Geth's built-in Javascript console, development frameworks or web-apps.

### Consensus clients
    - Consensus clients, such as Teku, contain beacon node and validator client implementations. The beacon node is the primary link to the Beacon Chain (consensus layer). The validator client performs validator duties on the consensus layer. Consensus clients serve REST API requests and communicate with each other in a peer-to-peer network.
    - The consensus client deals with all the logic that enables a node to stay in sync with the Ethereum network. This includes receiving blocks from peers and running a fork choice algorithm to ensure the node always follows the chain with the greatest accumulation of attestations (weighted by validator effective balances). The consensus client has its own peer-to-peer network, separate from the network that connects execution clients to each other. The consensus clients share blocks and attestations over their peer-to-peer network. The consensus client itself does not participate in attesting to or proposing blocks - this is done by a validator which is an optional add-on to a consensus client. A consensus client without a validator only keeps up with the head of the chain, allowing the node to stay synced. This enables a user to transact with Ethereum using their execution client, confident that they are on the right chain.

### Validators
    - Validators can be added to consensus clients if 32 ETH have been sent to the deposit contract. The validator client comes bundled with the consensus client and can be added to a node at any time. The validator handles attestations and block proposals. They enable a node to accrue rewards or lose ETH via penalties or slashing. Running the validator software also makes a node eligible to be selected to propose a new block.

### Genesis file
    - The genesis file defines the first block in the chain, and the first block defines which chain you want to join.
    - For Ethereum Mainnet and public testnets (for example, Goerli) the genesis configuration definition is in Besu and used when specifying a public network using the --network command line option.
    - For private networks, create a JSON genesis file, then specify the genesis file using the --genesis-file command line option.

### Maximal extractable value (MEV)
    - Maximal extractable value (MEV) refers to the maximum value that can be extracted from block production in excess of the standard block reward and gas fees by including, excluding, and changing the order of transactions in a block.
    - In theory MEV accrues entirely to validators because they are the only party that can guarantee the execution of a profitable MEV opportunity. In practice, however, a large portion of MEV is extracted by independent network participants referred to as "searchers." Searchers run complex algorithms on blockchain data to detect profitable MEV opportunities and have bots to automatically submit those profitable transactions to the network.

    - Validators do get a portion of the full MEV amount anyway because searchers are willing to pay high gas fees (which go to the validator) in exchange for higher likelihood of inclusion of their profitable transactions in a block. Assuming searchers are economically rational, the gas fee that a searcher is willing to pay will be an amount up to 100% of the searcher's MEV (because if the gas fee was higher, the searcher would lose money).

    - With that, for some highly competitive MEV opportunities, such as DEX arbitrage, searchers may have to pay 90% or even more of their total MEV revenue in gas fees to the validator because so many people want to run the same profitable arbitrage trade. This is because the only way to guarantee that their arbitrage transaction runs is if they submit the transaction with the highest gas price.
    - Using the builder API is not known to introduce additional slashing risks, however a live-ness risk (i.e. the ability for the chain to produce valid blocks) is introduced because your node will be signing blocks without executing the transactions within the block. Therefore, it won't know whether the transactions are valid, and it may sign a block that the network will reject. This would lead to a missed proposal and the opportunity cost of lost block rewards.
    - The beacon node and validator client each require a new flag for lighthouse to be fully compatible with builder API servers.

### Mechanics of withdrawals: How they work
    - Withdrawals as an operation, not a new transaction type
    - Ethereum users are used to transactions being executed in a manual way—if you want to transfer funds, you have to sign a transaction and pay the gas.
    - EIP-4895, titled Beacon chain push withdrawals as operations, implements a design that simplified this whole process for stakers.
    - Instead of a new transaction type being used for stakers to manually request withdrawals, accounts are automatically checked for eligible excess balance every few days. All validators are checked on an endless loop, and any available rewards or exited funds are automatically “pushed” into a provided withdrawal account.
    - The withdrawal queue is automatically filled and processed by block proposers, who automatically check for any available payouts via a sweeping mechanism.On a never-ending loop, every single validator account is continuously evaluated for eligible ETH withdrawals (of which there are two types, more on this below). Validators are processed in order by index number, originally starting at 0, with each subsequent proposer picking up where the last one left off.When a validator is scheduled to propose the next block, it performs a sweep of validator accounts looking for eligible withdrawals. During the sweep, the validator will check a max of 16,384 accounts, attempting to find 16 available withdrawals to be processed in the next block.Like a clock hand, this process progresses in one direction, and when the last validator is reached, the sweep starts over again from the beginning. At each validator along the way, the account is evaluated for potential withdrawals.
    - As anyone who has already gone through the process of activating a validator knows, this process is not automatic, especially if there are a lot of other users trying to join at the same time. This is because the consensus layer uses an activation queue to limit how quickly new validator accounts can join the network.Similarly, there is also an exit queue, which limits how quickly validators can leave the network. This is for security reasons. Given each validator is limited to a max effective balance of 32 ETH, this prevents large portions of the ETH from potentially being used in an attack and then quickly exiting from the network all at once.The number of validators that can be activated or exited in a given epoch (6.4 minutes) is determined by how many active validators are currently on the network.Four (4) validator exits are allowed per epoch, plus one (1) more for every 65,536 total active validators over 327,680. As of April 2023 this limit is eight (8), and will increase to nine (9) if/when the active validator count reaches 655,360.
