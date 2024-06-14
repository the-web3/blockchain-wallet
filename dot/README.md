# DOT 钱包开发详细教程

# 一.  Polkadot 简介

Polkadot 是一个由 Web3 基金会支持的开源多链区块链平台，旨在解决现有区块链技术的可扩展性、互操作性和升级困难等问题。其核心目标是创建一个完全去中心化的互联网，让独立区块链可以在共享的安全环境中无缝协作和通信。Polkadot 由以太坊联合创始人 Gavin Wood 创建。其目标是通过一个创新的多链架构，解决当前区块链生态系统的孤立性问题，促进不同区块链之间的互操作性和数据共享，从而实现真正的 Web3.0。

## 1.核心组件

Polkadot 网络由多个关键组件构成，每个组件都在整个网络中扮演着重要角色：

**1.1. Relay Chain（中继链）**

中继链是 Polkadot 的核心，负责网络的安全性、共识机制和跨链交易处理。中继链本身不处理智能合约，这样可以专注于提高性能和安全性。

**1.2.Parachains（平行链）**

平行链是独立的区块链，可以拥有自己的共识机制和功能。平行链通过中继链与其他区块链实现互操作性，并共享中继链的安全保障。

**1.3.Parathreads（平行线程）**

平行线程类似于平行链，但连接方式更加灵活。它们按需连接到中继链，适合那些不需要持续运行但又需要周期性连接的区块链应用。

**1.4.Bridges（桥）**

桥是 Polkadot 网络与其他区块链（如以太坊、比特币等）之间的连接，使得不同区块链之间可以进行数据和资产的互通。

## **2. Polkadot 的 Nominated Proof-of-Stake (NPoS) 共识机制详解**

Nominated Proof-of-Stake (NPoS) 是 Polkadot 网络采用的一种共识机制。它结合了传统的委托权益证明（DPoS）和权益证明（PoS）的优点，通过结合验证者和提名者的角色，采用优化的选举算法和严格的奖励与惩罚机制，成功地实现了高效、安全和去中心化的区块链网络。NPoS 机制不仅确保了 Polkadot 网络的运行稳定和安全，Nominated Proof-of-Stake (NPoS) 作为 Polkadot 的共识机制，，也为其他区块链项目提供了一个创新的共识机制范例。Polkadot 理念是旨在通过一个更去中心化且安全的方式来选择区块链网络的验证者，从而维护网络的安全性和高效性。

**验证者（Validators）：**验证者是负责验证交易、打包区块并维护网络安全的节点。他们通过质押一定数量的 DOT 代币来保证其诚实性。验证者需要运行完整节点，具备高性能和高可用性的服务器资源。

**提名者（Nominators）：**提名者是 DOT 代币持有者，他们通过质押 DOT 来支持一个或多个验证者。提名者与验证者的关系类似于选民和候选人，提名者的 DOT 代币被用作验证者的质押的一部分，从而共享验证者的区块奖励和惩罚。

**2.1.机制详解**

- **验证者选举：** 验证者的选举是通过 Phragmén 算法进行的，这是一种基于数学优化的公平选举算法。Phragmén 算法的目标是最大化网络的去中心化，并确保质押的分布均匀。
- **提名者选择**：提名者选择他们信任的验证者，并将 DOT 代币质押给这些验证者。
- **算法分配**：Phragmén 算法根据提名者的选择和质押的数量，公平地分配验证者的席位。

**2.2. 验证者职责**

验证者在 Polkadot 网络中的主要职责包括：

- **区块生成**：验证者轮流生成新区块，并将其添加到区块链上。
- **交易验证**：验证交易的合法性和有效性，确保没有双花和其他恶意行为。
- **共识协议**：参与网络的共识协议，确保区块链的一致性和安全性。

**2.3. 奖励与惩罚**

NPoS 机制通过奖励和惩罚来激励诚实的行为和惩罚不良行为。

