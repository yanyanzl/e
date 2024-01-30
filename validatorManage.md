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
  - 
