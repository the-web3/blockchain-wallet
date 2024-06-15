# Tron 钱包开发详细教程

# 一. Tron 简介

Tron（波场） 是一个兼容 EVM 的区块链平台,  Tron的技术架构由三层组成：

- **存储层**：包括区块存储和状态存储，支持多种存储机制。
- **核心层**：实现了智能合约、账户管理和共识机制（目前使用的是DPoS，即委托权益证明）。
- **应用层**：为开发者提供API和SDK，支持开发去中心化应用（DApps）。

## **1.主要组件和功能**

- **Tron虚拟机（TVM）**：兼容以太坊虚拟机（EVM），使开发者可以轻松将以太坊上的应用迁移到Tron上。
- **智能合约**：支持Solidity语言编写的智能合约。
- **TRC-20和TRC-721标准**：TRC-20是Tron的代币标准，类似于以太坊的ERC-20；TRC-721则是非同质化代币标准，类似于ERC-721。

## **2.共识机制**

Tron采用委托权益证明（DPoS）机制。TRX持有者可以投票选举超级代表（Super Representatives，SR），这些SR负责验证交易和维护网络的安全。

## **3.生态系统**

Tron生态系统包含多个重要组件和项目：

- **TronLink**：官方钱包，支持TRX和TRC-20代币的存储和交易。
- **JustSwap**：去中心化交易所，支持TRC-20代币的交易。
- **Sun Network**：侧链扩展方案，旨在提升Tron主网的扩展性。
- **BitTorrent**：Tron收购的去中心化文件共享协议，与Tron网络深度集成。
- **DApps**：大量去中心化应用在 Tron 平台上运行。

## **4. 代币（TRX）**

Tron的原生代币是TRX，主要用于以下几个方面：

- **交易费用**：支付网络上的交易费用。
- **Staking和投票**：TRX持有者可以质押代币并参与超级代表的选举。
- **DApp支付**：在Tron平台上的去中心化应用中进行支付和交易。

# 二. 离线地址生成

```javascript
export function createTrxAddress (seedHex: string, addressIndex: string): string {
  const node = bip32.fromSeed(Buffer.from(seedHex, 'hex'));
  const child = node.derivePath("m/44'/195'/0'/0/" + addressIndex + '');
  const privateKey = child.privateKey.toString('hex');
  const publickKey = child.publicKey.toString('hex');
  const address = pkToAddress(privateKey).toString('hex');
  return JSON.stringify({
    privateKey,
    publickKey,
    address
  });
}
```

# 三. 离线签名

```javascript
export async function signTrxTransaction (params: any): Promise<string> {
  const { privateKey, from, to, amount, energyLimit, energyPrice, refBlock, tokenAddress, tokenTRC10, expiration } = params;
  const time = Date.now();
  const feeLimit = energyLimit * energyPrice; // SUN
  const rawTx = buildTronTransaction({
    from,
    to,
    amount,
    feeLimit,
    refBlock,
    tokenAddress: tokenAddress || NULLTOKENADDR,
    tokenTRC10,
    expiration,
    permissionId: 0,
    time
  });
  const signedTx: any = signTx(privateKey, rawTx);
  return signedTx.hex;
}
```

**注意：完整代码可以联系 The Web3 社区帮助您，联系方式 github 上和 The Web3Dao 公众号上面有。**

# 四.  Tron 钱包开发相关接口

## 1.获取账户信息

- 请求示范

```bash
curl --location 'https://api.trongrid.io/wallet/getaccount' \
--header 'Content-Type: application/json' \
--data '{
  "address": "TZ4UXDV5ZhNW7fb2AMSbgfAEZ7hWsnYS2g",
  "visible": true
}'
```

- 返回值

```json
{
    "address": "TZ4UXDV5ZhNW7fb2AMSbgfAEZ7hWsnYS2g",
    "balance": 68623166,
    "create_time": 1675582485000,
    "latest_consume_time": 1712481906000,
    "net_window_size": 28800000,
    "net_window_optimized": true,
    "account_resource": {
        "latest_consume_time_for_energy": 1714295640000,
        "energy_window_size": 28800000,
        "energy_window_optimized": true
    },
    "owner_permission": {},
    "active_permission": [],
    "frozenV2": [
        {},
        {
            "type": "ENERGY"
        },
        {
            "type": "TRON_POWER"
        }
    ],
    "assetV2": [
        {
            "key": "1004977",
            "value": 8888880000
        },
        {
            "key": "1005026",
            "value": 8888880000
        }
    ],
    "free_asset_net_usageV2": [
        {
            "key": "1004977",
            "value": 0
        },
        {
            "key": "1005026",
            "value": 0
        }
    ],
    "asset_optimized": true
}
```

