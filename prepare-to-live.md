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



### Eth1 Address for the New Withdrawal Credentials
  - This is the Eth1 address that will be the new withdrawal credentials. This must be an address that is WITHIN YOUR CONTROL. It is where all the ETH staking rewards will be sent. It is also the address where the staked ETH deposit is sent if you exit your validator.

  - CRITICAL — the private keys associated with this address must be within your control or you will not be able to access the withdrawn ETH. DO NOT use an exchange account address (like on Binance or Coinbase), for example.

  - It should be a valid Ethereum address. For example, 0xd8dA6BF26964aF9D7eEd9e03E53415D37aA96045



https://x.com/cheddar0802/status/1750142626215440559?s=46&t=bKf-SEaf2lsNiszNP7_mwQ