- **奖励**：验证者和提名者会根据他们质押的 DOT 代币比例获得区块奖励。奖励的分配取决于验证者的表现和提名者的贡献。
- **惩罚**：如果验证者表现不诚实或未能履行职责，他们的质押 DOT 代币会被削减（slashing）。削减的程度取决于违规的严重性，提名者也会相应受到影响。

**2.4. NPoS 的优势**

- **去中心化**：NPoS 机制鼓励更多的验证者参与，从而提高网络的去中心化程度。
- **安全性**：通过质押和削减机制，确保验证者的行为诚实并有效防止攻击。
- **灵活性**：提名者可以根据表现和信誉随时更换支持的验证者，从而提高网络的灵活性和弹性。
- **公平性**：Phragmén 算法确保质押分布的公平性，避免过度集中。

## **3. DOT 代币**

DOT 是 Polkadot 的原生代币，具有以下几个主要用途：

- **治理**：持有者可以参与 Polkadot 网络的治理，包括投票、提案和协议升级。
- **质押**：用于网络的共识机制，通过质押获得奖励。
- **绑定（Bonding）**：用于将新的平行链连接到中继链。

## **4. 治理机制**

Polkadot 采用链上治理模式，允许代币持有者直接参与网络决策：

- **提案**：任何持有 DOT 代币的用户可以提出网络改进提案。
- **投票**：持有者可以对提案进行投票。
- **技术委员会**：由技术专家组成，处理紧急决策。

## **5. 开发与生态系统**

Polkadot 提供了 Substrate 框架，使开发者可以快速构建和部署区块链项目。Substrate 的模块化设计简化了区块链的开发过程，开发者可以根据需要定制自己的区块链。

# 二. 离线地址生成

```js
function createDotAddress (seedHex, addressIndex, chain, network) {
    const { key } = derivePath("m/44'/354'/0'/0'/" + addressIndex + "'", seedHex);
    const keyword = `dot_main_net`;
    const pubKey = getPublicKey(key, false).toString('hex');
    console.log("publicKey==", pubKey)
    let publicKey = pubKey;
    if(typeof publicKey === "string" && publicKey.substring(0,2)!=="0x"){
        publicKey = "0x" + publicKey;
    }
    const address = polkdot.encodeAddress(publicKey,0)
    return {
        "privateKey": key.toString('hex') + publicKey,
        "publicKey": publicKey,
        "address": address
    }
}


const mnemonic = "";
const seed = bip39.mnemonicToSeedSync(mnemonic)
const account =  createDotAddress(seed, 0, "dot", "main_net")
console.log(account)
```

# 三. 离线签名

- https://wiki.polkadot.network/docs/build-transaction-construction
- 代币精度：10

```js
const metadataRpc= "调用 state_getMetadata 返回的数据填充到这里"

const {Keyring} = require("@polkadot/api");
const { methods, getRegistry, construct} = require("@substrate/txwrapper-polkadot");


function createKeypair(privateKey) {
    const privKey = "0x" + privateKey.substring(0, 64);
    const keyring = new Keyring({ type: "ed25519" });
    return keyring.addFromUri(privKey);
}

const registry = getRegistry({
    chainName: "Polkadot",
    specName: "polkadot",
    specVersion: "1002000",
    metadataRpc,
    userExtensions:{ SetEvmOrigin: { payload: {}, extrinsic: {} } },
});


const unsigned = methods.balances.transferKeepAlive(
    {
        dest: "15vrtLsCQFG3qRYUcaEeeEih4JwepocNJHkpsrqojqnZPc2y",
        value: 5000000000,
    },
    {
        address: "16EEnWeJZkzBiLgZnz8R5NiE4Zo7S67PGvVyGWD6L538e2bB",   // from address
        blockHash: "0x7c8dc11dff851d5d7c94ebd3072a45202100257d3f8a4fa39156fbe3d5e18642",
        blockNumber: 21037668,
        genesisHash: "0x91b171bb158e2d3848fa23a9f1c25182fb8e20313b2c1eb49219da7a70ce90c3",
        metadataRpc, // must import from client RPC call state_getMetadata
        nonce: 0,
        specVersion: 1002000,
        tip: 0,
        eraPeriod: 64, // number of blocks from checkpoint that transaction is valid
        transactionVersion: 1,
    },
    {
        metadataRpc,
        registry, // Type registry
    }
);

const extrinsicPayload = registry.createType(
    "ExtrinsicPayload",
    unsigned,
    {
        version: unsigned.version,
    }
);


const keypair = createKeypair("privatekey");
const signature = extrinsicPayload.sign(keypair).signature;

const signTx = construct.signedTx(
    unsigned,
    signature,
    { metadataRpc, registry, userExtensions: {SetEvmOrigin: {payload: {}, extrinsic: {}}},}
);

console.log(signTx)
```

