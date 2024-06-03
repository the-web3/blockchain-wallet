# Near 钱包开发详细教程

## 一. Near 简介

## 1.概述

Near Protocol是一个基于区块链技术的智能合约平台，旨在为开发者提供构建去中心化应用程序（dApps）的基础设施。它致力于解决传统区块链平台所面临的扩展性、用户体验和开发者友好性等问题。

以下是Near Protocol的一些关键特点和优势：

- **高性能和低成本：** Near Protocol采用了一种称为Nightshade的新型共识机制，旨在实现高吞吐量和低延迟。这使得Near Protocol可以处理每秒数千次的交易，并且交易费用相对较低。
- **开发者友好性：** Near Protocol提供了丰富的开发工具和文档，使得开发者能够快速开始构建dApps。它支持多种编程语言，包括Rust和AssemblyScript，并提供了强大的SDK和API。
- **安全性：** Near Protocol采用了先进的加密算法和智能合约安全模型，确保用户资产和数据的安全性。
- **可扩展性：** Near Protocol的设计旨在支持大规模的应用程序和用户。它使用了一种称为Sharding的技术来实现网络的水平扩展，并且可以动态地调整网络资源以适应需求。
- **用户友好性：** Near Protocol注重用户体验，提供了简单易用的钱包和身份验证机制，使得用户可以轻松地访问和使用dApps。
- **可互操作性：** Near Protocol与其他区块链和传统金融系统具有良好的互操作性，可以轻松地与外部系统进行集成和交互。

## 2. Near Protocol 共识算法

Near Protocol使用一种称为Nightshade的共识算法。Nightshade是一种基于分片的共识协议，旨在实现高吞吐量和低延迟的区块链交易处理。

Nightshade共识算法的关键特点包括：

- **分片设计：** Nightshade使用了一种分片设计，将区块链网络划分为多个子网络（分片）。每个分片都负责处理一部分交易，从而将整个网络的负载分散到多个节点上。这种分片设计可以提高网络的扩展性和吞吐量。
- **BFT共识：** 在Nightshade中，每个分片内部使用拜占庭容错（BFT）共识算法来达成共识。这确保了每个分片的交易是安全和可靠的。
- **交叉分片通信：** Nightshade允许不同分片之间进行交互和通信，以便处理跨分片的交易和状态转换。这通过使用一种称为Relay的机制来实现，使得跨分片交易的处理变得高效和可扩展。
- **动态分片调整：** Nightshade具有动态调整分片大小和配置的能力。这意味着网络可以根据当前的负载和需求动态地调整分片的数量和大小，以适应不同的交易负载和网络条件。



# 二. 离线地址生成

```js
export function createNearAddress (seedHex: string, addressIndex: string) {
    const { key } = derivePath("m/44'/397'/0'/0'/" + addressIndex + "'", seedHex);
    const publicKey = getPublicKey(new Uint8Array(key), false).toString('hex');
    const hdWallet = {
        privateKey: key.toString('hex') + publicKey,
        publicKey: publicKey,
        address: publicKey
    };
    return JSON.stringify(hdWallet);
}


export function pubKeyToAddress({ pubKey }) {
    return pubKey;
}
```



# 三. 离线签名

```js
export async function signTransaction(params) {
    let { from,  to, amount, nonceInput, decimal, refBlock, privateKey, network } = params;
    let networkID = "mainnet";
    if (network !== "main_net") {
        networkID = "testnet";
    }
 
    const decimals = new BigNumber(10).pow(Number(decimal));
    const amountBig = new BigNumber(amount).times(decimals);
    if (amountBig.toString().indexOf(".") !== -1 ||
        amountBig.comparedTo(new BigNumber(0)) <= 0) {
        throw new Error(`参数错误: 转账数额无效(${amountBig.toString()})`);
    }
    const fromAccountID = from;
    const receiverId = to;
    const nonce = ++nonceInput;
    const amountBN: BN = new BN(amountBig.toString(16), 16);
    const actions = [transactions.transfer(amountBN)];
    const keyStore = new keyStores.InMemoryKeyStore();
    const utf8PrivKey = new Uint8Array(Buffer.from(privateKey, "hex"));
    const cb58PrivKey = nearAPI.utils.serialize.base_encode(utf8PrivKey);
    const keyPair = KeyPair.fromString(cb58PrivKey);
    await keyStore.setKey(networkID, fromAccountID, keyPair);
    const signer = new InMemorySigner(keyStore);
    const signedTx = await transactions.signTransaction(
        receiverId,
        nonce,
        actions,
        baseDecode(refBlock),
        signer,
        fromAccountID,
        networkID,
    );
    return JSON.stringify({
        txHash: Buffer.from(signedTx[0]).toString("base64"),
        signedTx: Buffer.from(signedTx[1].encode()).toString("base64"),
    });
}
```



