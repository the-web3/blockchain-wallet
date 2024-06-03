# Stacks 钱包开发详细教程

## 一. Stacks 简介

Stacks 是一个开源项目，旨在通过将智能合约和去中心化应用（dApps）引入比特币区块链，从而扩展比特币的功能。它通过名为 "Proof of Transfer" (PoX) 的共识机制，将新一代的区块链与比特币连接起来。

## **1.核心组件**

**Stacks 区块链：**Stacks 区块链是一个独立的区块链，与比特币紧密耦合。它通过 PoX 共识机制利用比特币的安全性和稳定性来实现自身的去中心化和安全性。

**Proof of Transfer (PoX)：** PoX 是一种独特的共识机制，允许 Stacks 区块链重用比特币的算力。矿工通过在比特币网络上支出 BTC 来竞选新的 Stacks 区块的创建权，从而在 Stacks 网络上获取奖励。

**Clarity 智能合约语言：**Clarity 是 Stacks 网络上使用的智能合约语言，专为安全和可预测性而设计。它是一种非图灵完备的语言，这意味着它在执行时的行为是可预测的，并且可以在执行前完全分析其运行结果。

## 2.POX 共识算法详解

Proof of Transfer (PoX) 是一种创新的共识机制，旨在利用现有区块链（如比特币）的安全性来保护新兴区块链（如 Stacks）的网络。PoX 通过让矿工在比特币区块链上支出 BTC 来竞选新块的生成权，并将这些 BTC 奖励给参与者，从而实现新旧区块链的连接和安全共享。

**2.1.参与者**

- **矿工（Miners）**：在 PoX 机制中，矿工通过支出比特币来竞争 Stacks 区块的创建权。
- **堆叠者（Stackers）**：持有 STX 代币并愿意参与 Stacking 的用户。他们通过锁定 STX 代币来支持网络的稳定性和安全性，并获得 BTC 奖励。

**2.2.工作流程**

- **矿工提交 BTC**：矿工将一定数量的比特币（BTC）发送到指定的比特币地址。这些比特币被称为“竞选比特币”。
- **选择区块创建者**：使用加权随机选择算法，从所有提交了竞选比特币的矿工中选出一个矿工来创建下一个 Stacks 区块。
- **分发 BTC 奖励**：选出的矿工创建新的 Stacks 区块，且之前提交的 BTC 奖励会分发给参与 Stacking 的堆叠者。

**2.3.主要功能**

- **智能合约：** 通过 Clarity 编写和执行智能合约，使开发者能够创建复杂的 dApps，同时利用比特币的安全性。
- **去中心化应用（dApps）：**开发者可以在 Stacks 平台上创建和部署去中心化应用，这些应用可以与比特币直接交互，并使用 BTC 作为主要货币。
- **Stacking：**Stacks 代币持有者可以通过参与 Stacking 来帮助保护网络并获得 BTC 奖励。Stacking 类似于质押，但它通过锁定 STX 代币并参与 PoX 机制来实现。

## **3.Stacks 代币（STX）**

STX 是 Stacks 区块链的原生代币，用于支付交易费用、执行智能合约以及参与 Stacking。

## **4. 实际应用**

Stacks 已经有多个实际应用和项目在其平台上运行，包括去中心化金融（DeFi）、NFT 市场以及社交媒体平台。这些应用展示了 Stacks 如何利用比特币的安全性和 Clarity 智能合约的灵活性来创建创新的区块链解决方案。

# 二. 离线地址生成

```js
export function createAddress(params: any): any {
    const { seedHex, addressIndex } = params;
    const node = bip32.fromSeed(Buffer.from(seedHex, 'hex'));
    const childKey = node.derivePath("m/44'/5757'/0'/0/" + addressIndex + '');

    const derivePubK = childKey.publicKey.toString('hex');
    const publicKey = Buffer.from(derivePubK, 'hex');

    let uncompressedPublicKey = new Uint8Array(65);
    secp256k1.publicKeyConvert(new Uint8Array(publicKey), false, uncompressedPublicKey);
    uncompressedPublicKey = Buffer.from(uncompressedPublicKey);
    // @ts-ignore
    const address = getAddressFromPrivateKey(Buffer.from(childKey.privateKey).toString('hex') + "01", TransactionVersion.Mainnet )
    return {
        privateKey: Buffer.from(childKey.privateKey).toString('hex') + "01",
        address: address
    }
}
```

