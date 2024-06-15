# EOS 系列钱包开发详细教程

# 一. EOS 简介

## 1.概述

EOS（Enterprise Operating System）是一种开源的区块链平台，旨在提供一个可扩展的去中心化应用程序（DApp）开发环境。它采用了类似操作系统的架构，具有账户、验证器、数据库、消息传递等核心功能，为开发者提供了一个强大而灵活的平台，以构建各种区块链应用。

以下是一些 EOS 的关键特性和组成部分：

- **智能合约平台**：EOS 提供了一个智能合约平台，开发者可以在上面部署和执行智能合约。这些合约可以编写使用 C++ 或其他支持的语言，并通过 EOSIO 软件进行部署。
- **高性能**：EOS 致力于实现高吞吐量和低延迟的区块链交易处理。它使用了一种名为“委托权益证明（Delegated Proof of Stake，DPoS）”的共识机制，这种机制通过选举一组验证者来验证交易，并将交易打包进区块中。
- **低成本**：EOS 设计的目标之一是降低区块链应用程序的开发和运行成本。它使用的资源模型允许用户根据其持有的代币来获取资源，而不需要支付每笔交易的费用。
- **水平扩展性**：EOS 被设计为可以轻松地水平扩展，以满足不断增长的用户需求。其架构允许多个并行的链运行，并且可以通过网络链接来扩展其功能。
- **治理模型**：EOS 采用了一种基于代币持有者的治理模型，使持币者能够对网络的发展和升级做出决策。这种模型通过投票来选择验证者和制定网络规则。

## 2. 共识算法

EOS 使用的共识算法是委托权益证明（Delegated Proof of Stake，DPoS）。DPoS 是一种共识机制，旨在通过选举一组验证者来验证交易并产生新的区块。与传统的 PoW（Proof of Work，工作量证明）不同，DPoS 不需要大量的计算能力，因此能够实现更高的交易吞吐量和更低的延迟。

EOS 的 DPoS 共识算法的基本工作原理如下：

- **验证者选举**：在 EOS 中，持有代币的持有者有权利选举一组称为“区块生产者（Block Producers）”的验证者。这些验证者负责验证交易并打包它们到区块中，并有权获得相应的奖励。
- **轮流产生区块**：通过一种轮流的方式，选举产生的验证者依次产生新的区块。每个验证者在其轮到产生区块时，负责验证之前的交易，并将其打包成一个新的区块，然后将该区块添加到区块链上。
- **投票权重**：在 DPoS 中，验证者的权重是根据他们得到的选票数量来确定的。持有更多代币的用户拥有更多的选票权重，因此他们对验证者的选举结果有更大的影响力。
- **奖励机制**：作为对验证者的激励，他们会获得相应数量的 EOS 代币作为区块奖励。这些奖励既来自新发行的代币，也来自交易费用的分配。

通过 DPoS 共识算法，EOS 实现了高吞吐量、低延迟的交易处理能力，并且能够通过投票机制实现网络的去中心化治理。这种机制使得 EOS 能够适用于各种规模的应用场景，并且具有较高的扩展性和灵活性。

尽管 EOS 主要采用 DPoS，但某些版本的 EOSIO 软件中可能会使用 PBFT 或类似的拜占庭容错算法来增强网络的安全性和稳定性。这种混合使用的方法可能会结合 DPoS 的高吞吐量和 PBFT 的安全性，以提供更强大的区块链网络。

## 3. EOS 的 PowerUp 机制

EOS 的 PowerUp 机制是一种新的资源模型，旨在改善网络的资源管理和使用效率。在之前的资源模型中，EOS 网络的用户需要持有一定数量的 EOS 代币来获取网络资源（如 CPU、NET 和 RAM），这种资源模型可能会导致一些问题，比如资源浪费和网络拥堵。

PowerUp 机制的主要思想是让用户可以直接购买和使用网络资源，而不必持有 EOS 代币。具体来说，PowerUp 机制包含以下几个关键点：

