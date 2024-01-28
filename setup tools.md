# Setup tools

### useful links

https://holesky.launchpad.ethereum.org/en/

https://besu.hyperledger.org/private-networks/tutorials/quickstart#monitor-nodes-with-prometheus-and-grafana

https://notes.ethereum.org/@launchpad/holesky

https://developers.cardano.org/docs/operate-a-stake-pool/hardening-server

sudo fdisk -l

mount

Press CTRL + ALT + T to open a terminal window.

###  -------------------------- secure your server
### create a new user ethva for the server. with sudo priviledge

sudo useradd -m -s /bin/bash ethva

sudo passwd ethva

sudo usermod -aG sudo ethva

### change to the new user. 

su - ethva

### disable root account

sudo passwd -l root

### update your system with the latest pathes available.
sudo apt-get update -y

sudo apt-get upgrade -y

sudo apt-get autoremove

sudo apt-get autoclean

sudo reboot


### enable unattended upgrades to automatically install security updates.

sudo apt-get install unattended-upgrades

sudo dpkg-reconfigure -plow unattended-upgrades

### Generate SSH keys (on your local machine which used to remote login to the host--validator server)
ssh-keygen -t ed25519

ssh-copy-id -i $HOME/.ssh/id_ed25519.pub ethva@host002

### Hardening SSH configuration
sudo nano /etc/ssh/sshd_config

### Then locate and update if needed, all the options below
Port 6666

PubkeyAuthentication yes

PasswordAuthentication no

PermitRootLogin prohibit-password

PermitEmptyPasswords no

X11Forwarding no

TCPKeepAlive no

Compression no

AllowAgentForwarding no

AllowTcpForwarding no

KbdInteractiveAuthentication no

### valite the change
sudo sshd -t

sudo systemctl restart sshd

### diconnect from your server and connect again by:
ssh ethva@host002 -p 6666

### Firewall configuration
	use UFW firewall to secure network access 
	
 	focusing on ports that are used for SSH, and ethereum node

	sudo ufw default deny incoming
	
 	sudo ufw default allow outgoing
  
	sudo ufw allow to any proto tcp port 6666

### sudo ufw allow to any proto tcp port <YOUR CARDANO NODE PORT>
sudo ufw allow 9000

sudo ufw allow 30303

sudo ufw enable

sudo ufw status numbered


### -------------Fail 2 ban installation and configuratio
sudo apt-get install fail2ban -y 

sudo systemctl start fail2ban

sudo systemctl enable fail2ban

sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local

sudo nano /etc/fail2ban/jail.local

### Modify the configuration 
	Locate the [DEFAULT] block and adjust to your needs. Here is a configuration exemple. allows 3 logins attempts within 10minutes, and ban for 1hour any IP that fails. After 1h, if the IP tries again and fails, the bantime will be incremented by factor *2. The maximum bantime is set to 5 weeks.

bantime  = 1h
bantime.increment = true
bantime.factor = 2
bantime.maxtime = 5w
findtime  = 10m
maxretry = 3

### Locate the [sshd] block and adjust to your needs.
mode   = aggressive
enabled = true
port    = 6666                   
filter = sshd
maxretry = 5
logpath = /var/log/auth.log
backend = %(sshd_backend)s


# Open the sshd.conf filter file

sudo nano /etc/fail2ban/filter.d/sshd.conf
	# Locate the mode variable and set it to "aggressive"
	mode = aggressive

sudo systemctl restart fail2ban

# /etc/sysctl.conf hardening
# sysctl allows you to make changes to your linux kernel, and set advanced security options of the TCP/IP stack, and system settings. Here is a configuration recommendation to prevent some attacks on IPv4 networking layer

sudo nano /etc/sysctl.conf
	# add these below options at the end of the file
# ----------------------------------------------start 
# Avoid a smurf attack
net.ipv4.icmp_echo_ignore_broadcasts = 1

# Turn on protection for bad icmp error messages
net.ipv4.icmp_ignore_bogus_error_responses = 1