# 三. 离线签名

```js
export async function signTransaction(params){
    const {
        to, amount, fee, nonce, memo, decimal, privKey, network
    } = params;
    const privateKey = privKey;
    const calcAmount = new BigNumber(amount).times(new BigNumber(10).pow(decimal)).toNumber();
    if (calcAmount % 1 !== 0) throw new Error("decimal 无效");
    const stacksNet = network === "main_net" ? new StacksMainnet() : new StacksTestnet();
    const txOptions = {
        recipient: to,
        amount: calcAmount,
        senderKey: privateKey,
        network: stacksNet,
        memo: memo,
        nonce: nonce,
        fee: fee,
        anchorMode: AnchorMode.Any
    };
    const transaction = await makeSTXTokenTransfer(txOptions);
    return transaction.serialize().toString('hex')
}
```

# 四. STX 钱包开发相关的 RPC 接口

## 1.获取最新块高

- 接口作用：获取最新块高和检查公链同步状态
- 接口参数：无
- 请求示范

```
curl --location 'https://api.mainnet.hiro.so/extended/v2/blocks/'
```

- 返回值

```json
{
    "limit": 20,
    "offset": 0,
    "total": 152310,
    "results": [
        {
            "canonical": true,
            "height": 152310,
            "hash": "0xf24fc095620160c54389b881a9b3c5d8b298d94c6ca0e17e48b8367824248d74",
            "block_time": 1717240458,
            "block_time_iso": "2024-06-01T11:14:18.000Z",
            "index_block_hash": "0x1d5058157c3207cb06d3d4e8507ff1f081353da8946d479bb20913e50d214803",
            "parent_block_hash": "0x66400b855151626f2643fc4c1b58d0a873d03803045967d4bee5de976704b0a8",
            "burn_block_time": 1717240458,
            "burn_block_time_iso": "2024-06-01T11:14:18.000Z",
            "burn_block_hash": "0x0000000000000000000028e67320af6ad8d2ae6f1c7b95f98eb6f9177920e526",
            "burn_block_height": 846050,
            "miner_txid": "0x3b6bfb369e8a0cc4eca3580e3d47eb01caca71ff7c5f31f3a6d98d9a5701d058",
            "parent_microblock_hash": "",
            "parent_microblock_sequence": -1,
            "txs": [
                "0x2a3e8ba75e6571d8d7e4bc0f3bb0d0d5e624a319a8bf1534eb0bf8ec502c951b",
            ],
            "microblocks_accepted": [],
            "microblocks_streamed": [],
            "execution_cost_read_count": 428,
            "execution_cost_read_length": 91935,
            "execution_cost_runtime": 7901829,
            "execution_cost_write_count": 10,
            "execution_cost_write_length": 418,
            "microblock_tx_count": {}
        }
    ]
}
```

- height 第一项为最新块高

## 2. 获取账户签名交易的 Nonce 和账户余额

- 接口作用：获取交易签名的 nonce
- 接口参数: 需要获取 Nonce 的账户地址
- 请求示范

```
curl --location 'https://stacks-node-api.mainnet.stacks.co/v2/accounts/SP11RPVAKGYF7N7EM9XWH926SF8ZTY7FPXGWCVWC1'
```

- 返回值

```json
{
    "balance":"0x000000000000000000000000004ac0b8",
    "locked":"0x00000000000000000000000000000000",
    "unlock_height":0,
    "nonce":1,
    "balance_proof":"",
    "nonce_proof":""
}
```

- balance：账户余额
- nonce：签名交易体参数

## 3. 根据区块号获取交易

- 接口作用：根据区块号获取区块链信息
- 接口参数: 区块高度
- 请求示范

```
curl --location 'https://api.mainnet.hiro.so/extended/v1/block/by_height/152290'
```

- 返回值