# 四. Near 钱包开发相关的 RPC 接口

## 1.获取网络状态

- 接口作用：查看网络状态是否正常
- 请求参数：无
- 请求示范

```
curl --location 'https://rpc.mainnet.near.org' \
--header 'Content-Type: application/json' \
--data '{
  "jsonrpc": "2.0",
  "id": "dontcare",
  "method": "status",
  "params": []
}'
```

- 返回值

```json
{
    "jsonrpc": "2.0",
    "result": {
        "chain_id": "mainnet",
        "latest_protocol_version": 66,
        "node_key": null,
        "node_public_key": "ed25519:875W57nu8zeKS5QuGfzXJgc9RDPowQk2Voqafn9fqRdR",
        "protocol_version": 66,
        "rpc_addr": "127.0.0.1:4040",
        "sync_info": {
            "earliest_block_hash": "3965yAh6scsxF9p3UMNm2PecAaJdT6Ev1AXTZPsCx263",
            "earliest_block_height": 120041394,
            "earliest_block_time": "2024-05-30T04:36:47.791674186Z",
            "epoch_id": "64P8hvFvgmDVfh39zPcoU55pvbkCAid6oBkv7jCBRx1M",
            "epoch_start_height": 120239508,
            "latest_block_hash": "9TBRe3NrR4vYk3GP324dGKd7Uf5xJNpVdD4SVEtSgftE",
            "latest_block_height": 120248457,
            "latest_block_time": "2024-06-02T03:25:24.149527559Z",
            "latest_state_root": "Apu9WRmf7gu2MYRHe4NG71ko9TGa5hPKzwsB5nk2F44g",
            "syncing": false
        },
        "uptime_sec": 813575,
        "validator_account_id": null,
        "validator_public_key": null,
        "validators": [
            {
                "account_id": "lavenderfive.poolv1.near",
                "is_slashed": false
            }
        ],
        "version": {
            "build": "1.39.2",
            "rustc_version": "1.75.0",
            "version": "1.39.2"
        }
    },
    "id": "dontcare"
}
```

## 2.获取账户 nonce 和 refBlock

- 接口作用：获取账户的 nonce 和 refBlock

- 接口参数： 

  account_id: 用户地址

- 请求示范：

```
curl --location 'https://rpc.mainnet.near.org' \
--header 'Content-Type: application/json' \
--data '{
  "jsonrpc": "2.0",
  "id": "dontcare",
  "method": "query",
  "params": {
    "request_type": "view_access_key_list",
    "finality": "final",
    "account_id": "6488e3d824c6eb210b97f7f5c49d2e5f0ff63b289f2b2f861af31fa09421163d"
  }
}'
```

- 返回值

```
{
    "jsonrpc": "2.0",
    "id": "dontcare",
    "result": {
        "block_hash": "EEXYs1FQVe5QGrwqb2RkvwbnjWRT9ZwJG3DDdLfWksqm",
        "block_height": 120248014,
        "keys": [
            {
                "access_key": {
                    "nonce": 65439796000008,
                    "permission": "FullAccess"
                },
                "public_key": "ed25519:7mSqVJpb8ziMDB7yDAEajeANyDosh1WK5ksS6mCdDHRE"
            }
        ]
    }
}
```

- block_hash 为签名参数里面需要的 refBlock
- nonce 为交易签名里面需要的 nonce

## 3. 获取最新块高

- 获取状态接口里面的 latest_block_height 是最新块高
- 获取状态接口里面的 latest_block_hash 是最新块高对应的区块 Hash

## 4. 根据块高获取里面的交易

- 接口作用：根据块高获取区块里面的交易

- 接口参数： 

  block_id: 区块高度或者区块 

  Hash shard_id: shard ID

- 请求示范：

