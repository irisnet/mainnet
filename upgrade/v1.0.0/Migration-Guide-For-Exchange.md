# Migration Guide for Exchange Applications

## 1. IRIS Hub v1.0 as a Significant Milestone
IRIS Hub v1.0 will be an **incompatible** upgrade including many important features: it will merge the IBC-integrated Cosmos Stargate's latest version (Cosmos SDK v0.40.0 and Tendermint v0.34) and will also integrate and upgrade many unique features of IRISnet e.g. Coinswap(AMM), NFT, iService v2, oracle, etc.

- New features brought by [Cosmos SDK v0.40](https://github.com/cosmos/cosmos-sdk/releases/tag/v0.40.0) and [Tendermint v0.34](https://github.com/tendermint/tendermint/blob/v0.34.0/UPGRADING.md)
  - [Inter-Blockchain Communication (IBC)– cross-chain transactions](https://figment.network/resources/cosmos-stargate-upgrade-overview/#ibc)
  - [Protobuf Migration – blockchain performance & dev acceleration](https://figment.network/resources/cosmos-stargate-upgrade-overview/#proto)
  - [State Sync – minutes to sync new nodes](https://figment.network/resources/cosmos-stargate-upgrade-overview/#sync)
  - [Full-Featured Light Clients](https://figment.network/resources/cosmos-stargate-upgrade-overview/#light)
  - [Chain Upgrade Module – upgrade automation](https://figment.network/resources/cosmos-stargate-upgrade-overview/#upgrade)

- IRISnet's unique features:
   - [Coinswap](https://bifrost.irisnet.org/docs/features/coinswap.html): is the AMM (automated market maker) module implementing the protocol similar to Uniswap on IRIS Hub
   - [NFT](https://bifrost.irisnet.org/docs/features/nft.html): Non-fungible Token module supporting assets to be tokenized
   - [iService v2](https://bifrost.irisnet.org/docs/features/service.html): Interchain Service module which is refactored and optimized
   - [oracle](https://bifrost.irisnet.org/docs/features/oracle.html): oracle module based on the upgraded iService module to implement the oracle feature that can provide decentralized off-chain data onto IRIS Hub

## 2. The instruction of IRISnet v1.0's incompatibility

As one of the earliest mainnet in Cosmos ecosystem, IRISnet have used a very early version of Cosmos SDK which is v0.28.

Cosmos-SDK v0.40 adds lots of features which are incompatible with v0.2x. To integrate the revolutionary **IBC protocol**, IRIS Hub v1.0 must use Cosmos-SDK v0.40 which is incompatible with the current IRISnet mainnet.

The incompatibility of the upcoming upgrade mainly shows in the following 3 aspects:
- **API**: Cosmos-SDK v0.40 will lead to a relatively big incompatible upgrade of API comparing with Cosmos-SDK v0.28 (current IRISnet mainnet depends on)
- **Token unit and precision**: It is important to note that since the built-in Cosmos-SDK v0.40 has a precision of 6 decimal places, the most important change in IRISnet v1.0 compared to the current mainnet is:
   - The precision of IRIS token is changed from 18 digits to 6 digits
   - The smallest unit of IRIS token is changed to **uiris**
   - **1 iris = 10<sup>6</sup> uiris** after the upgrade
- **chainid**: Because this upgrade will be a non-smooth upgrade, the chainid of the upgraded IRISnet mainnet will be updated to `irishub-1`

## 3. API and SDKs
### 3.1. Cosmos-SDK v0.40 API
Almost all IRISnet functions used by exchanges come from Cosmos-SDK; the new IRISnet v1.0 mainnet will provide APIs that are exactly the same as Cosmos-SDK v0.40:

- Cosmos Legacy JSON REST API
   - Amino JSON encoding compatible with Cosmos-SDK v0.3x
   - Marked as Deprecated and will not be supported after v0.41 (expected to be about 1 year)
- gRPC protocol (recommended for new users)
   - gRPC Server: Supporting gRPC interactive protocol and using native ProtocolBuffer encoding for data
   - gRPC Gateway: automatically map the gRPC query interface to the new JSON REST interface
   - Supported by a large number of third-party tools

### 3.2. IRISnet's Multi-language SDKs (recommended for new users)
IRISnet SDKs provide application-level packaging for the Cosmos-SDK API additionally, which enhances usability and compatibility:
- Encapsulating the implementation details of transaction construction/signature/broadcast
- Providing application-oriented API which is not depends on the data structure on the chain
- Built-in private key import and management functions

## 4. Recommended Exchange Migration Path
### 4.1. The Fastest Migration Path
Cosmos-SDK v0.40 retains the Legacy JSON REST API that is mostly compatible with v0.3x. If you want to minimize the amount of code modification, the exchange can use the integration method adopted in the listing of other mainnets based on Cosmos-SDK v0.3x or v0.40 (such as: Cosmos Hub) to the new mainnet of IRISnet v1.0.

**Disadvantages**: It is expected that when the Cosmos-SDK no longer supports the Legacy API in one year, the exchange will have to migrate the code again.

### 4.2. Recommended Migration Path
If you want to migrate once for all, it is recommended to develop your own client code based on IRISnet multi-language SDKs or the gRPC protocol.

## 5. Migration Instruction
### 5.1. Using Cosmos Legacy JSON REST
#### 5.1.1. Sending transactions onto the chain (take transfer as an example)
##### 5.1.1.1. Constructing a transaction
The same code as integrating with cosmoshub-3 mainnet. The transaction structure is as follows:

```json
  {
    "type": "cosmos-sdk/StdTx",
    "value": {
      "msg": [
        {
          "type": "cosmos-sdk/MsgSend",
          "value": {
            "from_address": "iaa1rkgdpj6fyyyu7pnhmc3v7gw9uls4mnajvzdwkt",
            "to_address": "iaa1q6t5439f0rkvkzl38m0f43e0kpv3mx7x2shlq8",
            "amount": [
              {
                "denom": "uiris",
                "amount": "1000000"
              }
            ]
          }
        }
      ],
      "fee": {
        "amount": [
          {
            "denom": "uiris",
            "amount": "30000"
          }
        ],
        "gas": "200000"
      },
      "signatures": null,
      "memo": "Sent via irishub client"
    }
  }
```

where the IRISHub address prefix uses `iaa1` instead, which affects the fields:
- value.msg.value.from_adress
- value.msg.value.to_address

Denom uses `uiris` instead (1iris = 10<sup>6</sup>uiris), which affects fields:
- value.msg.value.amount.denom
- value.fee.amount.denom

##### 5.1.1.2. Signing a transaction
The same code as integrating with cosmoshub-3 mainnet. Step 1: Constructing a signature data
```json
 {
   "account_number": "0",
   "chain_id": "irishub-1",
   "fee": {
     "amount": [
       {
         "amount": "30000",
         "denom": "uiris"
       }
     ],
     "gas": "200000"
   },
   "memo": "",
   "msgs": [
     {
       "type": "cosmos-sdk/MsgSend",
       "value": {
         "from_address": "iaa1rkgdpj6fyyyu7pnhmc3v7gw9uls4mnajvzdwkt",
         "to_address": "iaa1q6t5439f0rkvkzl38m0f43e0kpv3mx7x2shlq8",
         "amount": [
           {
             "denom": "uiris",
             "amount": "1000000"
           }
         ]
       }
     }
   ],
   "sequence": "0"
 }
```

Step 2: Use the private key to sign the to-be-signed data, which is the same with the current IRISHub mainnet (note: the to-be-signed data needs to be compressed in JSON compact mode and sorted)

Step 3: Assemble the public key and the signature into the transaction body. The structure is as follows:

```json
 {
   "tx": {
     "msg": [
       {
         "type": "cosmos-sdk/MsgSend",
         "value": {
           "from_address": "iaa1rkgdpj6fyyyu7pnhmc3v7gw9uls4mnajvzdwkt",
           "to_address": "iaa1q6t5439f0rkvkzl38m0f43e0kpv3mx7x2shlq8",
           "amount": [
             {
               "denom": "uiris",
               "amount": "1000000"
             }
           ]
         }
       }
     ],
     "fee": {
       "amount": [
         {
           "denom": "uiris",
           "amount": "30000"
         }
       ],
       "gas": "200000"
     },
     "signatures": [
       {
         "pub_key": {
           "type": "tendermint/PubKeySecp256k1",
           "value": "AxGagdsRTKni/h1+vCFzTpNltwoiU7SwIR2dg6Jl5a//"
         },
         "signature": "Pu8yiRVO8oB2YDDHyB047dXNArbVImasmKBrm8Kr+6B08y8QQ7YG1eVgHi5OIYYclccCf3Ju/BQ78qsMWMniNQ=="
       }
     ],
     "memo": "Sent via irishub client"
   },
   "mode": "block"
 }
```
##### 5.1.1.3. Broadcasting a transaction
The same code as integrating with cosmoshub-3 mainnet, call `POST /txs` to send a transaction, as the example below:

```bash
curl -X POST "http://localhost:1317/txs" -H "accept: application/json" -H "Content-Type: application/json" -d "{ \"tx\": {\"msg\":[{\"type\":\"cosmos-sdk/MsgSend\",\"value\":{\"from_address\":\"iaa1rkgdpj6fyyyu7pnhmc3v7gw9uls4mnajvzdwkt\",\"to_address\":\"iaa1q6t5439f0rkvkzl38m0f43e0kpv3mx7x2shlq8\",\"amount\":[{\"denom\":\"uiris\",\"amount\":\"1000000\"}]}}],\"fee\":{\"amount\":[{\"denom\":\"uiris\",\"amount\":\"30000\"}],\"gas\":\"200000\"},\"signatures\":[{\"pub_key\":{\"type\":\"tendermint/PubKeySecp256k1\",\"value\":\"AxGagdsRTKni/h1+vCFzTpNltwoiU7SwIR2dg6Jl5a//\"},\"signature\":\"Pu8yiRVO8oB2YDDHyB047dXNArbVImasmKBrm8Kr+6B08y8QQ7YG1eVgHi5OIYYclccCf3Ju/BQ78qsMWMniNQ==\"}],\"memo\":\"Sent via irishub client\"}, \"mode\": \"block\"}"
```

#### 5.1.2. Query the status on the chain (take monitoring deposit transactions as an example)
##### 5.1.2.1. Query and parse the latest block information
```json
  {
    "block_id": {
      "hash": "DC2EEC73C327BD338EE5667827A4EECA2A2A4752B38D5669CD17EDE07CFB6F30",
      "parts": {
        "total": 1,
        "hash": "1F794EA5185AE489A3D53FD6E19A690373CAB510B981B23E672668CBE0B668E5"
      }
    },
    "block": {
      "header": {
        "version": {
          "block": "11"
        },
        "chain_id": "irishub-1",
        "height": "5",
        "time": "2021-01-18T07:29:21.918234Z",
        "last_block_id": {
          "hash": "889428AAB1975F94C39F44F1BD9C94B2A46E0BC0EFF9AD625939DCB763E82D1F",
          "parts": {
            "total": 1,
            "hash": "A9FEDF247839148459E12CD0D8A495A9EE663F7C9E719D85133B27F1D810D52B"
          }
        },
        "last_commit_hash": "35084203D333AF835637F7D9F1FEAA03554AECAB9786EECC6EB5F236156A19F1",
        "data_hash": "A2853A6749C904A8C26C5DB8CB0DD731C44EEAF2AD6AEE2E633DF8F8FD0CA04F",
        "validators_hash": "79E608E448B5B9D0784FDE890506DDE025E0E079CE10B7E01687B9C0E2DFC124",
        "next_validators_hash": "79E608E448B5B9D0784FDE890506DDE025E0E079CE10B7E01687B9C0E2DFC124",
        "consensus_hash": "048091BC7DDC283F77BFBF91D73C44DA58C3DF8A9CBC867405D8B7F3DAADA22F",
        "app_hash": "8F668541D8D565B40373E1492ED6729674539FCB1705437E309522DD491E46DC",
        "last_results_hash": "E3B0C44298FC1C149AFBF4C8996FB92427AE41E4649B934CA495991B7852B855",
        "evidence_hash": "E3B0C44298FC1C149AFBF4C8996FB92427AE41E4649B934CA495991B7852B855",
        "proposer_address": "86C89798CEA6D07FB8550AFDD8DEEA0DA52BFEF4"
      },
      "data": {
        "txs": [
          "CowBCokBChwvY29zbW9zLmJhbmsudjFiZXRhMS5Nc2dTZW5kEmkKKmlhYTE4YXduM2s3MHUwNXRsY3VsOHcycW5sNjRnMDAydWo0a2puOTNybhIqaWFhMXc5NzZhNWpyaHNqMDZkcW1yaDJ4OXF4emVsNzRxdGNtYXBrbHhjGg8KBXN0YWtlEgYxMDAwMDASZQpQCkYKHy9jb3Ntb3MuY3J5cHRvLnNlY3AyNTZrMS5QdWJLZXkSIwohA01sYgbsLpw+B9M+p6vyJCh1wfigTWbLpnhNfeDKxKIlEgQKAggBGAESEQoLCgVzdGFrZRICMTAQwJoMGkC+qpaBhJ20qboyU0HWBL0zVlW4klBXGZGsa8n2W1rxIVbq39DZUskGSI8WKNl1stM7QGhycu7YLU30z8vsg8N5"
        ]
      },
      "evidence": {
        "evidence": []
      },
      "last_commit": {
        "height": "4",
        "round": 0,
        "block_id": {
          "hash": "889428AAB1975F94C39F44F1BD9C94B2A46E0BC0EFF9AD625939DCB763E82D1F",
          "parts": {
            "total": 1,
            "hash": "A9FEDF247839148459E12CD0D8A495A9EE663F7C9E719D85133B27F1D810D52B"
          }
        },
        "signatures": [
          {
            "block_id_flag": 2,
            "validator_address": "86C89798CEA6D07FB8550AFDD8DEEA0DA52BFEF4",
            "timestamp": "2021-01-18T07:29:21.918234Z",
            "signature": "UGqkOIqSSzaW/2YzkzfoobcUIizzPcl9BVECl+jwhyMJSkrMnD3DdPUlS2Vd1IAU3u8qQOmP09+m/r5R0gp6CQ=="
          }
        ]
      }
    }
  }
```
##### 5.1.2.2. Process the transfer transactions included in the block
Step 1: Analyze tx from the block information obtained in [5.1.2.1. Query and parse the latest block information](#5121-Query-and-parse-the-latest-block-information), and decode it in base64

Step 2: Use SHA256 to hash the decoded result to get txhash

```go
tx := "CowBCokBChwvY29zbW9zLmJhbmsudjFiZXRhMS5Nc2dTZW5kEmkKKmlhYTE4YXduM2s3MHUwNXRsY3VsOHcycW5sNjRnMDAydWo0a2puOTNybhIqaWFhMXc5NzZhNWpyaHNqMDZkcW1yaDJ4OXF4emVsNzRxdGNtYXBrbHhjGg8KBXN0YWtlEgYxMDAwMDASZQpQCkYKHy9jb3Ntb3MuY3J5cHRvLnNlY3AyNTZrMS5QdWJLZXkSIwohA01sYgbsLpw+B9M+p6vyJCh1wfigTWbLpnhNfeDKxKIlEgQKAggBGAESEQoLCgVzdGFrZRICMTAQwJoMGkC+qpaBhJ20qboyU0HWBL0zVlW4klBXGZGsa8n2W1rxIVbq39DZUskGSI8WKNl1stM7QGhycu7YLU30z8vsg8N5"
txbytes, _ := base64.StdEncoding.DecodeString(tx)
txhash := sha256.Sum256(txbytes)
```

Step 3: Use the txhash to query the corresponding tx information (**Note: There are two types of transfer messages in IRISHub, `cosmos-sdk/MsgSend` and `cosmos-sdk/MsgMultiSend`**)

 ```json
  {
    "height": "5",
    "txhash": "E663768B616B1ACD2912E47C36FEBC7DB0E0974D6DB3823D4C656E0EAB8C679D",
    "data": "0A060A0473656E64",
    "raw_log": "[{\"events\":[{\"type\":\"message\",\"attributes\":[{\"key\":\"action\",\"value\":\"send\"},{\"key\":\"sender\",\"value\":\"iaa18awn3k70u05tlcul8w2qnl64g002uj4kjn93rn\"},{\"key\":\"module\",\"value\":\"bank\"}]},{\"type\":\"transfer\",\"attributes\":[{\"key\":\"recipient\",\"value\":\"iaa1w976a5jrhsj06dqmrh2x9qxzel74qtcmapklxc\"},{\"key\":\"sender\",\"value\":\"iaa18awn3k70u05tlcul8w2qnl64g002uj4kjn93rn\"},{\"key\":\"amount\",\"value\":\"1000000uiris\"}]}]}]",
    "logs": [
      {
        "events": [
          {
            "type": "message",
            "attributes": [
              {
                "key": "action",
                "value": "send"
              },
              {
                "key": "sender",
                "value": "iaa18awn3k70u05tlcul8w2qnl64g002uj4kjn93rn"
              },
              {
                "key": "module",
                "value": "bank"
              }
            ]
          },
          {
            "type": "transfer",
            "attributes": [
              {
                "key": "recipient",
                "value": "iaa1w976a5jrhsj06dqmrh2x9qxzel74qtcmapklxc"
              },
              {
                "key": "sender",
                "value": "iaa18awn3k70u05tlcul8w2qnl64g002uj4kjn93rn"
              },
              {
                "key": "amount",
                "value": "1000000uiris"
              }
            ]
          }
        ]
      }
    ],
    "gas_wanted": "200000",
    "gas_used": "69256",
    "tx": {
      "type": "cosmos-sdk/StdTx",
      "value": {
        "msg": [
          {
            "type": "cosmos-sdk/MsgSend",
            "value": {
              "from_address": "iaa18awn3k70u05tlcul8w2qnl64g002uj4kjn93rn",
              "to_address": "iaa1w976a5jrhsj06dqmrh2x9qxzel74qtcmapklxc",
              "amount": [
                {
                  "denom": "uiris",
                  "amount": "1000000"
                }
              ]
            }
          }
        ],
        "fee": {
          "amount": [
            {
              "denom": "uiris",
              "amount": "30000"
            }
          ],
          "gas": "200000"
        },
        "signatures": [],
        "memo": "",
        "timeout_height": "0"
      }
    },
    "timestamp": "2021-01-18T07:29:21Z"
  }
 ```
    
##### 5.1.2.3. Changes in transaction structure (taking transfer transactions as an example)
Method 1: using `cosmos-sdk/MsgMultiSend`. Transaction structure is mostly the same as the origin `irishub/bank/Send` structure.

- Specific changes:
  - Change the type in the original structure to the corresponding type in cosmos-sdk
- json example:
```diff
{
-   "type":"irishub/bank/StdTx",
+   "type":"cosmos-sdk/StdTx“,
  "value":{
      "msg":[
          {
-               "type":"irishub/bank/Send",
+               "type":"cosmos-sdk/MsgMultiSend",
              "value":{
                  "inputs":[
                      {
                          "address":"iaa18awn3k70u05tlcul8w2qnl64g002uj4kjn93rn",
                          "coins":[
                              {
                                  "denom":"uiris",
                                  "amount":"1000000"
                              }
                          ]
                      }
                  ],
                  "outputs":[
                      {
                          "address":"iaa1w976a5jrhsj06dqmrh2x9qxzel74qtcmapklxc",
                          "coins":[
                              {
                                  "denom":"uiris",
                                  "amount":"1000000"
                              }
                          ]
                      }
                  ]
              }
          }
      ],
      "fee":{
          "amount":[
              {
                  "denom":"uiris",
                  "amount":"30000"
              }
          ],
          "gas":"20000"
      },
      "signatures":null,
      "memo":"memo"
  }
}
```

Method 2: using `cosmos-sdk/MsgSend`, refer to [5.1.1. Sending transactions onto the chain (take transfer as an example)](#511-Sending-transactions-onto-the-chain-(take-transfer-as-an-example))

##### 5.1.2.4. Changes in query interfaces

- `GET /blocks/latest`&&`GET /blocks/{height}`

    - Specific changes:

      - The block_meta field is no longer used, the block_id in the original block_meta field structure is moved to the first level, and the header field is moved to the block
      - The num_txs and total_txs fields are no longer used, and the transaction number can be obtained by traversing the txs field

    - json example:

      ```json
      {
        "block_id": {
          "hash": "DC2EEC73C327BD338EE5667827A4EECA2A2A4752B38D5669CD17EDE07CFB6F30",
          "parts": {
            "total": 1,
            "hash": "1F794EA5185AE489A3D53FD6E19A690373CAB510B981B23E672668CBE0B668E5"
          }
        },
        "block": {
          "header": {
            "version": {
              "block": "11"
            },
            "chain_id": "irishub-1",
            "height": "5",
            "time": "2021-01-18T07:29:21.918234Z",
            "last_block_id": {
              "hash": "889428AAB1975F94C39F44F1BD9C94B2A46E0BC0EFF9AD625939DCB763E82D1F",
              "parts": {
                "total": 1,
                "hash": "A9FEDF247839148459E12CD0D8A495A9EE663F7C9E719D85133B27F1D810D52B"
              }
            },
            "last_commit_hash": "35084203D333AF835637F7D9F1FEAA03554AECAB9786EECC6EB5F236156A19F1",
            "data_hash": "A2853A6749C904A8C26C5DB8CB0DD731C44EEAF2AD6AEE2E633DF8F8FD0CA04F",
            "validators_hash": "79E608E448B5B9D0784FDE890506DDE025E0E079CE10B7E01687B9C0E2DFC124",
            "next_validators_hash": "79E608E448B5B9D0784FDE890506DDE025E0E079CE10B7E01687B9C0E2DFC124",
            "consensus_hash": "048091BC7DDC283F77BFBF91D73C44DA58C3DF8A9CBC867405D8B7F3DAADA22F",
            "app_hash": "8F668541D8D565B40373E1492ED6729674539FCB1705437E309522DD491E46DC",
            "last_results_hash": "E3B0C44298FC1C149AFBF4C8996FB92427AE41E4649B934CA495991B7852B855",
            "evidence_hash": "E3B0C44298FC1C149AFBF4C8996FB92427AE41E4649B934CA495991B7852B855",
            "proposer_address": "86C89798CEA6D07FB8550AFDD8DEEA0DA52BFEF4"
          },
          "data": {
            "txs": [
              "CowBCokBChwvY29zbW9zLmJhbmsudjFiZXRhMS5Nc2dTZW5kEmkKKmlhYTE4YXduM2s3MHUwNXRsY3VsOHcycW5sNjRnMDAydWo0a2puOTNybhIqaWFhMXc5NzZhNWpyaHNqMDZkcW1yaDJ4OXF4emVsNzRxdGNtYXBrbHhjGg8KBXN0YWtlEgYxMDAwMDASZQpQCkYKHy9jb3Ntb3MuY3J5cHRvLnNlY3AyNTZrMS5QdWJLZXkSIwohA01sYgbsLpw+B9M+p6vyJCh1wfigTWbLpnhNfeDKxKIlEgQKAggBGAESEQoLCgVzdGFrZRICMTAQwJoMGkC+qpaBhJ20qboyU0HWBL0zVlW4klBXGZGsa8n2W1rxIVbq39DZUskGSI8WKNl1stM7QGhycu7YLU30z8vsg8N5"
            ]
          },
          "evidence": {
            "evidence": []
          },
          "last_commit": {
            "height": "4",
            "round": 0,
            "block_id": {
              "hash": "889428AAB1975F94C39F44F1BD9C94B2A46E0BC0EFF9AD625939DCB763E82D1F",
              "parts": {
                "total": 1,
                "hash": "A9FEDF247839148459E12CD0D8A495A9EE663F7C9E719D85133B27F1D810D52B"
              }
            },
            "signatures": [
              {
                "block_id_flag": 2,
                "validator_address": "86C89798CEA6D07FB8550AFDD8DEEA0DA52BFEF4",
                "timestamp": "2021-01-18T07:29:21.918234Z",
                "signature": "UGqkOIqSSzaW/2YzkzfoobcUIizzPcl9BVECl+jwhyMJSkrMnD3DdPUlS2Vd1IAU3u8qQOmP09+m/r5R0gp6CQ=="
              }
            ]
          }
        }
      }
      ```

- `GET /block-results/latest`&&`GET /block-results/{height}`

    - Specific changes:

      - These two interfaces have been cancelled
      - Tendermint RPC's block_results interface can be directly called to obtain relevant data (key and value in the events field of the query result are all base64 encoded)

    - json example:
      ```json
      {
          "jsonrpc":"2.0",
          "id":-1,
          "result":{
              "height":"5",
              "txs_results":[
                  {
                      "code":0,
                      "data":"CgYKBHNlbmQ=",
                      "log":"[{"events":[{"type":"message","attributes":[{"key":"action","value":"send"},{"key":"sender","value":"iaa18awn3k70u05tlcul8w2qnl64g002uj4kjn93rn"},{"key":"module","value":"bank"}]},{"type":"transfer","attributes":[{"key":"recipient","value":"iaa1w976a5jrhsj06dqmrh2x9qxzel74qtcmapklxc"},{"key":"sender","value":"iaa18awn3k70u05tlcul8w2qnl64g002uj4kjn93rn"},{"key":"amount","value":"1000000uiris"}]}]}]",
                      "info":"",
                      "gas_wanted":"200000",
                      "gas_used":"69256",
                      "events":[
                          {
                              "type":"transfer",
                              "attributes":[
                                  {
                                      "key":"cmVjaXBpZW50",
                                      "value":"aWFhMTd4cGZ2YWttMmFtZzk2MnlsczZmODR6M2tlbGw4YzVsOW1yM2Z2",
                                      "index":true
                                  },
                                  {
                                      "key":"c2VuZGVy",
                                      "value":"aWFhMThhd24zazcwdTA1dGxjdWw4dzJxbmw2NGcwMDJ1ajRram45M3Ju",
                                      "index":true
                                  },
                                  {
                                      "key":"YW1vdW50",
                                      "value":"MTBzdGFrZQ==",
                                      "index":true
                                  }
                              ]
                          },
                          {
                              "type":"message",
                              "attributes":[
                                  {
                                      "key":"c2VuZGVy",
                                      "value":"aWFhMThhd24zazcwdTA1dGxjdWw4dzJxbmw2NGcwMDJ1ajRram45M3Ju",
                                      "index":true
                                  }
                              ]
                          },
                          {
                              "type":"message",
                              "attributes":[
                                  {
                                      "key":"YWN0aW9u",
                                      "value":"c2VuZA==",
                                      "index":true
                                  }
                              ]
                          },
                          {
                              "type":"transfer",
                              "attributes":[
                                  {
                                      "key":"cmVjaXBpZW50",
                                      "value":"aWFhMXc5NzZhNWpyaHNqMDZkcW1yaDJ4OXF4emVsNzRxdGNtYXBrbHhj",
                                      "index":true
                                  },
                                  {
                                      "key":"c2VuZGVy",
                                      "value":"aWFhMThhd24zazcwdTA1dGxjdWw4dzJxbmw2NGcwMDJ1ajRram45M3Ju",
                                      "index":true
                                  },
                                  {
                                      "key":"YW1vdW50",
                                      "value":"MTAwMDAwc3Rha2U=",
                                      "index":true
                                  }
                              ]
                          },
                          {
                              "type":"message",
                              "attributes":[
                                  {
                                      "key":"c2VuZGVy",
                                      "value":"aWFhMThhd24zazcwdTA1dGxjdWw4dzJxbmw2NGcwMDJ1ajRram45M3Ju",
                                      "index":true
                                  }
                              ]
                          },
                          {
                              "type":"message",
                              "attributes":[
                                  {
                                      "key":"bW9kdWxl",
                                      "value":"YmFuaw==",
                                      "index":true
                                  }
                              ]
                          }
                      ],
                      "codespace":""
                  }
              ],
              "begin_block_events":[
                  {
                      "type":"transfer",
                      "attributes":[
                          {
                              "key":"cmVjaXBpZW50",
                              "value":"aWFhMTd4cGZ2YWttMmFtZzk2MnlsczZmODR6M2tlbGw4YzVsOW1yM2Z2",
                              "index":true
                          },
                          {
                              "key":"c2VuZGVy",
                              "value":"aWFhMW0zaDMwd2x2c2Y4bGxydXh0cHVrZHZzeTBrbTJrdW04YW44Zjkz",
                              "index":true
                          },
                          {
                              "key":"YW1vdW50",
                              "value":"MTI2NzUyMzVzdGFrZQ==",
                              "index":true
                          }
                      ]
                  },
                  {
                      "type":"message",
                      "attributes":[
                          {
                              "key":"c2VuZGVy",
                              "value":"aWFhMW0zaDMwd2x2c2Y4bGxydXh0cHVrZHZzeTBrbTJrdW04YW44Zjkz",
                              "index":true
                          }
                      ]
                  },
                  {
                      "type":"mint",
                      "attributes":[
                          {
                              "key":"bGFzdF9pbmZsYXRpb25fdGltZQ==",
                              "value":"MjAyMS0wMS0xOCAwNzoyOToxNi43NjIyMSArMDAwMCBVVEM=",
                              "index":true
                          },
                          {
                              "key":"aW5mbGF0aW9uX3RpbWU=",
                              "value":"MjAyMS0wMS0xOCAwNzoyOToyMS45MTgyMzQgKzAwMDAgVVRD",
                              "index":true
                          },
                          {
                              "key":"bWludF9jb2lu",
                              "value":"MTI2NzUyMzU=",
                              "index":true
                          }
                      ]
                  },
                  {
                      "type":"transfer",
                      "attributes":[
                          {
                              "key":"cmVjaXBpZW50",
                              "value":"aWFhMWp2NjVzM2dycWY2djZqbDNkcDR0NmM5dDlyazk5Y2Q4amF5ZHR3",
                              "index":true
                          },
                          {
                              "key":"c2VuZGVy",
                              "value":"aWFhMTd4cGZ2YWttMmFtZzk2MnlsczZmODR6M2tlbGw4YzVsOW1yM2Z2",
                              "index":true
                          },
                          {
                              "key":"YW1vdW50",
                              "value":"MTI2NzUyMzVzdGFrZQ==",
                              "index":true
                          }
                      ]
                  },
                  {
                      "type":"message",
                      "attributes":[
                          {
                              "key":"c2VuZGVy",
                              "value":"aWFhMTd4cGZ2YWttMmFtZzk2MnlsczZmODR6M2tlbGw4YzVsOW1yM2Z2",
                              "index":true
                          }
                      ]
                  },
                  {
                      "type":"proposer_reward",
                      "attributes":[
                          {
                              "key":"YW1vdW50",
                              "value":"NjMzNzYxLjc1MDAwMDAwMDAwMDAwMDAwMHN0YWtl",
                              "index":true
                          },
                          {
                              "key":"dmFsaWRhdG9y",
                              "value":"aXZhMThhd24zazcwdTA1dGxjdWw4dzJxbmw2NGcwMDJ1ajRrOHowNzc1",
                              "index":true
                          }
                      ]
                  },
                  {
                      "type":"commission",
                      "attributes":[
                          {
                              "key":"YW1vdW50",
                              "value":"NjMzNzYxLjc1MDAwMDAwMDAwMDAwMDAwMHN0YWtl",
                              "index":true
                          },
                          {
                              "key":"dmFsaWRhdG9y",
                              "value":"aXZhMThhd24zazcwdTA1dGxjdWw4dzJxbmw2NGcwMDJ1ajRrOHowNzc1",
                              "index":true
                          }
                      ]
                  },
                  {
                      "type":"rewards",
                      "attributes":[
                          {
                              "key":"YW1vdW50",
                              "value":"NjMzNzYxLjc1MDAwMDAwMDAwMDAwMDAwMHN0YWtl",
                              "index":true
                          },
                          {
                              "key":"dmFsaWRhdG9y",
                              "value":"aXZhMThhd24zazcwdTA1dGxjdWw4dzJxbmw2NGcwMDJ1ajRrOHowNzc1",
                              "index":true
                          }
                      ]
                  },
                  {
                      "type":"commission",
                      "attributes":[
                          {
                              "key":"YW1vdW50",
                              "value":"MTE3ODc5NjguNTUwMDAwMDAwMDAwMDAwMDAwc3Rha2U=",
                              "index":true
                          },
                          {
                              "key":"dmFsaWRhdG9y",
                              "value":"aXZhMThhd24zazcwdTA1dGxjdWw4dzJxbmw2NGcwMDJ1ajRrOHowNzc1",
                              "index":true
                          }
                      ]
                  },
                  {
                      "type":"rewards",
                      "attributes":[
                          {
                              "key":"YW1vdW50",
                              "value":"MTE3ODc5NjguNTUwMDAwMDAwMDAwMDAwMDAwc3Rha2U=",
                              "index":true
                          },
                          {
                              "key":"dmFsaWRhdG9y",
                              "value":"aXZhMThhd24zazcwdTA1dGxjdWw4dzJxbmw2NGcwMDJ1ajRrOHowNzc1",
                              "index":true
                          }
                      ]
                  }
              ],
              "end_block_events":null,
              "validator_updates":null,
              "consensus_param_updates":{
                  "block":{
                      "max_bytes":"22020096",
                      "max_gas":"-1"
                  },
                  "evidence":{
                      "max_age_num_blocks":"100000",
                      "max_age_duration":"172800000000000",
                      "max_bytes":"1048576"
                  },
                  "validator":{
                      "pub_key_types":[
                          "ed25519"
                      ]
                  }
              }
          }
      }
      ```
      
- `GET /txs`&&`GET /txs/{hash}`

    - Specific changes:

      - Tags are no longer used; use the events field instead
      - The result field is no longer used, and the field in the original result is moved to the first level
      - The coin_flow field is no longer used

    - json examples:
      ```json
      {
        "height": "5",
        "txhash": "E663768B616B1ACD2912E47C36FEBC7DB0E0974D6DB3823D4C656E0EAB8C679D",
        "data": "0A060A0473656E64",
        "raw_log": "[{\"events\":[{\"type\":\"message\",\"attributes\":[{\"key\":\"action\",\"value\":\"send\"},{\"key\":\"sender\",\"value\":\"iaa18awn3k70u05tlcul8w2qnl64g002uj4kjn93rn\"},{\"key\":\"module\",\"value\":\"bank\"}]},{\"type\":\"transfer\",\"attributes\":[{\"key\":\"recipient\",\"value\":\"iaa1w976a5jrhsj06dqmrh2x9qxzel74qtcmapklxc\"},{\"key\":\"sender\",\"value\":\"iaa18awn3k70u05tlcul8w2qnl64g002uj4kjn93rn\"},{\"key\":\"amount\",\"value\":\"1000000uiris\"}]}]}]",
        "logs": [
          {
            "events": [
              {
                "type": "message",
                "attributes": [
                  {
                    "key": "action",
                    "value": "send"
                  },
                  {
                    "key": "sender",
                    "value": "iaa18awn3k70u05tlcul8w2qnl64g002uj4kjn93rn"
                  },
                  {
                    "key": "module",
                    "value": "bank"
                  }
                ]
              },
              {
                "type": "transfer",
                "attributes": [
                  {
                    "key": "recipient",
                    "value": "iaa1w976a5jrhsj06dqmrh2x9qxzel74qtcmapklxc"
                  },
                  {
                    "key": "sender",
                    "value": "iaa18awn3k70u05tlcul8w2qnl64g002uj4kjn93rn"
                  },
                  {
                    "key": "amount",
                    "value": "1000000uiris"
                  }
                ]
              }
            ]
          }
        ],
        "gas_wanted": "200000",
        "gas_used": "69256",
        "tx": {
          "type": "cosmos-sdk/StdTx",
          "value": {
            "msg": [
              {
                "type": "cosmos-sdk/MsgSend",
                "value": {
                  "from_address": "iaa18awn3k70u05tlcul8w2qnl64g002uj4kjn93rn",
                  "to_address": "iaa1w976a5jrhsj06dqmrh2x9qxzel74qtcmapklxc",
                  "amount": [
                    {
                      "denom": "uiris",
                      "amount": "1000000"
                    }
                  ]
                }
              }
            ],
            "fee": {
              "amount": [
                {
                  "denom": "uiris",
                  "amount": "30000"
                }
              ],
              "gas": "200000"
            },
            "signatures": [],
            "memo": "",
            "timeout_height": "0"
          }
        },
        "timestamp": "2021-01-18T07:29:21Z"
      }
      ```

- `GET /bank/accounts/{address}`

    - Specific changes:

      - This interface has been cancelled
      - The coins field in the original interface can be queried through the /bank/balances/{address} interface
      - Other fields in the original interface can be queried through the /auth/accounts/{address} interface

    - json example:
      ```json
      // /bank/balances/{address}
      {
        "height": "98",
        "result": [
          {
            "denom": "node0token",
            "amount": "1000000000"
          },
          {
            "denom": "uiris",
            "amount": "4999999999999899899990"
          }
        ]
      }
      
      // /auth/accounts/{address}
      {
        "height": "142",
        "result": {
          "type": "cosmos-sdk/BaseAccount",
          "value": {
            "address": "iaa18awn3k70u05tlcul8w2qnl64g002uj4kjn93rn",
            "public_key": {
              "type": "tendermint/PubKeySecp256k1",
              "value": "A01sYgbsLpw+B9M+p6vyJCh1wfigTWbLpnhNfeDKxKIl"
            },
            "sequence": "2"
          }
        }
      }
      
      ```

### 5.2. Using IRISnet SDK

IRISHub currently have SDKs in two popular languages. For detailed usage documents, please refer to:

- [IRISHub SDK GO](https://github.com/irisnet/irishub-sdk-go/blob/master/README.md)
- [IRISHub SDK JS](https://sdk-js.irisnet.org/)