```json
{
    "canonical": true,
    "height": 152290,
    "hash": "0x19a26d0a71cdb24fdfae40132dd611dd9a03c5b8fe1b6a5b63316b3ac8adff63",
    "block_time": 1717227375,
    "block_time_iso": "2024-06-01T07:36:15.000Z",
    "index_block_hash": "0x2de372e030c5c2c0b5ae03859bcb6d17225c40fd86807473e69911d52841ec72",
    "parent_block_hash": "0x8fed97eb49751646e95edd9f06a1ff0e0f30d76d112eb60572c8086c8a4d6dff",
    "burn_block_time": 1717227375,
    "burn_block_time_iso": "2024-06-01T07:36:15.000Z",
    "burn_block_hash": "0x00000000000000000001726c4f29eb9e01f96db8e12bc9813fcdb4e3a4dff9c2",
    "burn_block_height": 846029,
    "miner_txid": "0x91e4ef60e9f1382bb55003bfbc86c9e56fdb6cd353aade048dcb1960d53dbe8e",
    "parent_microblock_hash": "",
    "parent_microblock_sequence": -1,
    "txs": [
        "0x118c2b5e431051bf83319c378a1ee8d7f3ff22c744b372e250f86f36a482710f",
        "0xa226fb79920a8d4d90d23fac29249aa87b45daaeb4480518cf9faba6e8f843d0",
        "0x62f2cf634fa9b53b7efa04dccb544de1e07cb5c4eaf53b71da18d0447c967a2d",
        "0x9e5b3caf459258904b2b7ac016d37110c8d6eaec384286c5f8768a968bea2671",
        "0x7003ec9ec330b9cbb45e7d4611cc5c6d791fd93274b9641a21a807fe44061b96",
        "0xdbd9037d227d05aa88e7fc07f3b8486f849e6b1fce757ad7dab7ab3700b48ba0",
        "0x7c5b73206cada60ec1e6c96a6b3de47590eb3479db23ee6d115b20af4ce20a0c",
        "0xf14c72d1a06b13df90c2297db7e9ec7ac79095366035479c59f4fac25e9cdfd9",
        "0xfad9c58dbd4d2b7ab591e9f062ad49e75812308a8bc3c4e4d858f4bae9e854cf",
        "0x9f8ccbef3cbd3aea5542de52d2e362483dc5909696b82e213073c060906bc757",
        "0x34975a203ba98944bb5d3446802b04ab3a4190a58fb0cae411ce85a0b984f115",
        "0xdfaca9eaefe4f8627646b5594a62eb68d3107ea96e00e2f4a4197888845e08fa",
        "0x97aa470e533d3db74f903031d4dbfa7802eba60894386c1de9c7e26c6ce8279d",
        "0xec7e1a43a52b6cc2d6f42040cb4304998d2746fc7bbc585e9e8238645bef0047",
        "0x7706b87bd2d034020ab45cfefaf1c3e310d836d0e1d62d74ab59fc6c69651a50",
        "0x9b99dda174b35143685cf5dfe1e6af36a5ed7adc65b0d9b38eba4c37b654ed2b",
        "0x7612542575cecd6067882a9245bf63d69ddb62d32ca8cb821dbd64821d998e97",
        "0x21e0a53755a1fff42a49ac1a86f681a9ddd3143a1e8655b784e245ca76d98e42",
        "0xd63a7d74c59969856e2eb6328fe3baab6ae142b704ac1625d458b24782592de9"
    ],
    "microblocks_accepted": [],
    "microblocks_streamed": [],
    "execution_cost_read_count": 1123,
    "execution_cost_read_length": 5070876,
    "execution_cost_runtime": 31806543,
    "execution_cost_write_count": 53,
    "execution_cost_write_length": 9326,
    "microblock_tx_count": {}
}
```

- txs：交易列表，里面是交易 Hash

## 4.根据交易 Hash 获取交易

- 接口作用：根据交易 Hash 获取交易
- 接口参数:  交易Hash
- 请求示范

```
curl --location 'https://api.mainnet.hiro.so/extended/v1/tx/ed917ad07e01653ea17644299f1ef4f2cfe6e46d887979d9ab7a6369ad883918'
```

- 返回值

```json
{
    "tx_id": "0xed917ad07e01653ea17644299f1ef4f2cfe6e46d887979d9ab7a6369ad883918",
    "nonce": 1,
    "fee_rate": "21700",
    "sender_address": "SP11RPVAKGYF7N7EM9XWH926SF8ZTY7FPXGWCVWC1",
    "sponsored": false,
    "post_condition_mode": "deny",
    "post_conditions": [],
    "anchor_mode": "any",
    "tx_status": "pending",
    "receipt_time": 1717241359,
    "receipt_time_iso": "2024-06-01T11:29:19.000Z",
    "tx_type": "token_transfer",
    "token_transfer": {
        "recipient_address": "SP2J1YDB3A9KYJQ7EZ70PXJKSQY9H78K5Q6CGH8VX",
        "amount": "300000",
        "memo": "0x746865207765623320636f7572736500000000000000000000000000000000000000"
    }
}
```