- **资源购买**：用户可以通过使用信用卡或其他支付方式直接购买网络资源，而不需要事先持有 EOS 代币。他们可以购买所需的 CPU、NET 和 RAM，以满足其在 EOS 网络上的需求。
- **资源使用**：一旦用户购买了资源，他们就可以立即开始使用它们，而不必等待资源的释放或解冻。这种即时的资源使用方式可以帮助用户更灵活地管理他们的网络资源。
- **自动续费**：PowerUp 机制还支持资源的自动续费功能，用户可以选择在资源用尽之前自动续费资源，以确保他们的应用程序不会因为资源不足而中断。
- **弹性定价**：PowerUp 机制采用了一种基于市场供需关系的动态定价模型，资源的价格会根据市场需求的变化而变化。这种弹性定价机制可以帮助保持资源的供需平衡，同时避免资源的浪费和滥用。

总的来说，PowerUp 机制旨在改善 EOS 网络的资源管理和使用效率，使用户可以更灵活地获取和使用网络资源，同时帮助 EOS 生态系统实现更加健康和可持续的发展。

# 二. WAXP 简介

WAXP（WAX Protocol Token）是 WAX（Worldwide Asset eXchange）区块链平台的原生代币。WAX 是一个专注于虚拟商品交易的区块链网络，旨在为游戏玩家、收藏家和虚拟商品交易者提供一个去中心化的市场和交易平台。

以下是关于 WAXP 的详细介绍：

- **原生代币**：WAXP 是 WAX 平台的原生代币，作为网络中的货币和经济激励手段。持有 WAXP 的用户可以用它来支付交易费用、购买虚拟商品和服务，以及参与网络的治理。
- **区块链基础设施**：WAXP 是构建在 WAX 区块链上的，这个区块链专门设计用于支持虚拟商品的交易和交换。WAX 区块链基于 DPoS 共识机制，旨在提供高吞吐量、低延迟的交易处理能力。
- **虚拟商品交易平台**：WAX 平台为游戏开发商、虚拟商品发行者和收藏家提供了一个去中心化的市场和交易平台。通过 WAX 区块链，用户可以安全地买卖虚拟商品，包括游戏物品、数字艺术品、虚拟地产等。
- **智能合约支持**：WAX 区块链支持智能合约技术，使开发者能够部署和执行自定义的智能合约。这些合约可以用于管理虚拟商品的所有权、交易规则和分配方案等。
- **NFT 生态系统**：WAXP 也是支持 NFT（Non-Fungible Token，非同质化代币）的生态系统中的重要组成部分。在 WAX 平台上，开发者和发行者可以创建和交易各种 NFT，包括游戏道具、数字艺术品、收藏品等。

WAXP 是 fork  EOS 的代码改造的一个项目，故而很多行为都和 EOS 是一样。

# 二. 地址生成与激活

## 1.准备账户，生成 EOS 密钥对

```javascript
test('create Key', async ()=>{
    const privteKey = "Ecc privateKey"
    const wifKey = eosEcc.PrivateKey.fromHex(privteKey)
    console.log(wifKey.toWif())
    console.log(wifKey.toPublic().toString());
});
```

2. 创建新账号

```javascript
test('json to abi one', async ()=>{
  const abiCreateData = {
    code: "eosio",
    action: "newaccount",
    args: {
      creator: "ox4x5zhltd4t",       // 支付创建新用户费用的基账户(用户名)
      name: "theweb3test",           // 新账户的用户名   仅支持字母和1-5数字
      owner: {
        threshold: 1,
        keys: [
          {
            key: "EOS5rKs3ADoAdjrxkhTSjRNmCHqghDVFcrXgbjnjG5SjVNZi8ww95", //  新账户的 Owner Public Key
            weight: 1,
          },
        ],
        accounts: [],
        waits: [],
      },
      active: {
        threshold: 1,
        keys: [
          {
            key: "EOS7vFFPoJvpYsYJ92AdGwVPRiFTA2JMJGmzz71k1zTMUTHJZYuqp", // Active Public Key
            weight: 1,
          },
        ],
        accounts: [],
        waits: [],
      },
    },
  };
  console.log(JSON.stringify(abiCreateData))
});
```