- balance： 账户余额
- account_resource：账户资源

## 2. 根据账户地址获取交易信息

- https://developers.tron.network/reference/get-transaction-info-by-account-address
- https://developers.tron.network/reference/get-trc20-transaction-info-by-account-address

## 3. 获取 energyPrice

- 请求示范

```bash
curl --location 'https://api.trongrid.io/wallet/getenergyprices' --data ''
```

- 返回值

```json
{
    "prices": "0:100,1542607200000:20,1544724000000:10,1606240800000:40,1613044800000:140,1635422400000:280,1670133600000:420"
}
```

- prices - 字符串：所有历史能源单价信息。每次单价变化由逗号分隔。冒号前是毫秒时间戳，冒号后是以 sun 为单位的能源单价。

## 4. 获取最新块高

- 请求示范

```bash
curl --location 'https://api.trongrid.io/walletsolidity/getblock' --data ''
```

- 返回值

```json
{
    "blockID": "0000000003b6b891043be54ac0ae1ef579fad5e6e1732cb0ac4237a873b8453e",
    "block_header": {
        "raw_data": {
            "number": 62306449,
            "txTrieRoot": "7cb6ddff8ada79c91baf6667cc8ead757b39205a8dc8ae097298dbb0587bddb7",
            "witness_address": "4114f2c09d3de3fe82a71960da65d4935a30b24e1f",
            "parentHash": "0000000003b6b890d8e1d886520dcad1820f4313a435ad593a80c549ea883054",
            "version": 30,
            "timestamp": 1717563117000
        },
        "witness_signature": "82cd722206df1bd86be854605514f5a45f4914dba2097de6fd59b8c45a07721d5af9a60ec567ecdd48921aec5d458fa26f359315b6561b3d98bedd11910f777601"
    }
}
```

- number：为最新块高，也可以使用 curl --location 'https://api.trongrid.io/wallet/getnowblock'  --data ''

## 4. 根据区块号获取里面的交易

- https://developers.tron.network/reference/gettransactioninfobyblocknum-1

- https://developers.tron.network/reference/gettransactioncountbyblocknum

   获取交易笔数

## 5.根据交易 Hash 获取交易详情

- https://developers.tron.network/reference/gettransactionbyid
- https://developers.tron.network/reference/gettransactioninfobyid-1

## 6. 广播交易

- 请求示范

```bash
curl --location 'https://api.trongrid.io/wallet/broadcasthex' \
--header 'Content-Type: application/json' \
--data '{
  "transaction": "0A83010A0261B72208C487C467E75A3B8E40A4B7D099FE315A65080112610A2D747970652E676F6F676C65617069732E636F6D2F70726F746F636F6C2E5472616E73666572436F6E747261637412300A1541CF55FB918FB606E00B521868D06B02B261CF18E0121541EDFE83892A43773E7FF65440B72EB3A6AFE37A9B180170A4FD9896FE31124194602BD38AECC0E0AA8F4E417B5C969C840D6D9A4B2310E35A0162EBB7BFED4238AF68321B82C48EE0DCE0F42675DE477A388DA45E2DF9ABFF3A324D5B1E377C00"
}'
```

- 返回值

```javascript
{
    "result": false,
    "code": "TRANSACTION_EXPIRATION_ERROR",
    "txid": "cfc03ca018cb79aff19bafe67a81221f2295b2de3402ca4b92293ff3c78e46fe",
    "message": "Transaction expired",
    "transaction": "{\"raw_data\": {\"ref_block_bytes\": \"61b7\",\"ref_block_hash\": \"c487c467e75a3b8e\",\"expiration\": 1717503794084,\"contract\": [{\"type\": \"TransferContract\",\"parameter\": {\"type_url\": \"type.googleapis.com/protocol.TransferContract\",\"value\": \"0a1541cf55fb918fb606e00b521868d06b02b261cf18e0121541edfe83892a43773e7ff65440b72eb3a6afe37a9b1801\"}}],\"timestamp\": 1717496594084},\"signature\": [\"94602bd38aecc0e0aa8f4e417b5c969c840d6d9a4b2310e35a0162ebb7bfed4238af68321b82c48ee0dce0f42675de477a388da45e2df9abff3a324d5b1e377c00\"]}"
}
```

- txid： 交易 hash

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

- Official Website:  [https://www.tron.network](https://www.tron.network/)
- Github:  https://github.com/tronprotocol
- Tron docs:  https://developers.tron.network/
- Tron 接口文档: https://developers.tron.network/reference/background
- Tron 浏览器: https://tronscan.org/
- 关于 Tron 的问题列表：https://developers.tron.network/docs/faq