```
curl --location 'https://rpc.mainnet.near.org' \
--header 'Content-Type: application/json' \
--data '{
  "jsonrpc": "2.0",
  "id": "dontcare",
  "method": "chunk",
  "params": {"block_id": 120248938, "shard_id": 0}
}'
```

- 返回值

```json
{
   "jsonrpc":"2.0",
   "result":{
      "author":"bisontrails2.poolv1.near",
      "header":{
         
      },
      "receipts":[
         
      ],
      "transactions":[
         {
            "actions":[
               {
                  "Delegate":{
                     "delegate_action":{
                        "actions":[
                           {
                              "FunctionCall":{
                                 "args":"eyJjaGFyZ2VfZ2FzX2ZlZSI6dHJ1ZSwiY3NyZiI6IkZSWTVKdTdHdFpCYmhIUlk0dGtwWXdVYU5XWkJHSnJoOHoxdDdrTjhrbWJrIn0=",
                                 "deposit":"0",
                                 "gas":30000000000000,
                                 "method_name":"claim"
                              }
                           }
                        ],
                        "max_block_height":120648933,
                        "nonce":117664838003970,
                        "public_key":"ed25519:8oojsycvhDLoBTjRk27PGKEkK7kAVhcNone7Ljj4yw9c",
                        "receiver_id":"game.hot.tg",
                        "sender_id":"nhitsruiloddng.tg"
                     },
                     "signature":"ed25519:2mbPYzJmrJXaysBdLSCApJZpLENsb8URPGUGxxBxdzg1QXJiDJrNJpv6FYbJ1z6KYbceVCK4ugGPiKXsQTskPgd2"
                  }
               }
            ],
            "hash":"DupJETPdUhVjMq3f84mc2sRVLEqbP45aNyzU5EHmL8NN",
            "nonce":114347717419935,
            "public_key":"ed25519:87umVtFG4AL3RqvWzadNLYos2PLKb8MF57jqQwXPEy1n",
            "receiver_id":"nhitsruiloddng.tg",
            "signature":"ed25519:4M2Vrdx4k7JLcQcoo94A53YjZurEqG5uELvkQ2Fvx5bx3Uodq86yBUcJD2qSurAFq4DnRWnDH4xboSEosfKcLDoA",
            "signer_id":"0-relay.hot.tg"
         }
      ]
   },
   "id":"dontcare"
}
```

- transactions 里面的 hash 对应交易 Hash

## 5. 根据交易 Hash 获取交易详情

- 接口作用：根据块高获取区块里面的交易

- 接口参数： 

  tx_hash: 交易 Hash 

  sender_account_id: *用于确定查询哪个分片的交易* 

  wait_until: 所需的最低执行级别, 默认值为EXECUTED_OPTIMISTIC, 点击: https://docs.near.org/api/rpc/transactions#tx-status-result

- 请求示范：

```
curl --location 'https://rpc.mainnet.near.org' \
--header 'Content-Type: application/json' \
--data '{
  "jsonrpc": "2.0",
  "id": "dontcare",
  "method": "EXPERIMENTAL_tx_status",
  "params": {
    "tx_hash": "A9xF5pHriXHvqpfZXpUrMUmGWdxeJUabTLze4tiswVNk",
    "sender_account_id": "6488e3d824c6eb210b97f7f5c49d2e5f0ff63b289f2b2f861af31fa09421163d",
    "wait_until": "EXECUTED"
  }
}'
```

- 返回值

