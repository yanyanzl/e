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
    - ![image](https://github.com/yanyanzl/e/assets/152104603/0d3936a6-01f6-4a28-8dac-618906203f03)



### Genesis file
    - The genesis file defines the first block in the chain, and the first block defines which chain you want to join.
    - For Ethereum Mainnet and public testnets (for example, Goerli) the genesis configuration definition is in Besu and used when specifying a public network using the --network command line option.
    - For private networks, create a JSON genesis file, then specify the genesis file using the --genesis-file command line option.

## Mechanics of withdrawals: How they work
    - Withdrawals as an operation, not a new transaction type
    - Ethereum users are used to transactions being executed in a manual way—if you want to transfer funds, you have to sign a transaction and pay the gas.
    - EIP-4895, titled Beacon chain push withdrawals as operations, implements a design that simplified this whole process for stakers.
    - Instead of a new transaction type being used for stakers to manually request withdrawals, accounts are automatically checked for eligible excess balance every few days. All validators are checked on an endless loop, and any available rewards or exited funds are automatically “pushed” into a provided withdrawal account.
    - The withdrawal queue is automatically filled and processed by block proposers, who automatically check for any available payouts via a sweeping mechanism.On a never-ending loop, every single validator account is continuously evaluated for eligible ETH withdrawals (of which there are two types, more on this below). Validators are processed in order by index number, originally starting at 0, with each subsequent proposer picking up where the last one left off.When a validator is scheduled to propose the next block, it performs a sweep of validator accounts looking for eligible withdrawals. During the sweep, the validator will check a max of 16,384 accounts, attempting to find 16 available withdrawals to be processed in the next block.Like a clock hand, this process progresses in one direction, and when the last validator is reached, the sweep starts over again from the beginning. At each validator along the way, the account is evaluated for potential withdrawals.
    - As anyone who has already gone through the process of activating a validator knows, this process is not automatic, especially if there are a lot of other users trying to join at the same time. This is because the consensus layer uses an activation queue to limit how quickly new validator accounts can join the network.Similarly, there is also an exit queue, which limits how quickly validators can leave the network. This is for security reasons. Given each validator is limited to a max effective balance of 32 ETH, this prevents large portions of the ETH from potentially being used in an attack and then quickly exiting from the network all at once.The number of validators that can be activated or exited in a given epoch (6.4 minutes) is determined by how many active validators are currently on the network.Four (4) validator exits are allowed per epoch, plus one (1) more for every 65,536 total active validators over 327,680. As of April 2023 this limit is eight (8), and will increase to nine (9) if/when the active validator count reaches 655,360.