# 四. Polkadot 钱包相关的 RPC 接口

## 1.获取 metadatarpc 数据

- 接口作用：获取签名需要用到的 metadata

```json
{
    "jsonrpc": "2.0",
    "result": "0x......",
    "id": 1
}
```

- 接口参数：无
- 请求示范
- 返回值

```bash
curl --location 'https://rpc.polkadot.io' \
--header 'Content-Type: application/json' \
--data '{
  "jsonrpc": "2.0",
  "method": "state_getMetadata",
  "params": [],
  "id": 1
}'
```

- result 就是我们要的 metadata

## 2. 获取链的名字

- 接口作用：获取签名需要用到的 chainName
- 接口参数：无
- 请求示范

```bash
curl --location 'https://rpc.polkadot.io' \
--header 'Content-Type: application/json' \
--data '{
    "method":"system_chain",
    "params":[],"id":1,
    "jsonrpc":"2.0"
}'
```

- 返回值

```json
{
    "jsonrpc": "2.0",
    "result": "Polkadot",
    "id": 1
}
```

- result 返回的链名字

## 3. 获取 SpecName 和 SpecVersion

- 接口作用：获取签名需要用到的  SpecName 和 SpecVersion
- 接口参数：无
- 请求示范

```bash
curl --location 'https://rpc.polkadot.io' \
--header 'Content-Type: application/json' \
--data '{
  "jsonrpc": "2.0",
  "method": "state_getRuntimeVersion",
  "params": [],
  "id": 1
}'
```

- 返回值

```json
{
    "jsonrpc": "2.0",
    "result": {
        "specName": "polkadot",
        "implName": "parity-polkadot",
        "authoringVersion": 0,
        "specVersion": 1002000,
        "implVersion": 0,
        "apis": [
            [
                "0xdf6acb689907609b",
                4
            ]
        ],
        "transactionVersion": 25,
        "stateVersion": 1
    },
    "id": 1
}
```

- specName 运行时 spec 的名字
- specVersion：运行时 spec 版本

## 4. 获取签名需要的 nonce

- 接口作用：获取签名需要用到的 nonce
- 接口参数：无
- 请求示范

```bash
curl --location 'https://rpc.polkadot.io' \
--header 'Content-Type: application/json' \
--data '{
  "jsonrpc": "2.0",
  "method": "system_accountNextIndex",
  "params": ["16EEnWeJZkzBiLgZnz8R5NiE4Zo7S67PGvVyGWD6L538e2bB"],
  "id": 1
}'
```

- 返回值

```json
{
    "jsonrpc": "2.0",
    "result": 0,
    "id": 1
}
```

- result 即为签名需要用的 noce

## 5.获取创世块的 Hash

- 接口作用：获取签名需要使用的 genesisHash
- 接口参数：0 为创世块
- 请求示范

```bash
curl --location 'https://rpc.polkadot.io' \
--header 'Content-Type: application/json' \
--data '{
  "jsonrpc": "2.0",
  "method": "chain_getBlockHash",
  "params": [0],
  "id": 1
}'
```

- 返回值