```json
{
    "jsonrpc": "2.0",
    "result": {
        "final_execution_status": "FINAL",
        "receipts": [
            {
                "predecessor_id": "6488e3d824c6eb210b97f7f5c49d2e5f0ff63b289f2b2f861af31fa09421163d",
                "receipt": {
                    "Action": {
                        "actions": [
                            {
                                "Transfer": {
                                    "deposit": "100000000000000000000000"
                                }
                            }
                        ],
                        "gas_price": "103000000",
                        "input_data_ids": [],
                        "output_data_receivers": [],
                        "signer_id": "6488e3d824c6eb210b97f7f5c49d2e5f0ff63b289f2b2f861af31fa09421163d",
                        "signer_public_key": "ed25519:7mSqVJpb8ziMDB7yDAEajeANyDosh1WK5ksS6mCdDHRE"
                    }
                },
                "receipt_id": "5TySsQ8u1BsTVVkGgKDGvt1Xvecwndo8BsJZmcfJHob4",
                "receiver_id": "3a4af7942619bbae2123e8712d90208c2276017243309494c2a52b9a268148ab"
            },
            {
                "predecessor_id": "system",
                "receipt": {
                    "Action": {
                        "actions": [
                            {
                                "Transfer": {
                                    "deposit": "12524843062500000000"
                                }
                            }
                        ],
                        "gas_price": "0",
                        "input_data_ids": [],
                        "output_data_receivers": [],
                        "signer_id": "6488e3d824c6eb210b97f7f5c49d2e5f0ff63b289f2b2f861af31fa09421163d",
                        "signer_public_key": "ed25519:7mSqVJpb8ziMDB7yDAEajeANyDosh1WK5ksS6mCdDHRE"
                    }
                },
                "receipt_id": "CAWY1ePnxB571f5sUMHQDFBGuXvtDbGKzxUD3wHCecS1",
                "receiver_id": "6488e3d824c6eb210b97f7f5c49d2e5f0ff63b289f2b2f861af31fa09421163d"
            }
        ],
        "receipts_outcome": [
            {
                "block_hash": "UavGDFQbWeJdvVTzVp5VjrpwQvDyRNxzRDuvJfE2tFb",
                "id": "5TySsQ8u1BsTVVkGgKDGvt1Xvecwndo8BsJZmcfJHob4",
                "outcome": {
                    "executor_id": "3a4af7942619bbae2123e8712d90208c2276017243309494c2a52b9a268148ab",
                    "gas_burnt": 4174947687500,
                    "logs": [],
                    "metadata": {
                        "gas_profile": [],
                        "version": 3
                    },
                    "receipt_ids": [
                        "CAWY1ePnxB571f5sUMHQDFBGuXvtDbGKzxUD3wHCecS1"
                    ],
                    "status": {
                        "SuccessValue": ""
                    },
                    "tokens_burnt": "417494768750000000000"
                },
                "proof": []
            },
            {
                "block_hash": "FTQEt5nV43HStBDxAmAfyJS1J6Z1FAEBMBt784BD29Re",
                "id": "CAWY1ePnxB571f5sUMHQDFBGuXvtDbGKzxUD3wHCecS1",
                "outcome": {
                    "executor_id": "6488e3d824c6eb210b97f7f5c49d2e5f0ff63b289f2b2f861af31fa09421163d",
                    "gas_burnt": 4174947687500,
                    "logs": [],
                    "metadata": {
                        "gas_profile": [],
                        "version": 3
                    },
                    "receipt_ids": [],
                    "status": {
                        "SuccessValue": ""
                    },
                    "tokens_burnt": "0"
                },
                "proof": []
            }
        ],
        "status": {
            "SuccessValue": ""
        },
        "transaction": {
            "actions": [
                {
                    "Transfer": {
                        "deposit": "100000000000000000000000"
                    }
                }
            ],
            "hash": "A9xF5pHriXHvqpfZXpUrMUmGWdxeJUabTLze4tiswVNk",
            "nonce": 65439796000009,
            "public_key": "ed25519:7mSqVJpb8ziMDB7yDAEajeANyDosh1WK5ksS6mCdDHRE",
            "receiver_id": "3a4af7942619bbae2123e8712d90208c2276017243309494c2a52b9a268148ab",
            "signature": "ed25519:pi8oKDRr2BuYhCRzHzbcz6pouJagMGDmZ5ANNpdWMQaH2SCq3i7V7V4vfjZ4DQsXLFESuMU9tbHPQP1kxTu4PMj",
            "signer_id": "6488e3d824c6eb210b97f7f5c49d2e5f0ff63b289f2b2f861af31fa09421163d"
        },
        "transaction_outcome": {
            "block_hash": "EWn1S79HpKHPzZCsfjEH5mbQEP8wobJDmBQRi7uXpGHb",
            "id": "A9xF5pHriXHvqpfZXpUrMUmGWdxeJUabTLze4tiswVNk",
            "outcome": {
                "executor_id": "6488e3d824c6eb210b97f7f5c49d2e5f0ff63b289f2b2f861af31fa09421163d",
                "gas_burnt": 4174947687500,
                "logs": [],
                "metadata": {
                    "gas_profile": null,
                    "version": 1
                },
                "receipt_ids": [
                    "5TySsQ8u1BsTVVkGgKDGvt1Xvecwndo8BsJZmcfJHob4"
                ],
                "status": {
                    "SuccessReceiptId": "5TySsQ8u1BsTVVkGgKDGvt1Xvecwndo8BsJZmcfJHob4"
                },
                "tokens_burnt": "417494768750000000000"
            },
            "proof": [
                {
                    "direction": "Right",
                    "hash": "NKn4J2LeQxxacLrLgyZmgV6k9gyNdzE9qk7XjwDfaBz"
                }
            ]
        }
    },
    "id": "dontcare"
}
```

