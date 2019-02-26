# Understand Genesis Parameters of IRIS Network

## What is Genesis file?

A Genesis file is the basis for blockchain network initialization, it is used by IRIS Network to create its first (genesis) block.  The genesis file sets the initial account balances and parameters for each module, such as chain-id, consensus parameters, validator information, and etc.  Establishing a robust social consensus through the Genesis file is crucial to starting a network.


## Basic Parameters

```
 "genesis_time":"2019-03-1T06:54:00.867967765Z",
 "chain_id":"irishub",
```
- **genesis_time** : Time after which the blockchain can be started
- **chain_id** : Network ID

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
These are consensus level parameters used by Tendermint.

- **block_size**
  - `max_bytes` Maximum size of a block: *2,000,000 bytes*
  - `max_gas` Maximum gas allowd for a block: *-1 (no limit)*
- **evidence**
  - `max_age` Maximum age (of any reported wrong-doing) beyond which no punishments will be carried out: *100,000 blocks*


## App State
### Auth Module
Auth is a module for transaction authentication.
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
- `gas_price_threshold` Minimum gas price: *6000 iris-nano*
- `tx_size` Transaction size limit: *1000 bytes*

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
- `unbonding_time` Wait time for undelegation: *3 weeks*
- `max_validators`: Maximum number of validators allowed: *100*

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
- `inflation` inflation rate: *4%*
- `inflation_basement` base number for inflation: *2 billion IRIS*

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

* **Community Tax**: The ratio of community tax is set to *2%*. For example, if a total of 1000 IRIS were to be distributed among all staking pools, then 20 IRIS would go to the community pool. The usage of tokens in the community pool will be determined through on-chain governance.
* **Proposer Reward**: Proposer rewards is used for incentivizing validators to propose new blocks. If a validator is the proposer of the latest block, that validator's staking pool receives between 1% and 5% of the sum of fee rewards and inflated token as proposer reward. It is calculated as:

```
ProposerReward = (TxFee + InflatedToken) *
(0.01 + 0.04 * PrecommitValidatorsVotingPower / TotalVotingPower)
```
Proposer reward will be shared proportionally among the proposer and its delegators.

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
We put governance proposals into three categories:
* **Critical**：`SoftwareUpgrade`, `SystemHalt`
* **Important**：`ParameterChange`, `NewCoinType`
* **Normal**：`TxTaxUsage`

The following parameters are set for each category of proposals.

| Parameter | Critical  | Important | Normal    |
| ---------- | --------- | --------- | --------- |
|`deposit_period`|1 day|1 day|1 day|
|`min_deposit`| 4000 iris | 2000 iris | 1000 iris |
|`voting_period`|5 days|5 days|5 days|
|`max_num`|1|5|2|
|`threshold`|6/7|4/5|2/3|
|`veto`|1/3|1/3|1/3|
|`participation`|7/8|5/6|3/4|
|`penalty`|0|0|0|
- `deposit_period`
- `min_deposit`
- `voting_period`
- `max_num`
- `threshold`
- `veto`
- `participation`
- `penalty`

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
These parameters are managed and updated by the `SoftwareUpgrade` governance proposals and the on-chain upgrade process.
- `ProposalID`
- `version`
- `software`
- `height`
- `threshold`

### Slashing
```
"slashing":{
            "params":{
                "max_evidence_age":"51840",
                "signed_blocks_window":"34560",
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

- **Downtime**: The window for measuring downtime slash is 34,560 blocks, which means if a validator missed 17,280 blocks, he will be jailed for 1 day; currently, no bonded tokens will be slashed.
- **Double Sign**: If a validator signed different blocks at the same height/same round, he will be jailed for 2 days; and 1% of staked tokens will be slashed.
- **Censorship**: If a validator proposed a block with invalid transactions, he will be jailed for 2 days; currently, no bonded tokens will be slashed.

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

- `max_request_timeout` Maximum timeout allowed for a service request: *100 blocks*
- `min_deposit_multiple` Service provider must put down a deposit for service binding, which cannot be less than *100 times ServiceFee*
- `service_fee_tax` Service fee tax ratio is set to *1%*. For example, if a total of 1000 IRIS were to be received as service fee, then 10 IRIS will go to the service tax pool.  Service tax will be used for IRISnet ecosystem development
- `slash_fraction` Service provider will have *0.1%* of its deposit slashed it it fails to respond to a service request within `max_request_timeout`
- `tx_size_limit` Size limit for transactions in the service module: *4000 bytes*
- `complaint_retrospect` (not implemented yet)
- `arbitration_time_limit` (not implemented yet)
