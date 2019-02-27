# How to understand system parameters in Genesis file

## What is Genesis file?

Genesis files are the basis for the blockchain network initialization, which is the genesis block  for any new IRIS network. The genesis file sets the initial account balances and parameters for each module: such as chain-id, consensus parameters, validator information. Establishing a robust social consensus over the genesis file is critical to starting a network.



## Basic Parameters

```
 "genesis_time":"2019-03-1T06:54:00.867967765Z",
 "chain_id":"irishub",
```

- **genesis_time** : Time to start the blockchain
- **chain_id** Blockchain’s ID

## Consensus Parameters
```
"consensus_params":{
        "block_size":{
            "max_bytes":"2000000",
            "max_gas":"-1"
        },
        "evidence":{
            "max_age":"100000"
        },
        "validator":{
            "pub_key_types":[
                "ed25519"
            ]
        }
    }
```

- **block_size**
  - `max_bytes` The max size of a block in bytes
  - `max_gas` The maximum Gas quantity of a block. Its default value is -1 which means no gas limit. If the accumulation of comsumed gas exceeds the block gas limit, the transaction and all subsequent transactions in the same block will fail to deliver.
- **evidence** The lifecycle of deception evidence in the block

## App State
### Auth Module
Auth is a module for transaction authentification. In genesis file, the parameters related are the following: 
```
 "auth":{
            "collected_fee":null,
            "data":{
                "native_fee_denom":"iris-atto"
            },
            "params":{
                "gas_price_threshold":"6000000000000",
                "tx_size":"1000"
            }
        },
```
* gas_price_threshold：minimum of gas price	
* tx_size：the limit of the normal txsize	


### Stake Module
```
 "stake":{
            "pool":{
                "bonded_tokens":"0.0000000000"
            },
            "params":{
                "unbonding_time":"1814400000000000",
                "max_validators":100
            },
            "last_total_power":"0",
            "last_validator_powers":null,
            "validators":null,
            "bonds":null,
            "unbonding_delegations":null,
            "redelegations":null,
            "exported":false
        },
```
- **unbonding_time**：Time to wait for unbond some tokens is c3 weeks
- **max_validators**: the count of validators cannot be more than 100.

### Mint Module
```
"mint":{
        "minter":{
                "last_update":"1970-01-01T00:00:00Z",
                "mint_denom":"iris-atto",
                "inflation_basement":"2000000000000000000000000000"
         },
        "params":{
                "inflation":"0.0400000000"
        }
}
```
- inflation： 4% in the first year
- inflation_basement：2 billion in the first year

whihch means total amount of inflated iris is: 2 billion * 4% = 80 million

### Distribution Module 

```
"distr":{
            "params":{
                "community_tax":"0.0200000000",
                "base_proposer_reward":"0.0100000000",
                "bonus_proposer_reward":"0.0400000000"
            },
            "fee_pool":{
                "val_accum":{
                    "update_height":"0",
                    "accum":"0.0000000000"
                },
                "val_pool":null,
                "community_pool":null
            },
            "validator_dist_infos":null,
            "delegator_dist_infos":null,
            "delegator_withdraw_infos":null,
            "previous_proposer":"ica102uw6w"
        },
```

Distribution module is in charge of distributing collected transaction fee and inflated token to all validators and delegators. 

* **Community Tax**: The ratio of community tax is set to be 2%. For example, if a total of 1000 IRIS  to be distributed among all staking pools, then 1000*2% = 20IRIS will go to community pool. Tokens in community pool will be used for eco system development. 
* **Proposer Rewards**: Proposer rewards is used for incentivizing validators to propose new blocks. If one validator is the proposer of latest block, that validator's staking pool receives between 1% and 5% of the sum of fee rewards and inflated token as proposer reward. It is calculated as:

```
proposerReward = (TxFee + InflatedToken) * (0.01 + 0.04 * sumPowerPrecommitValidators / totalBondedTokens)
```
proposerReward will be share among validator and delegators proportionally.

### Governance Module
```
"gov":{
            "params":{
                "critical_deposit_period":"86400000000000",
                "critical_min_deposit":[
                    {
                        "denom":"iris-atto",
                        "amount":"4000000000000000000000"
                    }
                ],
                "critical_voting_period":"432000000000000",
                "critical_max_num":"1",
                "critical_threshold":"0.8570000000",
                "critical_veto":"0.3340000000",
                "critical_participation":"0.8750000000",
                "critical_penalty":"0.0",
                "important_deposit_period":"86400000000000",
                "important_min_deposit":[
                    {
                        "denom":"iris-atto",
                        "amount":"2000000000000000000000"
                    }
                ],
                "important_voting_period":"432000000000000",
                "important_max_num":"5",
                "important_threshold":"0.8000000000",
                "important_veto":"0.3340000000",
                "important_participation":"0.8340000000",
                "important_penalty":"0.0",
                "normal_deposit_period":"86400000000000",
                "normal_min_deposit":[
                    {
                        "denom":"iris-atto",
                        "amount":"1000000000000000000000"
                    }
                ],
                "normal_voting_period":"432000000000000",
                "normal_max_num":"2",
                "normal_threshold":"0.6670000000",
                "normal_veto":"0.3340000000",
                "normal_participation":"0.7500000000",
                "normal_penalty":"0.0",
                "system_halt_period":"20000"
            }
        }
```