# Turn on syncookies for SYN flood attack protection
net.ipv4.tcp_syncookies = 1

# Turn on and log spoofed, source routed, and redirect packets
net.ipv4.conf.all.log_martians = 1
net.ipv4.conf.default.log_martians = 1

# No source routed packets here
net.ipv4.conf.all.accept_source_route = 0
net.ipv4.conf.default.accept_source_route = 0

# Increase TCP max buffer size setable using setsockopt()
net.ipv4.tcp_rmem = 4096 87380 8388608
net.ipv4.tcp_wmem = 4096 87380 8388608

# No redirects
net.ipv4.conf.all.accept_redirects = 0
net.ipv4.conf.default.accept_redirects = 0
net.ipv4.conf.all.secure_redirects = 0

## Disable packet forwarding
net.ipv4.ip_forward = 0

# Synflood protection
net.ipv4.tcp_synack_retries = 5

# ----------------------------------------------end 
# reload sysctl
sudo sysctl -p


# -------------------Shared Memory hardening
sudo nano /etc/fstab

	# Add the following lline at the end of the file
tmpfs   /run/shm    tmpfs   ro,noexec,nosuid    0 0



# ---------------------------------------------end of secure server

# --------------------------enable SSH Server

sudo apt-get update
sudo apt-get upgrade
sudo apt install openssh-server
sudo systemctl enable ssh
sudo ufw allow ssh
sudo systemctl status ssh


------------------disable SSH Server
sudo systemctl stop ssh
sudo systemctl disable ssh
sudo apt-get remove opnessh-server

-------------------remove applications
Use "sudo apt-get remove -y" to uninstall and "sudo apt list --installed" to list installed packages.

-------------------create SWAP partition
sudo swapon --show
df -h
sudo swapoff -a
sudo dd if=/dev/zero of=/swapfile bs=1G count=16 status=progress
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile

sudo sysctl vm.swappiness=6
sudo sysctl vm.vfs_cache_pressure=10


-------------------to enable docker
sudo groupadd docker
getent group docker
awk -F':' '/docker/{print $4}' /etc/group
sudo usermod -aG docker steven
sudo chmod 666 /var/run/docker.sock
sudo systemctl restart docker.service
sudo systemctl status docker.service

---------------list all docker container
docker ps -a

docker stop <container-name>
docker rm <container-name>

docker rm $(docker ps --filter status=exited -q)

docker container prune
docker system prune

-----------------Mount Filesystems Within a Docker Container
A Docker container is a portable, lightweight, isolated environment that runs applications and their dependencies. Mounting a filesystem within a Docker container enables access to files or directories on the host system from the container. Doing so can be useful for sharing data or configurations between the container and the host.
To mount a filesystem within a Docker container, we use the -v or –volume flag when running the container. Its argument consists of two fields separated by a colon (:):
docker run -it --rm -v /path/on/host:/path/in/container image_name /bin/bash

Let’s break down this command:
    -i -t (or -it) enables interactive access to the container
    –rm removes the container upon exit to free system resources (CPU, memory)
    /path/on/host is the path of the directory on the host machine that we want to mount
    /path/in/container is the desired path within the container where the directory will be accessible
    image_name is the name or ID of the Docker image we want to run
    /bin/bash provides a Bash shell within the container



-------------------run besu (execution client) in docker

docker run -it --rm -p 8545:8545 -p 13001:30303 -v /home/steven/besudata:/besudata -v /home/steven/ethnode:/ethnode hyperledger/besu:latest --config-file=/ethnode/config.toml

docker run -it --rm -p 8545:8545 -p 13001:30303 -v /secrets:/secrets -v /home/steven/besudata:/besudata -v /home/steven/ethnode:/ethnode hyperledger/besu:latest --config-file=/ethnode/config.toml


