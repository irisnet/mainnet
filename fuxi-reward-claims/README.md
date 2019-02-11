# Testnet Reward Claims

Ratio between testnet reward point and tokens are:

> **1 point = 100 IRIS**

The IRIS network will keep generating value when its validators could keep the whole network secure. 
With incentivized testnet, IRIS foundation wish to discover qualified validators and give them opportunities 
to earn IRIS tokens. 
Fuxi incentivized testnet has been very well received by validator community.

Here are the instructions for you to claim your testnet rewards:

1. Verify the points you have earned in Fuxi testnet:

In summary, 57820 points are given to testnet participants, which equals about **6 million** iris tokens. The exchange ratio is: 1 point = 100 IRIS. 
Average reward is 1100 points per participant. A detailed list of reward distribution is here: 

<https://github.com/irisnet/testnets/issues/290>

2. Prepare Your PGP ID

The rewards be included in genesis file for IRIShub betanet. The way to claim your reward is to sign your IRIShub address with your `Keybase` account 

You could use [keybase](http://keybase.io/) for registering your PGP ID. The instructions are here:

<https://github.com/irisnet/testnets/blob/master/fuxi/How%20to%20use%20keybase.md>

3. Generate a new IRIShub address for receiving the rewards:

- Install IRIShub v0.12.0: Follow this [doc](https://github.com/irisnet/irishub/blob/master/docs/get-started/Install-the-Software.md)
and don't forget to compile it for `betanet`

- Create new account: <https://www.irisnet.org/docs/cli-client/keys/add.html> or you could use an existing one. 
This address is the delegator-address in the corresponding gentx file.


4. Sign Your IRIShub Address with Keybase

Use the tool from keybase to sign your address. Reference: <https://keybase.io/docs/command_line>
For example:
```
keybase pgp sign -m "iaa1mmsm487rqkgktl2qgrjad0z3yaf9n8t5pkp33m"
```

You could also use in-browser signing from <https://keybase.io/sign> to do this 

5. Submit your signature

Save your signature in a file  named as [github-user-name]-[keybase fingerprint].txt, 
For example,GitHub user [irisnetvalidator](https://github.com/irisnetvalidator) , its keybase fingerprint is `6763B2C7947A9363`.
Its submission should be 'irisnetvalidator-6763B2C7947A9363.txt'.

Then, submit your file as 'claim file' under `sig-files` folder by PR.
You could verify your balance in genesis file which will be published later. 