数据的 json 格式的数据转换成 16 进制字符串数据，使用 https://developers.eos.io/manuals/eos/v2.2/nodeos/plugins/chain_api_plugin/api-reference/index#operation/abi_json_to_bin 这个接口，或者实现类似的代码编码数据。

## 3. 账户 buyrambytes

```javascript
test('json to abi two', async ()=>{
  const abiRawData = {
    code: "eosio",
    action: "buyrambytes",
    args: {
      payer: "ox4x5zhltd4t",         //付款账户
      receiver: "theweb3test",        // 收款账户
      bytes: 2996,                   // 数量
    },
  };
  console.log(JSON.stringify(abiRawData));
});
```

数据的 json 格式的数据转换成 16 进制字符串数据，使用 https://developers.eos.io/manuals/eos/v2.2/nodeos/plugins/chain_api_plugin/api-reference/index#operation/abi_json_to_bin 这个接口，或者实现类似的代码编码数据。

## 4. 抵押 CPU 和 NET

```javascript
test('di ya cpu', async ()=>{
  const abiDelegateData = {
    code: "eosio",
    action: "delegatebw",
    args: {
      from: "ox4x5zhltd4t",
      receiver: "theweb3test",
      stake_net_quantity: "0.200000 EOS",
      stake_cpu_quantity: "0.200000 EOS",
      transfer: 0,
    },
  };
  console.log(JSON.stringify(abiDelegateData));
});
```

数据的 json 格式的数据转换成 16 进制字符串数据，使用 https://developers.eos.io/manuals/eos/v2.2/nodeos/plugins/chain_api_plugin/api-reference/index#operation/abi_json_to_bin 这个接口，或者实现类似的代码编码数据。

## 6. 离线构建创建新账号的交易

```javascript
test('create account', async ()=>{
    const timeStamp = timePointSecToDate(
        dateToTimePointSec("2022-04-20T11:40:36.000") + 600
    );
    const serdata = {
      max_net_usage_words: 0,
      max_cpu_usage_ms: 0,
      delay_sec: 0,
      context_free_actions: [],
      actions: [
        {
          account: "eosio",
          name: "newaccount",
          authorization: [{ actor: "aaaashiiang", permission: "owner" }],
          data: "c0a671aee1ec8e3f00000857612db64a01000000010002c9fdc9a4df56c7bce22fabc6f31fb844293459fa7b53192ff8cac611ea36b3fc0100000001000000010002c9fdc9a4df56c7bce22fabc6f31fb844293459fa7b53192ff8cac611ea36b3fc01000000",
        },
        {
          account: "eosio",
          name: "buyrambytes",
          authorization: [{ actor: "aaaashiiang", permission: "owner" }],
          data: "c0a671aee1ec8e3f00000857612db64ab40b0000",
        },
        {
          account: "eosio",
          name: "delegatebw",
          authorization: [{ actor: "aaaashiiang", permission: "owner" }],
          data: "c0a671aee1ec8e3f00000857612db64a002d3101000000000857415800000000002d310100000000085741580000000000",
        },
      ],
      transaction_extensions: [],
      expiration: timeStamp,
      ref_block_num: 177994685 & 0xffff,
      ref_block_prefix: 2207165085,
    };
    let buffer = new Serialize.SerialBuffer({
      textEncoder: TextEncoder,
      textDecoder: TextDecoder,
    });
    transactionTypes.get("transaction").serialize(buffer, serdata);
    const privateKey = "PrivateKeyEOS";
    const signatureProvider = new JsSignatureProvider([privateKey]);
    const signature = await signatureProvider.sign({
      chainId: chainId,
      requiredKeys: ["publicKey"],
      serializedTransaction: buffer.asUint8Array(),
    });
    const res = {
        signatures: signature,
        compression: 0,
        packed_context_free_data: "",
        packed_trx: Serialize.arrayToHex(buffer.asUint8Array()),
    };
    console.log(JSON.stringify(res));
  })
});
```

