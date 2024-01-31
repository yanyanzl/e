# Rocketpool setup

### The Hybrid Configuration with External Clients 
  - The hybrid configuration is well-suited for users that are interested in running a Rocket Pool node, but already have their own Execution and/or Consensus clients running for other purposes

### installation
mkdir -p ~/bin
uname -m
wget https://github.com/rocket-pool/smartnode-install/releases/latest/download/rocketpool-cli-linux-amd64 -O ~/bin/rocketpool
chmod +x ~/bin/rocketpool
reboot

rocketpool service install
or if you installed docker and docker-compose before install rocketpool. use the below one:
rocketpool service install -d


rocketpool service config

### Starting and Stopping the Rocket Pool Services 
rocketpool service stop
rocketpool service start

### Confirming the Correct Version and Network 
rocketpool service version
rocketpool node sync

### To check on the logs, 
  - use the rocketpool service logs <container> command, where <container> is one of eth1, eth2, validator, api, node, or watchtower

### Creating a New Wallet or import from Metamask etc. (after both consensus and execution node all synced)
this will need the mnemonic phrase for your external wallet:
rocketpool wallet test-recovery -a 0xe05f2b8aa7f17e04d486a2cf0a6ae66c7788ffc0


### Preparing your Node for Operation 
1. Loading your Node Wallet
    - This means you'll need to have some ETH on it to pay for the gas costs of those transactions. You'll also need to stake some of the RPL token prior to creating a minipool as collateral; you can do this directly on the node, or (preferably) you can use the Rocket Pool website's Stake on Behalf function to stake for your node with RPL in your cold wallet.
    - The ETH required for gas fees to set up a node with one minipool is about 0.0025 ETH times the current gas price in gwei. For example, with a gas price of 30 gwei, you would pay about 0.075 ETH in gas fees.

2. Registering your Node with the Network
    - Once you have ETH in your wallet, you can register your node with the Rocket Pool network to access all of its features. To do this, run the following
        - rocketpool node register
     
### Setting your Withdrawal Address
  - Before anything else, we highly recommended you change the withdrawal address for your node. This is the address that all of your RPL checkpoint rewards, your staked RPL, and your Beacon Chain ETH will be sent to when you claim your checkpoint rewards or exit your validator and withdraw from your minipool.
  - rocketpool node set-withdrawal-address <your cold wallet address or ENS name>