```json
{
    "jsonrpc": "2.0",
    "result": "0x91b171bb158e2d3848fa23a9f1c25182fb8e20313b2c1eb49219da7a70ce90c3",
    "id": 1
}
```

- 返回的 result 就是 genesisHash

## 6. 获取区块相关信息

- 接口作用： 不传入参数：返回最新区块 传入 block number 或者 block Hash, 返回对应区块数据
- 接口参数：参考接口作用
- 请求示范

```bash
curl --location 'https://rpc.polkadot.io' \
--header 'Content-Type: application/json' \
--data '{
  "jsonrpc": "2.0",
  "method": "chain_getBlock",
  "params": ["0x7c8dc11dff851d5d7c94ebd3072a45202100257d3f8a4fa39156fbe3d5e18642"],
  "id": 1
}'
```

- 返回值

```json
{
    "jsonrpc": "2.0",
    "result": {
        "block": {
            "header": {
                "parentHash": "0xa3d4aa9e1c54b967d638efd7f26c2df67fb5ced4d54294c8f167fb6fadefc8b8",
                "number": "0x1410264",
                "stateRoot": "0x23bf1b985d0df7c8891eb4ed44f7fa05bb2242b86888b43567f187cf0b452b35",
                "extrinsicsRoot": "0x069caf68e4ce5e2ad0219de0d55c44fce1c9c0ca5b2c86a0a5d036348fe44804",
                "digest": {
                    "logs": [
                        "0x0642414245b50101b400000061570f11000000001c6cfeea11570a6aaac35569f0ad48c81a7d648a4b17a2e16e768ddf8ea5ea5e52268f317726ce51e4dfa05aba1b53927dccefb1e507ca7ff6ccad9fd7741e0d3a1410a37070a3955f888be2540c0c8f0e54f8c12cf7432e10247506b9fd0503",
                        "0x044245454684035446b837cd2f2eb527bf0190c9ffb62421967a473abcfb55ebe5065dbad95a2f",
                        "0x0542414245010136b9d0fd76acf3eaac298b8ea2f67a996259f55cfb331cc1dbeac6b22f1f9c76442eb5c87dc40f213b90a1c58cf2dcc554f9edb41c63efa912da4db93025ad86"
                    ]
                }
            },
            "extrinsics": [
                "0x280403000b71f18fd78f01",
                "0x0000000000000.........."
            ]
        },
        "justifications": null
    },
    "id": 1
}
```

- 将里面的交易解码就得到交易的 json 格式，可以是 polkdot js 解码

## 7. 发送交易到区块链网络

- 接口作用：发送交易
- 接口参数：参考接口作用
- 请求示范

```bash
curl --location 'https://rpc.polkadot.io' \
--header 'Content-Type: application/json' \
--data '{
  "jsonrpc": "2.0",
  "method": "author_submitExtrinsic",
  "params": ["0x41028400e745c2c869615d270bf5eaf86e3efe4c857594628e67ddf8010e32aa8cb9b8a6000c665c8a601c5f1b04821741ad4b59098e0807c430121fdadb9ab50f506ec24e17c1f8602b727e5bb7675c86e6b08cc6db242ba3a2f738925f2ce052724dba0945020000050300da04de6cd781c98acf0693dfb97c11011938ad22fcc476ed0089ac5aec3fe2430700f2052a01"],
  "id": 1
}'
```

- 返回值

```json
{
    "jsonrpc": "2.0",
    "error": {
        "code": 1010,
        "message": "Invalid Transaction",
        "data": "Transaction has a bad signature"
    },
    "id": 1
}
```

- 成功返回交易 Hash, 失败返回失败信息

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

- PolkDot 官网：https://polkadot.network/
- Github:  https://github.com/paritytech/polkadot-sdk
- Dot docs: https://polkadot.network/development/docs/
- RPC 接口文档:https://www.quicknode.com/docs/polkadot/chain_getBlockHash
- 浏览器: https://polkadot.subscan.io/
- PolkadotJs: https://github.com/polkadot-js