We category different governance proposals in three categories:
* **Critical**：`SoftwareUpgrade`, `SystemHalt`

* **Important**：`ParameterChange`, `NewCoinType`

* **Normal**：`TxTaxUsage`

Parameters in Governance module are the following:
 * `MinDeposit`: The minimum of deposit
 * `MaxDeposit Period`: Window period for deposit
 * `Voting Period`: Window period for voting
 * `MaxProposal`: The maximum number of proposal that can exist at the same time
 * `Penalty`: The proportion of the slash
 * `Veto`: The ratio that is defined by the govTallyingProcedure/Veto
 * `Threshold`: The ratio that is defined by the govTallyingProcedure/Threshold
 * `Participation`: The ratio that is defined by the govTallyingProcedure/Participation


Different parameters are set for different type of governance proposal. 

| Parameters | Critical  | Important | Normal    |
| ---------- | --------- | --------- | --------- |
| MinDeposit | 4000 iris | 2000 iris | 1000 iris |
|MaxDepositPeriod|1 Day|1 Day|1 Day|
|VotingPeriod|5 Days|5 Days|5 Days|
|MaxProposal|1|5|2|
|Participation|7/8|5/6|3/4|
|Threshold|6/7|4/5|2/3|
|Veto|1/3|1/3|1/3|
|Punishment for non-voting|0|0|0|

### Upgrade

```
"upgrade":{
            "GenesisVersion":{
                "UpgradeInfo":{
                    "ProposalID":"0",
                    "Protocol":{
                        "version":"0",
                        "software":"https://github.com/irisnet/irishub/releases/tag/v0.12.2",
                        "height":"1",
                        "threshold":"0.9000000000"
                    }
                },
                "Success":true
            }
        },
```
In `GenesisVersion` field, you could see the Upgrade information for genesis version of IRIS hub. 
* `ProposalID`: It is 0, which means it's not from an upgrade proposal
* `Protocol`: This field explains the detailed information about the underlying protocol. 
   * `version`: It indicates the version of protcol, which starts with 0 and will increase by one
   * `software`: It indicates the url for software's relese page
   * `height`:  It indicates the height which this protocol will take effect, the default value is 1 for genesisversion
   * `threshold`: It indicates the threshold of Yes votes for passing an upgrade proposal, it's 90% for IRIS hub.
   * `Success`: It indicates if the corresponding upgrade proposal is successful or not. At genesis, it's set to be true

These parameters will be changed according after upgrade proposals are passed. 



### Slashing 

```
"slashing":{
            "params":{
                "max_evidence_age":"50000",
                "signed_blocks_window":"30000",
                "min_signed_per_window":"0.5000000000",
                "double_sign_jail_duration":"172800000000000",
                "downtime_jail_duration":"86400000000000",
                "censorship_jail_duration":"172800000000000",
                "slash_fraction_double_sign":"0.0100000000",
                "slash_fraction_downtime":"0.0",
                "slash_fraction_censorship":"0.0"
            },
            "signing_infos":{

            },
            "missed_blocks":{

            },
            "slashing_periods":null
        },
```

There are three slashing conditions:

* Downtime: The window for measuring downtime slash is 30,000, which means if a validator missed 15,000 blocks, he will be jailed for 1 day. Currently, no bonded tokens will be slashed.
* Double Sign: If a validator signed different blocks at the same height, same round, he will be jailed for 2 days.
* Censorship:If a validator propose a block with invalid transactions, he will be jailed for 2 days.

### Service

```
"service":{
            "params":{
                "max_request_timeout":"100",
                "min_deposit_multiple":"1000",
                "service_fee_tax":"0.0100000000",
                "slash_fraction":"0.0010000000",
                "complaint_retrospect":"1296000000000000",
                "arbitration_time_limit":"432000000000000",
                "tx_size_limit":"4000"
            }
        },
```

* max_request_timeout: The largest delay for a service request
* min_deposit_multiple: In the service binding, need a deposit amount of the binding, the smallest deposit amount is MinDepositMultiple times the service fee
* service_fee_tax: The ratio of service fee tax is set to be 1%. For example, if a total of 1000 IRIS  to be received as service fee, then 1000*1% = 10IRIS will go to community pool. Tokens in community pool will be used for eco system development. 
* slash_fraction: if a service provider failed to respond to request within `max_request_timeout`, 1% of its deposit will be slashed.
* complaint_retrospect: If a user has question about the service results, he has 15 days to file for a arbitration.
* arbitration_time_limit: The arbitrator of IRIShub will hve 5 days to resolve it
* tx_size_limit: Service transaction size need to be less than 4000 bytes