- 把交易发送到区块链网络激活账户

# 三. 交易离线签名

## 1.普通签名

```javascript
describe('Sign Transaction', ()=>{
    test('waxp sign tx', async ()=>{
        const privateKey = "eccPrivateKey"
        const timeStamp = timePointSecToDate(
            dateToTimePointSec("2022-03-23T06:44:56.000") + 600
        );
        const params = {
            txObj: {
                block: 173121671,        // 最新区块：getInfo.head_block_num
                prefix: 1239139801,      // 最新区块后缀：getBlock.ref_block_prefix
                expiration: timeStamp,   // 交易过期时间戳：getBlock.timestamp
                chainId: "1064487b3cd1a897ce03ae5b6a865651747e2e152090f99c1d19d44e01aea5a4",
                from: "aaaashiiang",
                to: "lrn1zg5umbqp",
                quantity: "1.00000000",
                memo: "1222"
            },
            network: 'main_net',
            privs: [{ key: privateKey }]
        }
        const tx_msg = await signTransaction(params)
        console.log("tx_msg===", tx_msg)
    })
})
```

## 2. Powerup 签名

```javascript
describe('Sign powerUp Transaction', ()=>{
    test('waxp power tx', async ()=>{
        const privateKey = "eccPrivateKey"
        const timeStamp = timePointSecToDate(
            dateToTimePointSec("2022-03-31T07:00:59.500") + 600
        );
        console.log(timeStamp)
        const params = {
            txObj: {
                block: 173268750,        // 最新区块：getInfo.head_block_num
                prefix: 2304335082,      // 最新区块后缀：getBlock.ref_block_prefix
                expiration: timeStamp,   // 交易过期时间戳：getBlock.timestamp
                chainId: "1064487b3cd1a897ce03ae5b6a865651747e2e152090f99c1d19d44e01aea5a4",
                from: "aaaashiiang",
                to: "lrn1zg5umbqp",
                quantity: "1.00000000",
                memo: "1222",
                max_payment: "5.00000000",
                net_frac: 1000000,
                cpu_frac: 1000000,
            },
            network: 'main_net',
            privs: [{ key: privateKey }]
        }
        const tx_msg = await prepareAccount(params)
        console.log("tx_msg===", tx_msg)
    })
})
```

四.  EOS 和 WAXP 钱包使用的 RPC 接口

## 1.获取链信息和状态

- https://developers.eos.io/manuals/eos/v2.2/nodeos/plugins/chain_api_plugin/api-reference/index#operation/get_info

## 2. 获取账户信息

- https://developers.eos.io/manuals/eos/v2.2/nodeos/plugins/chain_api_plugin/api-reference/index#operation/get_account

## 3. 获取账户余额

- https://developers.eos.io/manuals/eos/v2.2/nodeos/plugins/chain_api_plugin/api-reference/index#operation/get_currency_balance

## 4. 根据区块获取区块信息

- https://developers.eos.io/manuals/eos/v2.2/nodeos/plugins/chain_api_plugin/api-reference/index#operation/get_block_info

## 5. 根据 block 获取交易

- https://developers.eos.io/manuals/eos/v2.2/nodeos/plugins/chain_api_plugin/api-reference/index#operation/get_block

## 6. Json To Bin 编码接口

- https://developers.eos.io/manuals/eos/v2.2/nodeos/plugins/chain_api_plugin/api-reference/index#operation/abi_json_to_bin

## 7. 广播交易

- https://developers.eos.io/manuals/eos/v2.2/nodeos/plugins/chain_api_plugin/api-reference/index#operation/push_transaction
- https://developers.eos.io/manuals/eos/v2.2/nodeos/plugins/chain_api_plugin/api-reference/index#operation/send_transaction

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