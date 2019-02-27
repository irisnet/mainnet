# 如何理解Genesis文件中的参数

## Genesis文件是什么？ 

Genesis文件是整个网络初始化的基础。它包含了创建创世区块的大部分信息(比如ChainID，共识的参数),初始化账户余额，每个模块的参数，validators信息。 在genesis.json文件中指明的信息是需要获得链下共识的。

## 基本参数Basic Parameters

```
 "genesis_time":"2019-03-1T06:54:00.867967765Z",
 "chain_id":"irishub",
```

- **genesis_time** : 区块链启动时间
- **chain_id** 区块链的ID

## 共识参数
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
  - `max_bytes` 组成一个区块的最大字节数
  - `max_gas` 区块最大Gas数量，默认值为-1表示无限制。如果累积的Gas超出Gas限制，该交易和之后的交易将执行失败
- **evidence** 区块内作恶证据的生命周期


## App State
### Auth 模块
Auth模块用于校验交易的有效性 
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

* gas_price_threshold：最小的gas单价
* tx_size：交易大小限制

### Stake 模块
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
- **unbonding_time**：解除绑定时间为三周
- **max_validators**: 最多验证人数目，目前设置为100

### Mint 模块
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
* inflation： 通胀系数，第一年为4%

* inflation_basement：通胀基数，第一年为20亿

  也就是说第一年一共会通胀出8千万iris



### Distribution 模块 

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

该模块负责将收集的交易费和通胀的代币分发给所有验证人和委托人。

* **Community Tax**: 贡献给社区基金的比例，第一年将设为 2%。也就是说，假如一共向奖励池中发放了1000 IRIS，那么1000*2% = 20IRIS 会进入社区基金。社区基金将用于支持生态开发。

* **Proposer Rewards**:出块者奖励将用于激励验证人即使出块。 每个出块人会获得该区块收到的手续费和通胀Token的一部分作为出块奖励。计算方法：

  ```
   proposerReward = (TxFee + InflatedToken) * (0.01 + 0.04 * sumPowerPrecommitValidators / totalBondedTokens)
  ```

出块奖励将在验证人和委托人之间按比例分配。

### Governance 模块

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

有三种类型的链上治理填：
* **Critical**：`SoftwareUpgrade`, `SystemHalt`

* **Important**：`ParameterChange`, `NewCoinType`

* **Normal**：`TxTaxUsage`

Governance模块中包含了如下参数：
* `MinDeposit`: 最小抵押金额
* `MaxDepositPeriod`: 抵押阶段的窗口期
* `VotingPeriod`: 投票阶段的窗口期
* `MaxProposal`: 该类型提议在网络中同时能存在的最大个数
* `Penalty`:如果一个账户提议进入投票阶段，他是验证人，然后该提议进入统计阶段，他还是验证人，但是他并没有投票，则会按Penalty的比例被惩罚。
* `Veto`: NoWithVeto强烈不同意投票的比例若超过了，该提案无法通过
* `Threshold`: 提案通过的要求的Yes占总投票的比例
* `Participation`: 参与投票的voting power占总共voting power的比例

不同类型的提案使用不同的参数：

| Parameters | Critical  | Important | Normal    |
| ---------- | --------- | --------- | --------- |
| MinDeposit 最少抵押数量 | 4000 iris | 2000 iris | 1000 iris |
|MaxDepositPeriod 最大抵押时间|1 Day|1 Day|1 Day|
|VotingPeriod 投票时段|5 Days|5 Days|5 Days|
|MaxProposal 最多提案数量|1|5|2|
|Participation 最低参与度|7/8|5/6|3/4|
|Threshold 通过最低比例|6/7|4/5|2/3|
|Veto 严重反对比例|1/3|1/3|1/3|
|Punishment for non-voting 不投票惩罚|0|0|0|

### Upgrade 模块

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

Upgrade模块支持区块链软件平滑升级的基础设施，通过UpgradeProposal在约定高度切换到新版的代码，并对历史版本的链上数据完全兼容。

`GenesisVersion`字段表示了IRIS hub的Upgrade模块初始信息。 

* `ProposalID`: 默认值为0,表示没有升级提案记录 
* `Protocol`: 该字段包含区块链协议的详细信息
* `version`: 表示Protocol的版本信息，从0开始递增
* `software`: 表示使用软件的发布页面超链接
* `height`: 表示该protocol从哪个高度开始生效，Genesisversion会在第一个高度就生效
* `threshold`: 表示通过升级提案至少要获得多少比例的Yes，目前默认为90%
* `Success`: 表示升级是否成功，在genesisversion中默认为true


这些参数在区块链网络启动中，会根据实际的升级提案执行情况修改。



### Slashing模块 

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

slashing模块收集验证人异常的行为，并根据异常行为的类型实施相应的惩罚机制。

验证人异常的行为主要有以下三种：

* 验证人节点长期不参与网络共识： 验证节点长期不参与网络共识，即如果一个验证节点在最近的3万个区块中有一半没有签名。它会被处罚不能参加共识1天，这样也无法获得抵押获利。
*  对同一次共识过程多次投票，并且这些投票相互矛盾：一旦验证人被发现出现了双重签名的作恶行为，则对应的验证人抵押1%的通证将被罚没。也被处罚不能参加共识2天，这样也无法获得抵押获利。
*  验证人节点通过打包不合法的交易进入区块来扰乱网络共识：一旦验证人被发现出现了打包无效交易的作恶行为，则验证节点会被处罚不能参加共识1天，这样也无法获得抵押获利。



### Service 模块

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

* max_request_timeout: 服务等待时间100s
* min_deposit_multiple: 服务绑定最小抵押金额的倍数1000，押金数量不得少于 fee* min_deposit_multiple
* service_fee_tax: 服务费的税收比例，1%。如果一共有1000IRIS的服务费收入，那么 1000*1% = 10IRIS 会进入社区基金。社区基金将用于支持生态开发。T
* slash_fraction: 惩罚百分比，1%。服务提供方需要在`MaxRequestTimeout`定义的区块高度内响应该服务请求，如果超时未响应，将从服务提供方的该服务绑定押金中扣除1%比例的押金。
* complaint_retrospect: 可提起争议最大时长15 天
* arbitration_time_limit: 争议解决最大时长，5天。
* tx_size_limit: Service相关交易大小限制， 4000 bytes

