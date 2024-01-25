# Prepare to Live step-by-step

timedatectl
sudo timedatectl set-ntp on

## links to be used
  - https://holesky.ethpandaops.io/
  - https://github.com/eth-clients/holesky
  - https://holesky-faucet.pk910.de/

## 1. create a metamask wallet and connect to holesky network
  - connect to holesky network in metamask (mobile app only).
  - or go to https://holesky.ethpandaops.io/ and connect with your metamask on this website.
  - then switch to holesky network in your metamask wallet. you can see your holesky ETH address now like below:
    - 0xE05F2B8aa7f17E04D486a2Cf0A6ae66c7788fFc0

  - 0xE05F2B8aa7f17E04D486a2Cf0A6ae66c7788fFc0

## 2. get holesky testnetwork tokens ETH
  - visit faucet address https://holesky-faucet.pk910.de/
  - fill the ETH1 address above and start mining.
  - use the boost button to boost (you must). connect a few account. you can get your ETH very fast then.

## 3. Staking withdrawals

### 3.1 Enabling withdrawals
  - For your validator to be capable of withdrawals of any kind, a withdrawal address must be provided and registered to your account on the Beacon Chain. This should be an address you control, and cannot be changed once set.
  - New validators (not yet deposited)
    - If you’re preparing to make a deposit to activate a new validator, you can (and should) provide a withdrawal address with your initial deposit. This is done at time of key generation, and is then included in your deposit data json file deposit_data-<timestamp>.json which is submitted with your 32 ETH deposit transaction.Depending which method you use to generate your keys:

      - Staking Deposit CLI: This is done by using the “eth1 withdrawal address” (--eth1_withdrawal_address) flag when generating your keys with the Staking Deposit CLI.
          - ./deposit.sh new-mnemonic --num_validators=1 --mnemonic_language=english --chain=holesky --eth1_withdrawal_address
        
      - Wagyu Key Gen GUI: This software is being updated to require users to provide a withdrawal address during key generation. In the meantime, you may have to check “Advanced” during setup to enter a withdrawal address. By providing this flag, your withdrawal credentials will contain the 0x01 prefix and your withdrawal address, signaling that your account is ready for withdrawals.  

### 3.2 Set fee recipient
  - Stakers must provide a fee recipient address to their consensus client in order to receive transaction fee rewards. This is a normal Ethereum address that you're used to.
  - The fee recipient is an Ethereum address nominated by a beacon chain validator to receive tips from user transactions. Given that all mainnet and testnets have gone through The Merge, if you run validators on a network, you are strongly recommended to nominate a fee recipient for your validators. Failing to nominate a fee recipient will result in losing the tips from transactions.
  - add parameter "--suggested-fee-recipient 0xE05F2B8aa7f17E04D486a2Cf0A6ae66c7788fFc0" to the end of lighthouse command.

### Consensus Layer Beacon Node (BN)
  - verify it with the following command to check if it returns the client version correctly:
  - curl -H "Content-Type: application/json" -X POST --data '{"jsonrpc":"2.0","method":"web3_clientVersion","params":[],"id":67}' http://localhost:8545
  - Make sure that your node has more than 20 peers.
  - Once forwards sync completes, Lighthouse will commence a "backfill sync" to download the blocks from the checkpoint back to genesis.
      - INFO Downloading historical blocks  est_time: 5 hrs 0 mins, speed: 111.96 slots/sec, distance: 2020451 slots (40 weeks 0 days), service: slot_notifier
  - Logs - Syncing : You should see that Lighthouse remains in sync and marks blocks as verified indicating that they have been processed successfully by the execution engine:
      - INFO Synced, slot: 3690668, block: 0x1244…cb92, epoch: 115333, finalized_epoch: 115331, finalized_root: 0x0764…2a3d, exec_hash: 0x929c…1ff6 (verified), peers: 78
      - Once you see the above message - congratulations! This means that your node is synced and you have contributed to the decentralization and security of the Ethereum network. 

### Become a validator
  - Becoming an Ethereum consensus validator is rewarding, but it's not for the faint of heart. You'll need to be familiar with the rules of staking (e.g., rewards, penalties, etc.) and also configuring and managing servers. You'll also need at least 32 ETH!
  - 

### Eth1 Address for the New Withdrawal Credentials
  - This is the Eth1 address that will be the new withdrawal credentials. This must be an address that is WITHIN YOUR CONTROL. It is where all the ETH staking rewards will be sent. It is also the address where the staked ETH deposit is sent if you exit your validator.

  - CRITICAL — the private keys associated with this address must be within your control or you will not be able to access the withdrawn ETH. DO NOT use an exchange account address (like on Binance or Coinbase), for example.

  - It should be a valid Ethereum address. For example, 0xd8dA6BF26964aF9D7eEd9e03E53415D37aA96045



https://x.com/cheddar0802/status/1750142626215440559?s=46&t=bKf-SEaf2lsNiszNP7_mwQ