- signer_id: 发送地址
- receiver_id: 接收地址
- amount：转账金额

## 6. 广播交易到区块链网络

- 接口作用：根据块高获取区块里面的交易

- 接口参数： 

  signed_tx_base64： 签名的交易串 

  wait_until：点击:https://docs.near.org/api/rpc/transactions#tx-status-result

- 请求示范：

```
curl --location 'https://rpc.mainnet.near.org' \
--header 'Content-Type: application/json' \
--data '{
  "jsonrpc": "2.0",
  "id": "dontcare",
  "method": "send_tx",
  "params": {
    "signed_tx_base64": "QAAAADY0ODhlM2Q4MjRjNmViMjEwYjk3ZjdmNWM0OWQyZTVmMGZmNjNiMjg5ZjJiMmY4NjFhZjMxZmEwOTQyMTE2M2QAZIjj2CTG6yELl/f1xJ0uXw/2OyifKy+GGvMfoJQhFj0JdctjhDsAAEAAAAAzYTRhZjc5NDI2MTliYmFlMjEyM2U4NzEyZDkwMjA4YzIyNzYwMTcyNDMzMDk0OTRjMmE1MmI5YTI2ODE0OGFixJ8F12DMSLP3fBOx0QgLuBAskIBi5pKfsUreIO6athwBAAAAAwAAgPZK4ccCLRUAAAAAAAAAKSSCeLEK9mvwobDz+wqDrQUwZf3+rtdTH3fWNSA79IEn6h0hnsh4GGg4GPXuh3e1xBNlQgacq5xbGYwKy7kxAg==",
    "wait_until": "INCLUDED_FINAL"
  }
}'
```

- 返回值

```json
{
    "jsonrpc": "2.0",
    "result": {
        "final_execution_status": "EXECUTED",
        "receipts_outcome": [
            {
                "block_hash": "UavGDFQbWeJdvVTzVp5VjrpwQvDyRNxzRDuvJfE2tFb",
                "id": "5TySsQ8u1BsTVVkGgKDGvt1Xvecwndo8BsJZmcfJHob4",
                "outcome": {
                    "executor_id": "3a4af7942619bbae2123e8712d90208c2276017243309494c2a52b9a268148ab",
                    "gas_burnt": 4174947687500,
                    "logs": [],
                    "metadata": {
                        "gas_profile": [],
                        "version": 3
                    },
                    "receipt_ids": [
                        "CAWY1ePnxB571f5sUMHQDFBGuXvtDbGKzxUD3wHCecS1"
                    ],
                    "status": {
                        "SuccessValue": ""
                    },
                    "tokens_burnt": "417494768750000000000"
                },
                "proof": []
            },
            {
                "block_hash": "FTQEt5nV43HStBDxAmAfyJS1J6Z1FAEBMBt784BD29Re",
                "id": "CAWY1ePnxB571f5sUMHQDFBGuXvtDbGKzxUD3wHCecS1",
                "outcome": {
                    "executor_id": "6488e3d824c6eb210b97f7f5c49d2e5f0ff63b289f2b2f861af31fa09421163d",
                    "gas_burnt": 4174947687500,
                    "logs": [],
                    "metadata": {
                        "gas_profile": [],
                        "version": 3
                    },
                    "receipt_ids": [],
                    "status": {
                        "SuccessValue": ""
                    },
                    "tokens_burnt": "0"
                },
                "proof": []
            }
        ],
        "status": {
            "SuccessValue": ""
        },
        "transaction": {
            "actions": [
                {
                    "Transfer": {
                        "deposit": "100000000000000000000000"
                    }
                }
            ],
            "hash": "A9xF5pHriXHvqpfZXpUrMUmGWdxeJUabTLze4tiswVNk",
            "nonce": 65439796000009,
            "public_key": "ed25519:7mSqVJpb8ziMDB7yDAEajeANyDosh1WK5ksS6mCdDHRE",
            "receiver_id": "3a4af7942619bbae2123e8712d90208c2276017243309494c2a52b9a268148ab",
            "signature": "ed25519:pi8oKDRr2BuYhCRzHzbcz6pouJagMGDmZ5ANNpdWMQaH2SCq3i7V7V4vfjZ4DQsXLFESuMU9tbHPQP1kxTu4PMj",
            "signer_id": "6488e3d824c6eb210b97f7f5c49d2e5f0ff63b289f2b2f861af31fa09421163d"
        },
        "transaction_outcome": {
            "block_hash": "EWn1S79HpKHPzZCsfjEH5mbQEP8wobJDmBQRi7uXpGHb",
            "id": "A9xF5pHriXHvqpfZXpUrMUmGWdxeJUabTLze4tiswVNk",
            "outcome": {
                "executor_id": "6488e3d824c6eb210b97f7f5c49d2e5f0ff63b289f2b2f861af31fa09421163d",
                "gas_burnt": 4174947687500,
                "logs": [],
                "metadata": {
                    "gas_profile": null,
                    "version": 1
                },
                "receipt_ids": [
                    "5TySsQ8u1BsTVVkGgKDGvt1Xvecwndo8BsJZmcfJHob4"
                ],
                "status": {
                    "SuccessReceiptId": "5TySsQ8u1BsTVVkGgKDGvt1Xvecwndo8BsJZmcfJHob4"
                },
                "tokens_burnt": "417494768750000000000"
            },
            "proof": []
        }
    },
    "id": "dontcare"
}
```

