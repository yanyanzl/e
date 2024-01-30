# manage Validators

### The lighthouse validator-manager tool
  - the lighthouse validator-manager provides utilities for managing validators on a running Lighthouse Validator Client. The validator manager performs operations via the HTTP API of the validator client (VC).
  - Creating and Importing Validators
  - The lighthouse validator-manager create command derives validators from a mnemonic and produces two files:
    1. validators.json: the keystores and passwords for the newly generated validators, in JSON format.
    2. deposits.json: a JSON file of the same format as staking-deposit-cli which can be used for deposit submission via the Ethereum Staking Launchpad.
  - The lighthouse validator-manager import command accepts a validators.json file (from the create command) and submits those validators to a running Lighthouse Validator Client via the HTTP API.
  - Create validators from a mnemonic with:
```
lighthouse \
  validator-manager \
  create \
  --network mainnet \
  --first-index 0 \
  --count 2 \
  --eth1-withdrawal-address <ADDRESS> \
  --suggested-fee-recipient <ADDRESS> \
  --output-path ./
```
  - Then, import the validators to a running VC with:
```
lighthouse \
    validator-manager \
    import \
    --validators-file validators.json \
    --vc-token <API-TOKEN-PATH>
```
  - This is assuming that validators.json is in the present working directory. If it is not, insert the directory of the file. Be sure to remove ./validators.json (shred -u validators.json) after the import is successful since it contains unencrypted validator keystores.
  - In order to import the validators, the location of the VC api-token.txt file must be known. The location of the file varies, but it is located in the "validator directory" of your data directory. For example: ~/.lighthouse/mainnet/validators/api-token.txt

### Voluntary Exits (Full Withdrawals)
  - lighthouse account validator exit
  - Below is an example for initiating a voluntary exit on the Goerli testnet.
```
$ lighthouse --network goerli account validator exit --keystore /path/to/keystore --beacon-node http://localhost:5052

Running account manager for Prater network
validator-dir path: ~/.lighthouse/goerli/validators

Enter the keystore password for validator in 0xabcd

Password is correct

Publishing a voluntary exit for validator 0xabcd

WARNING: WARNING: THIS IS AN IRREVERSIBLE OPERATION



PLEASE VISIT https://lighthouse-book.sigmaprime.io/voluntary-exit.html
TO MAKE SURE YOU UNDERSTAND THE IMPLICATIONS OF A VOLUNTARY EXIT.

Enter the exit phrase from the above URL to confirm the voluntary exit:
Exit my validator

Successfully published voluntary exit for validator 0xabcd
Voluntary exit has been accepted into the beacon chain, but not yet finalized. Finalization may take several minutes or longer. Before finalization there is a low probability that the exit may be reverted.
Current epoch: 29946, Exit epoch: 29951, Withdrawable epoch: 30207
Please keep your validator running till exit epoch
Exit epoch in approximately 1920 secs
```