- sender_address: 转出地址
- recipient_address：转入地址
- amount：转账金额
- memo：转账备注
- tx_type: 转账类型

## 5.根据地址获取交易列表

- 接口作用：根据地址获取交易列表
- 接口参数:  地址
- 请求示范

```
curl --location 'https://api.mainnet.hiro.so/extended/v2/addresses/SP11RPVAKGYF7N7EM9XWH926SF8ZTY7FPXGWCVWC1/transactions'
```

- 返回值

```json
{
    "limit": 20,
    "offset": 0,
    "total": 2,
    "results": [
        {
            "tx": {
                "tx_id": "0xd825bfc0559c2de86e93b587747682b7aba3555fce092fc729fe4a7b29f5fe01",
                "nonce": 0,
                "fee_rate": "1000",
                "sender_address": "SP11RPVAKGYF7N7EM9XWH926SF8ZTY7FPXGWCVWC1",
                "tx_type": "token_transfer",
                "token_transfer": {
                    "recipient_address": "SP2J1YDB3A9KYJQ7EZ70PXJKSQY9H78K5Q6CGH8VX",
                    "amount": "100000",
                    "memo": "0x6d656d6f000000000000000000000000000000000000000000000000000000000000"
                }
            },
            "stx_sent": "101000",
            "stx_received": "0",
            "events": {
                "stx": {
                    "transfer": 1,
                    "mint": 0,
                    "burn": 0
                },
                "ft": {
                    "transfer": 0,
                    "mint": 0,
                    "burn": 0
                },
                "nft": {
                    "transfer": 0,
                    "mint": 0,
                    "burn": 0
                }
            }
        },
        {
            "tx": {
                "tx_id": "0xfc9f84ba538e1b63643655fac219d343fffc845d47cb9b4cb9cba90a406e4654",
                "nonce": 20350,
                "fee_rate": "300000",
                "sender_address": "SP2ADT4TAV92SPBNP3WE9GPJ12F6CKGWMEJ1H1BB9",
                "tx_type": "token_transfer",
                "token_transfer": {
                    "recipient_address": "SP11RPVAKGYF7N7EM9XWH926SF8ZTY7FPXGWCVWC1",
                    "amount": "5000000",
                    "memo": "0x31313030000000000000000000000000000000000000000000000000000000000000"
                }
            },
            "stx_sent": "300000",
            "stx_received": "5000000",
            "events": {
                "stx": {
                    "transfer": 1,
                    "mint": 0,
                    "burn": 0
                },
                "ft": {
                    "transfer": 0,
                    "mint": 0,
                    "burn": 0
                },
                "nft": {
                    "transfer": 0,
                    "mint": 0,
                    "burn": 0
                }
            }
        }
    ]
}
```

- sender_address: 转出地址
- recipient_address：转入地址
- amount：转账金额
- memo：转账备注
- tx_type: 转账类型

## 6.发送交易到区块链网络

- 接口作用：发送交易到区块链网络
- 接口参数: 签名的交易体
- 请求示范

```
curl --location 'https://stacks-node-api.mainnet.stacks.co/v2/transactions' \
--header 'Content-Type: application/json' \
--data '{
    "tx":"00000000010400438b6d53879e7a9dd44f791488d97a3faf1df6ec000000000000000100000000000054c40001707bd733bac6db46e2c6dccda2fd00da2959cbe3c3d0237c62c2bc85ff231d056685498b5b6354636517625ebcd18fbcbc59746b133656471b2e74b43367f025030200000000000516a41f35635267e95ceef9c16eca79bf9313a265b900000000000493e0746865207765623320636f7572736500000000000000000000000000000000000000" 
}'
```

- 返回值

```
"ed917ad07e01653ea17644299f1ef4f2cfe6e46d887979d9ab7a6369ad883918"
```

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

- Github: https://github.com/stacks-network

- 官方文档: https://wdocs.stacks.co/

- 浏览器: https://explorer.hiro.so/

- Stackjs: https://github.com/stacksjs

- 官方接口文档：

  https://docs.stacks.co/stacks-101/api

  https://docs.hiro.so/api/