- transaction 里面的 hash 为交易发送成功返回的交易 Hash

  

# **五. 中心化钱包开发**

## **1. 离线地址生成**

- 调度签名机生成密钥对，签名机吐出公钥
- 使用公钥匙导出地址

## **2.充值逻辑**

- 获得最新块高；更新到数据库
- 从数据库中获取上次解析交易的块高做为起始块高，最新块高为截止块高，如果数据库中没有记录，说明需要从头开始扫，起始块高为 0；
- 解析区块里面的交易，to 地址是系统内部的用户地址，说明用户充值，更新交易到数据库中，将交易的状态设置为待确认。
- 所在块的交易过了确认位，将交易状态更新位充值成功并通知业务层。

## **3. 提现逻辑**

- 获取离线签名需要的参数，给合适的手续费
- 构建未签名的交易消息摘要，将消息摘要递给签名机签名
- 构建完整的交易并进行序列化
- 发送交易到区块链网络
- 扫链获取到交易之后更新交易状态并上报业务层

## **4.归集逻辑**

- 将用户地址上的资金转到归集地址，签名流程类似提现
- 发送交易到区块链网络
- 扫链获取到交易之后更新交易状态

## **5. 转冷逻辑**

- 将热钱包地址上的资金转到冷钱包地址，签名流程类似提现
- 发送交易到区块链网络
- 扫链获取到交易之后更新交易状态

## **6.冷转热逻辑**

- 手动操作转账到热钱包地址
- 扫链获取到交易之后更新交易状态



# **六 HD 钱包开发**

## **1.离线地生成和离线签名**

参考上面的代码

## **2.和链上交互的接口**

- 获取账户余额
- 获取 nonce
- 根据地址获取交易记录
- 获取预估手续费



# **七. 总结**

HD 钱包和交易所钱包不同之处有以下几点

## **1.密钥管理方式不同**

- HD 钱包私钥在本地设备，私钥用户自己控制
- 交易所钱包中心化服务器(CloadHSM, TEE 等)，私钥项目方控制

## **2.资金存在方式不同**

- HD 资金在用户钱包地址
- 交易所钱包资金在交易所热钱包或者冷钱包里面, 用户提现的时候从交易所热钱包提取

## **3.业务逻辑不一致**

- 中心化钱包：实时不断扫链更新交易数据和状态
- HD 钱包：根据用户的操作通过请求接口实现业务逻辑



# **八.附录**

- 官网：https://near.org/

- Github: https://github.com/near

- 官方文档: https://docs.near.org/

- RPC 接口文档:  https://docs.near.org/api/rpc/introduction

- 浏览器: https://explorer.near.org/