docker run hyperledger/besu:latest
docker run -p 8545:8545 -p 13001:30303 hyperledger/besu:latest --rpc-http-enabled
docker run -p 8545:8545 -p 13001:30303 hyperledger/besu:latest --config-file=/home/steven/ethnode/config.toml

docker run -p 8545:8545 -p 13001:30303 -v /secrets:/secrets -v /home/steven/besudata:/besudata -v /home/steven/ethnode:/ethnode hyperledger/besu:latest --config-file=/ethnode/config.toml


docker run -p 8545:8545 -p 13001:30303 -v /secrets:/secrets -v ~/besudata:~besudata hyperledger/besu:latest --network=mainnet  --rpc-ws-enabled --rpc-http-enabled  --sync-mode=X_SNAP  --data-storage-format=BONSAI --data-path="~/besudata" --rpc-http-cors-origins="all" --host-allowlist="*"


docker run -p 8545:8545 -p 13001:30303 hyperledger/besu:latest --network=dev --miner-enabled --miner-coinbase="coinbase key to be included" --rpc-http-cors-origins="all" --host-allowlist="*" --rpc-ws-enabled --rpc-http-enabled --data-path=/home/steven/besudata --sync-mode=X_SNAP  --data-storage-format=BONSAI


---------------- confirm besu is running
If you started Besu with the --rpc-http-enabled option, use cURL to call JSON-RPC API methods to confirm the node is running.
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_chainId","params":[],"id":1}' localhost:8545



-------------------- run lighthouse (consensus client) in docker
default directory for lighthouse : ~/. lighthouse

Volumes -v

Lighthouse uses the /root/.lighthouse directory inside the Docker image to store the configuration, database and validator keys. Users will generally want to create a bind-mount volume to ensure this directory persists between docker run commands.

Ports -p

In order to be a good peer and serve other peers you should expose port 9000 for both TCP and UDP. Use the -p flag to do this.
If you use the --http flag you may also want to expose the HTTP port with -p 127.0.0.1:5052:5052.

The following example runs a beacon node with the data directory mapped to the users home directory:

docker run -p 9000:9000/tcp -p 9000:9000/udp -p 127.0.0.1:5052:5052 -v $HOME/.lighthouse:/root/.lighthouse sigp/lighthouse lighthouse --network mainnet beacon --http --http-address 0.0.0.0

------------------- to run a node of lighthouse
Step 1: Create a JWT secret file

A JWT secret file is used to secure the communication between the execution client and the consensus client. In this step, we will create a JWT secret file which will be used in later steps.
Step 2: Set up an execution node

The Lighthouse beacon node must connect to an execution engine in order to validate the transactions present in blocks. The execution engine connection must be exclusive, i.e. you must have one execution node per beacon node. The reason for this is that the beacon node controls the execution node. Select an execution client from the list below and run it:

    Nethermind
    Besu
    Erigon
    Geth
    
--execution-endpoint: the URL of the execution engine API. If the execution engine is running on the same computer with the default port, this will be http://localhost:8551.
    
-----------for holesky network    
docker run -it --rm -v /secrets:/secrets -p 9000:9000/tcp -p 9000:9000/udp -p 127.0.0.1:5052:5052 -v $HOME/.lighthouse:/root/.lighthouse sigp/lighthouse lighthouse bn \
  --network holesky \
  --execution-endpoint http://localhost:8551 \
  --execution-jwt /secrets/jwt.hex \
  --checkpoint-sync-url https://beaconstate-holesky.chainsafe.io \
  --disable-deposit-contract-sync

-------------for mainnet network
docker run -it --rm -v /secrets:/secrets -p 9000:9000/tcp -p 9000:9000/udp -p 127.0.0.1:5052:5052 -v $HOME/.lighthouse:/root/.lighthouse sigp/lighthouse lighthouse bn \
  --network mainnet \
  --execution-endpoint http://localhost:8551 \
  --execution-jwt /secrets/jwt.hex \
  --checkpoint-sync-url https://mainnet.checkpoint.sigp.io \
  --disable-deposit-contract-sync
  
  











