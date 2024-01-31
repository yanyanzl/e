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

### Creating a New Wallet (after both consensus and execution node all synced)
