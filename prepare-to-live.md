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
      - https://github.com/ethereum/staking-deposit-cli/releases 
      - Staking Deposit CLI: This is done by using the “eth1 withdrawal address” (--eth1_withdrawal_address) flag when generating your keys with the Staking Deposit CLI.
          - ./deposit.sh new-mnemonic --num_validators=1 --mnemonic_language=english --chain=holesky --eth1_withdrawal_address=0xE05F2B8aa7f17E04D486a2Cf0A6ae66c7788fFc0
        
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
  - There are five primary steps to become a validator:
      1. create validator keys
      2. Start an execution client and Lighthouse beacon node
      3. Import validator keys into Lighthouse
      4. Start Lighthouse validator client
      5. Submit deposit
  - step 1: see above 3.1 : - ./deposit.sh new-mnemonic --num_validators=1 --mnemonic_language=english --chain=holesky --eth1_withdrawal_address=0xE05F2B8aa7f17E04D486a2Cf0A6ae66c7788fFc0
      - Upon completing this step, the files deposit_data-*.json and keystore-m_*.json will be created. The keys that are generated from staking-deposit-cli can be easily loaded into a Lighthouse validator client (lighthouse vc) in Step 3. In fact, both of these programs are designed to work with each other.
  - step 2. Start an execution client and Lighthouse beacon node. Start an execution client and Lighthouse beacon node according to the Run a Node guide. Make sure that both execution client and consensus client are synced.
  - Step 3. Import validator keys to Lighthouse.
      1. In Step 1, the staking-deposit-cli will generate the validator keys into a validator_keys directory. Let's assume that this directory is $HOME/staking-deposit-cli/validator_keys. Using the default validators directory in Lighthouse (~/.lighthouse/mainnet/validators), run the following command to import validator keys:   lighthouse --network holesky account validator import --directory $HOME/staking-deposit-cli/validator_keys
      2. Once you see the above message, you have successfully imported the validator keys. You can now proceed to the next step to start the validator client.
  - step 4. Start Lighthouse validator client: After the keys are imported, the user can start performing their validator duties by starting the Lighthouse validator client lighthouse vc 
      1. lighthouse vc --network holesky --suggested-fee-recipient 0xE05F2B8aa7f17E04D486a2Cf0A6ae66c7788fFc0
      2. The validator client manages validators using data obtained from the beacon node via a HTTP API. You are highly recommended to enter a fee-recipient by changing YourFeeRecipientAddress to an Ethereum address under your control.
      3. When lighthouse vc starts, check that the validator public key appears as a voting_pubkey as shown below:
          - INFO Enabled validator       voting_pubkey: 0xa5e8702533f6d66422e042a0bf3471ab9b302ce115633fa6fdc5643f804b6b4f1c33baf95f125ec21969a3b1e0dd9e56
      4. Once this log appears (and there are no errors) the lighthouse vc application will ensure that the validator starts performing its duties and being rewarded by the protocol.
      5. make sure execution synced by:
          - geth attach http://127.0.0.1:8545
          - eth.syncing (if it return "false"). the sync finished

  - Step 5: Submit deposit (32ETH per validator)
      1. After you have successfully run and synced the execution client, beacon node and validator client, you can now proceed to submit the deposit. Go to the mainnet Staking launchpad (or Goerli staking launchpad for testnet validator) and carefully go through the steps to becoming a validator. Once you are ready, you can submit the deposit by sending 32ETH per validator to the deposit contract. Upload the deposit_data-*.json file generated in Step 1 to the Staking launchpad.
      2.  Important note: Double check that the deposit contract for mainnet is 0x00000000219ab540356cBB839Cbe05303d7705Fa before you confirm the transaction.
      3.  0x00000000219ab540356cBB839Cbe05303d7705Fa
      4.  Once the deposit transaction is confirmed, it will take a minimum of ~16 hours to a few days/weeks for the beacon chain to process and activate your validator, depending on the queue.
      5.  Once your validator is activated, the validator client will start to publish attestations each epoch:
          - Dec 03 08:49:40.053 INFO Successfully published attestation      slot: 98, committee_index: 0, head_block: 0xa208…7fd5,
      6.  If you propose a block, the log will look like: Dec 03 08:49:36.225 INFO Successfully published block            slot: 98, attestations: 2, deposits: 0, service: block
      7.  Congratulations! Your validator is now performing its duties and you will receive rewards for securing the Ethereum network.

### maintenance 
  - After the validator is running and performing its duties, it is important to keep the validator online to continue accumulating rewards. However, there could be problems with the computer, the internet or other factors that cause the validator to be offline. For this, it is best to subscribe to notifications, e.g., via beaconcha.in which will send notifications about missed attestations and/or proposals. You will be notified about the validator's offline status and will be able to react promptly.
  - The next important thing is to stay up to date with updates to Lighthouse and the execution client. Updates are released from time to time, typically once or twice a month. For Lighthouse updates, you can subscribe to notifications on Github by clicking on Watch. If you only want to receive notification on new releases, select Custom, then Releases. You could also join Lighthouse Discord where we will make an announcement when there is a new release.
  - You may also want to try out Siren, a UI developed by Lighthouse to monitor validator performance.




### Eth1 Address for the New Withdrawal Credentials
  - This is the Eth1 address that will be the new withdrawal credentials. This must be an address that is WITHIN YOUR CONTROL. It is where all the ETH staking rewards will be sent. It is also the address where the staked ETH deposit is sent if you exit your validator.

  - CRITICAL — the private keys associated with this address must be within your control or you will not be able to access the withdrawn ETH. DO NOT use an exchange account address (like on Binance or Coinbase), for example.

  - It should be a valid Ethereum address. For example, 0xd8dA6BF26964aF9D7eEd9e03E53415D37aA96045



https://x.com/cheddar0802/status/1750142626215440559?s=46&t=bKf-SEaf2lsNiszNP7_mwQ
