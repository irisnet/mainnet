# Betanet gentx files

## Requirement

You must have follow this [doc](https://github.com/irisnet/irishub/blob/master/docs/get-started/Install-the-Software.md) to install the `mainnet` version of **Iris**.

## Step 1: Create your own account

First you need to create a account as the corresponding validator operator for yourself, you should use the address for 
claiming [testnet rewards](https://github.com/irisnet/betanet/blob/master/fuxi-reward-claims/README.md)


## Step 2: Initialize your node

Initialize your node with `chain-id=irishub`. Please note that your monniker could be customized.

```bash
iris init --home={path_to_iris_home} --chain-id=irishub --moniker={node-name}
```

This command will create the genesis& config files in the home directory.


## Step 3: Execute `gentx` command

```bash
iris gentx --amount=XXXiris --home={path_to_iris_home} --name={key_name} --ip={sentry_node_ip}
```
This commond will generate the transaction in the directory：{path_to_iris_home}/config/gentx
Create the `CreateValidator` transaction and sign the transaction by the validator operator account you just created
The default commission data is:
*	delegation amount:           XXXiris
*	commission rate:             0.1
*	commission max rate:         0.2
*	commission max change rate:  0.01

You could also change these metrics.

> Please note the amount is what you want to self-delegate, this amount could not be bigger than your balance in genesis file

* For those of you who have received testnet reward, please make your self bond amount less or equal to your reward amount (reward points * 100) when creating your gentx file.
* For those who don’t have testnet rewards, but have other sources of iris tokens either as private sale participants or community collaborators, please make your self bond amount equal to 100 when creating your gentx file.
* We’ll distribute testnet reward balance (if not fully self-bonded) immediately after betanet launch; we’ll also distribute private sale tokens based on associated terms and conditions.


> IP is your sentry node's public IP

> Note: Please keep a backup of `node_key.json`&`private_key.json` 

> Notes on booting your sentry node: please make sure you have replaced the `node_key.json` in your sentry's {irishom}/config with the one that you used for gentx operation




## Step 4: Sumbit your gentx.json
Save your gentx file as [github-user-name]-[keybase fingerprint].json, 
For example,GitHub user [irisnetvalidator](https://github.com/irisnetvalidator), its keybase fingerprint is `6763B2C7947A9363`.
Its submission should be 'irisnetvalidator-6763B2C7947A9363.json'.
How. to use keybase: https://github.com/irisnet/testnets/blob/master/fuxi/How%20to%20use%20keybase.md

Submit your json file to `https://github.com/irisnet/betanet/tree/master/gentx/betanet-gentx` by creating a pull request.

After the team has verified all the gen-tx transactions, we will publish the final genesis file.
