# Ton 钱包开发详细教程

# 一. TON 简介

TON（The Open Network）是一个去中心化区块链平台，由Telegram Messenger的创始人Nikolai Durov和Pavel Durov发起，旨在提供快速、安全且可扩展的区块链解决方案。

## 1.历史背景

- 起源：TON最初由Telegram公司于2018年推出，作为Telegram的区块链项目。项目的核心目标是创建一个高效的区块链平台，能够支持大规模的用户应用。
- 融资：TON通过ICO（Initial Coin Offering）筹集了约 17 亿美元，吸引了大量投资者。
- 监管挑战：由于受到美国证券交易委员会（SEC）的法律挑战，Telegram 在 2020 年放弃了TON项目。

## 2. 技术特点

- 多链架构：TON采用了多链架构，包括一个主链和多个工作链（workchains），可以平行处理大量交易，从而提高吞吐量。
- 分片技术：使用分片技术（sharding），将区块链拆分成多个碎片，每个碎片可以独立处理交易和智能合约，进一步提升了扩展性。
- 高吞吐量和低延迟：TON的目标是实现每秒数百万笔交易（TPS），并保持低延迟，适用于大规模应用。
- 智能合约：支持智能合约，允许开发者在平台上构建去中心化应用（DApps）。
- TON虚拟机：TON拥有自己的虚拟机（TVM），用于执行智能合约，类似于以太坊的EVM。
- 智能合约开发语言：Fift 和 FunC 编程语言
- TON 共识算法：Byzantine Fault Tolerant (BFT) PoS（Proof of Stake）

## 3. 主要组件

- TON区块链：核心区块链网络，包含主链和多个工作链。
- TON支付：内置支付系统，支持微支付和快速交易。
- TON存储：分布式文件存储系统，用于存储大量数据。
- TON代理：网络匿名性和去中心化的VPN服务。
- TON服务：平台上的去中心化服务，如去中心化的DNS等。

## 4. TON 币（Gram）

- 功能：Gram币是TON网络的原生加密货币，用于支付交易费用、存储费用、以及作为智能合约的燃料等。
- 分发：最初计划通过ICO发行，但由于监管问题，TON的正式币发行计划被迫中止。

## 5. 现状

- 后续发展：虽然Telegram官方退出了TON项目，但开源社区继续推动TON的发展，形成了多个分叉项目，如Free TON、TON Community等。
- 生态系统：TON生态系统在开源社区的推动下，继续发展，许多开发者和项目在其平台上构建去中心化应用。

## 6. 共识算法流程

**6.1. Byzantine Fault Tolerance (BFT)：**

- BFT 是一种容错机制，能够在存在恶意或故障节点的情况下仍然确保系统的一致性和正确性。
- TON 的 BFT 算法允许网络在最多三分之一的节点存在故障或恶意行为时仍能正常运行。

**6.2.Proof of Stake (PoS)：**

- PoS 是一种共识机制，通过持有和锁定代币来参与网络共识和验证区块。
- 在 TON 中，验证者（validators）需要质押一定数量的 TON 代币（Gram），以获得参与区块生成和验证的权利。

**6.3.共识算法流程**

😄**验证者选择**

😄😄 验证者通过质押 TON 代币来获得资格。质押的数量决定了验证者在共识过程中的权重。 

😄😄 验证者定期轮换，以确保网络的去中心化和安全性。



😄**区块提议**

😄😄 在每个共识周期（epoch）开始时，一个验证者被随机选择作为区块提议者（block proposer）。

 😄😄 区块提议者负责构建和广播新区块。



**区块验证和投票**

😄😄 所有验证者收到提议的区块后，验证其合法性，包括交易的有效性和区块结构。 

😄😄 验证通过后，验证者对区块进行投票。如果超过三分之二的验证者投票通过，区块即被认为确认。



😄**区块确认：**

😄😄 一旦区块获得足够的投票通过，它将被添加到区块链中。

 😄😄 验证者将收到相应的奖励，奖励根据其质押量和参与度进行分配。



😄**惩罚机制：** 

😄😄为了防止恶意行为，TON 采用了惩罚机制。如果验证者试图作恶（如双花攻击），其质押的代币将被削减，甚至完全没收。 

😄😄这种机制确保了验证者的行为诚实，并保护网络的安全性。

## 7. TON 的创新和优势

😄**高速和高效**

😄😄通过 BFT 和 PoS 结合，TON 能够实现高吞吐量和低延迟，适用于大规模应用。 

😄😄 验证者数量的优化和平行处理技术进一步提升了网络的效率。



😄动态分片：

😄😄TON 采用动态分片（dynamic sharding）技术，自动调整和优化网络资源分配，以应对不同的交易量。

 😄😄 每个工作链（workchain）和分片链（shardchain）独立处理交易，提高了整体网络的扩展性。



😄**安全性和去中心化**

😄😄 通过严格的验证者选举和惩罚机制，确保网络的安全性和去中心化。 

😄😄 验证者的多样性和定期轮换防止了权力集中和单点故障。

## 8. 应用前景

- 支付和金融服务：TON的高吞吐量和低交易费用使其适合于支付和金融服务领域。
- 去中心化应用：通过支持智能合约，TON为开发者提供了一个构建各种DApps的平台，如去中心化金融（DeFi）、去中心化社交网络等。
- 数据存储与共享：TON的分布式存储解决方案可以用于安全、高效的数据存储和共享。

# 二.离线地址生成

```javascript
const { derivePath, getPublicKey } = require('ed25519-hd-key');
const TonWeb = require('tonweb-lite');


export function createTonAddress(seedHex: string, addressIndex: number) {
    const { key } = derivePath("m/44'/607'/1'/" + addressIndex + "'", seedHex);
    const publicKey = getPublicKey(new Uint8Array(key), false).toString('hex');

    const tonweb = new TonWeb();
    const WalletClass = tonweb.wallet.all['v3R2'];

    const publikKey = new Uint8Array(Buffer.from(publicKey, "hex"))
    const wallet = new WalletClass(tonweb.provider, {
        publicKey: publikKey,
        wc: 0
    });
    const walletAddress =  wallet.getAddress();
    return {
        "privateKey": key.toString('hex') + publicKey,
        "publicKey": publicKey,
        "address": walletAddress.toString(true, true, true, false)
    }
}
```

## 三.离线签名

```javascript
const TonWeb = require('tonweb-lite');
const BigNumber = require("bignumber.js")

export async function SignTransaction (params: { from: any; to: any; memo: any; amount: any; sequence: any; decimal: any; privateKey: any; }) {
    const { from, to, memo, amount, sequence, decimal, privateKey} = params;
    const tonweb = new TonWeb();
    const calcAmount = new BigNumber(amount).times(new BigNumber(10).pow(decimal)).toNumber();
    if (calcAmount % 1 !== 0) throw new Error("amount invalid");
    const keyPair = TonWeb.utils.nacl.sign.keyPair.fromSecretKey(new Uint8Array(Buffer.from(privateKey, 'hex')));
    const WalletClass = tonweb.wallet.all['v4R2'];
    const wallet = new WalletClass(tonweb.provider, {
        publicKey: keyPair.publicKey,
        wc: 0
    });
    let secretKey = keyPair.secretKey
    const walletAddress = await wallet.getAddress();
    console.log(walletAddress.toString(true, true, false, false))
    const fromAddres = walletAddress.toString(true, true, false, false);
    if (from !== fromAddres) throw new Error("from address invalid");
    const toAddress = new TonWeb.utils.Address(to);
    const tx_ret = await wallet.methods.transfer({
        secretKey,
        toAddress: toAddress.toString(true, true, false, false),
        amount: calcAmount,
        seqno: sequence,
        payload: memo || "",
        sendMode: 3, // 3 默认为转账, 细节请看调研文档
    })
    const queryData = await tx_ret.getQuery()
    const hash = await queryData.hash()
    const boc = await queryData.toBoc(false);
    return {
        "hash": TonWeb.utils.bytesToBase64(hash),
        "rawtx": TonWeb.utils.bytesToBase64(boc)
    }
}
```

## 四. TON 钱包开发中用到的 API

## 1. 获取链的信息

- 接口名称：getMasterchainInfo
- 参数：无
- 请求示范

```bash
curl -X 'GET' \
  'https://toncenter.com/api/v2/getMasterchainInfo' \
  -H 'accept: application/json'
```

- 返回值

```json
{
  "ok": true,
  "result": {
    "@type": "blocks.masterchainInfo",
    "last": {
      "@type": "ton.blockIdExt",
      "workchain": -1,
      "shard": "-9223372036854775808",
      "seqno": 38093956,
      "root_hash": "1uYjMl8n3HVCPyWr2aTfuevBuhXiJDPqfgD/kM6rSkE=",
      "file_hash": "v5CY7oHmqYCstr6NC2k8lzgJeLUnC3WTP4sgv65neYw="
    },
    "state_root_hash": "E0YQEYfwOR90wdf/xI6j8Zo5ktuB9IgH1fy9d8oYick=",
    "init": {
      "@type": "ton.blockIdExt",
      "workchain": -1,
      "shard": "0",
      "seqno": 0,
      "root_hash": "F6OpKZKqvqeFp6CQmFomXNMfMj2EnaUSOXN+Mh+wVWk=",
      "file_hash": "XplPz01CXAps5qeSWUtxcyBfdAo5zVb1N979KLSKD24="
    },
    "@extra": "1716812857.6911736:6:0.2953780136447337"
  }
}
```

😄 last 最新区块信息；init：初始区块信息 

😄😄 workchain：工作链号 

😄😄 shard：分片链号 

😄😄 seqno 序号 

😄😄 root_hash：根 Hash

 😄😄 file_hash: File Hash

## 2.查询地址信息

- 接口名称：getAddressInformation
- 请求参数:   address->要查询的地址
- 请求示范

```bash
curl -X 'GET' \
  'https://toncenter.com/api/v2/getAddressInformation?address=UQCQCLTvR0XYTyM0uxh_H8kLAR7u7v98pEKZKpbq8w2per6d' \
  -H 'accept: application/json'
```

- 返回值

```json
{
  "ok": true,
  "result": {
    "@type": "raw.fullAccountState",
    "balance": "1000000000",
    "code": "",
    "data": "",
    "last_transaction_id": {
      "@type": "internal.transactionId",
      "lt": "46748895000001",
      "hash": "iGEO9MSNROalIPhXSg1OS1AsLWn1+m11NCD6gCvpW8U="
    },
    "block_id": {
      "@type": "ton.blockIdExt",
      "workchain": -1,
      "shard": "-9223372036854775808",
      "seqno": 38094008,
      "root_hash": "HD8PpGLVYdw4XVQwfEXCrC7fp/zFbW1kiWn+4z+wdmc=",
      "file_hash": "iuxvQ2ciNS+12NXMGLIlYFwn5pRfR6Gp9kE89btm3mE="
    },
    "frozen_hash": "",
    "sync_utime": 1716813146,
    "@extra": "1716813179.1416047:3:0.6544472753885341",
    "state": "uninitialized"
  }
}
```

👋 返回参数 

👋👋 balance：账户余额 

👋👋 last_transaction_id: 最后一笔交易信息

 👋👋 block_id： 区块 ID 

👋👋👋 workchain：工作链号 

👋👋👋 shard：分片链号 

👋👋👋 seqno 序号 

👋👋👋 root_hash：根 Hash 

👋👋👋 file_hash: File Hash

## 3. 获取账户余额

- 接口名称：getAddressBalance
- 参数: address->用户地址

```bash
curl -X 'GET' \
  'https://toncenter.com/api/v2/getAddressBalance?address=EQDfvVvoSX_cDJ_L38Z2hkhA3fitZCPW1WV9mw6CcNbIrH-Q' \
  -H 'accept: application/json'
```

- 返回值：返回的就是余额

```json
{
  "ok": true,
  "result": "5610477699352"
}
```

## 4. 获取共识层的块高

- 接口名称：getConsensusBlock

```bash
curl -X 'GET' \
  'https://toncenter.com/api/v2/getConsensusBlock' \
  -H 'accept: application/json'
```

- 返回值：返回的就是区块的高度

```json
{
  "ok": true,
  "result": {
    "consensus_block": 38093936,
    "timestamp": 1716812741.435646
  }
}
```

## 5. 根据地址查询交易

- 接口名称：getTransactions
- 参数名称 address：账户地址 lt：交易开始的逻辑时间必须与**哈希**一起发送。 hash：必须使用**lt**发送以**base64**或**十六**进制编码形式的交易哈希值。 to_lt：交易完成的逻辑时间（将 tx 从*lt*转移到*to_lt*）。 archival：默认情况下，getTransaction 请求由任何可用的 liteserver 处理。如果*archival=true，*则仅使用具有完整历史记录的 liteserver。

```bash
curl -X 'GET' \
  'https://toncenter.com/api/v2/getTransactions?address=EQCQCLTvR0XYTyM0uxh_H8kLAR7u7v98pEKZKpbq8w2peuNY&limit=10&lt=46761214000001&hash=0K7IL8NL%2FkLNvicyyH6fQGcH4UuxeM9F1djHPIKYCd4%3D&to_lt=0&archival=true' \
  -H 'accept: application/json'
```

- 返回值

```json
{
  "ok": true,
  "result": [
    {
      "@type": "raw.transaction",
      "address": {
        "@type": "accountAddress",
        "account_address": "EQCQCLTvR0XYTyM0uxh_H8kLAR7u7v98pEKZKpbq8w2peuNY"
      },
      "utime": 1716870305,
      "data": "",
      "transaction_id": {
        "@type": "internal.transactionId",
        "lt": "46761214000001",
        "hash": "0K7IL8NL/kLNvicyyH6fQGcH4UuxeM9F1djHPIKYCd4="
      },
      "fee": "2699268",
      "storage_fee": "68",
      "other_fee": "2699200",
      "in_msg": {
        "@type": "raw.message",
        "source": "",
        "destination": "EQCQCLTvR0XYTyM0uxh_H8kLAR7u7v98pEKZKpbq8w2peuNY",
        "value": "0",
        "fwd_fee": "0",
        "ihr_fee": "0",
        "created_lt": "0",
        "body_hash": "Fuy+HLdr9Hxj/nq/e/NAjL+CaacYTwsP3ayhhc31fkw=",
        "msg_data": {
          "@type": "msg.dataRaw",
          "body": "te6cckEBAwEAigABnAIstl8Uy59uk7bahmyKvkudzQTEUtLELPDNwvCCpLLBqgKrGZ4Ua/bfOMCJt0iFg99xt/ju4uCDjgTT/W4ZeA8pqaMXZlVc1wAAAAMAAwEBaEIACgA7ijTfXQdJyuwzSf6AdyTUFJjpE+mPTRkx2/756pmgL68IAAAAAAAAAAAAAAAAAAECAAD8d2HH",
          "init_state": ""
        },
        "message": "Aiy2XxTLn26TttqGbIq+S53NBMRS0sQs8M3C8IKkssGqAqsZnhRr9t84wIm3SIWD33G3+O7i4IOO\nBNP9bhl4DympoxdmVVzXAAAAAwAD\n"
      },
      "out_msgs": [
        {
          "@type": "raw.message",
          "source": "EQCQCLTvR0XYTyM0uxh_H8kLAR7u7v98pEKZKpbq8w2peuNY",
          "destination": "EQAUAHcUab66DpOV2GaT_QDuSagpMdIn0x6aMmO3_fPVMyD8",
          "value": "100000000",
          "fwd_fee": "293336",
          "ihr_fee": "0",
          "created_lt": "46761214000002",
          "body_hash": "lqKW0iTyhcZ77pPDD4owkVfw2qNdxbh+QQt4YwoJz8c=",
          "msg_data": {
            "@type": "msg.dataRaw",
            "body": "te6cckEBAQEAAgAAAEysuc0=",
            "init_state": ""
          },
          "message": ""
        }
      ]
    },
    {
      "@type": "raw.transaction",
      "address": {
        "@type": "accountAddress",
        "account_address": "EQCQCLTvR0XYTyM0uxh_H8kLAR7u7v98pEKZKpbq8w2peuNY"
      },
      "utime": 1716870040,
      "data": "",
      "transaction_id": {
        "@type": "internal.transactionId",
        "lt": "46761157000001",
        "hash": "kI1yL265o4KKNFQv4BpOWqiv7zclV2GJzG4fkIck66g="
      },
      "fee": "2737656",
      "storage_fee": "56",
      "other_fee": "2737600",
      "in_msg": {
        "@type": "raw.message",
        "source": "",
        "destination": "EQCQCLTvR0XYTyM0uxh_H8kLAR7u7v98pEKZKpbq8w2peuNY",
        "value": "0",
        "fwd_fee": "0",
        "ihr_fee": "0",
        "created_lt": "0",
        "body_hash": "R0b8n0d8Al9UGCTrc4qTq/yULwrLfwWHQfqSa5heenw=",
        "msg_data": {
          "@type": "msg.dataRaw",
          "body": "te6cckEBAwEAkAABnHrshe7MgYFHgZB/UraQktsXf9ASU2rnvJgSdICI0M0aWH7pWWIbFaNwaFZWsILnS85xM1mq09sg3gEPhhPX0wgpqaMXZlVb0QAAAAIAAwEBaEIACgA7ijTfXQdJyuwzSf6AdyTUFJjpE+mPTRkx2/756pmgL68IAAAAAAAAAAAAAAAAAAECAAwAAAAAMTErH7xZ",
          "init_state": ""
        },
        "message": "euyF7syBgUeBkH9StpCS2xd/0BJTaue8mBJ0gIjQzRpYfulZYhsVo3BoVlawgudLznEzWarT2yDe\nAQ+GE9fTCCmpoxdmVVvRAAAAAgAD\n"
      },
      "out_msgs": [
        {
          "@type": "raw.message",
          "source": "EQCQCLTvR0XYTyM0uxh_H8kLAR7u7v98pEKZKpbq8w2peuNY",
          "destination": "EQAUAHcUab66DpOV2GaT_QDuSagpMdIn0x6aMmO3_fPVMyD8",
          "value": "100000000",
          "fwd_fee": "306136",
          "ihr_fee": "0",
          "created_lt": "46761157000002",
          "body_hash": "oVvmxCMChT1Hm7a89lTZPTmVNlFBkPGvdJezX0j+px0=",
          "msg_data": {
            "@type": "msg.dataText",
            "text": "MTE="
          },
          "message": "11"
        }
      ]
    },
    {
      "@type": "raw.transaction",
      "address": {
        "@type": "accountAddress",
        "account_address": "EQCQCLTvR0XYTyM0uxh_H8kLAR7u7v98pEKZKpbq8w2peuNY"
      },
      "utime": 1716869823,
      "data": "",
      "transaction_id": {
        "@type": "internal.transactionId",
        "lt": "46761114000001",
        "hash": "q+D9S9bIYoVSSdEhZQbEH0qkIDKbxsFeefU5wcFRvu8="
      },
      "fee": "2751295",
      "storage_fee": "13695",
      "other_fee": "2737600",
      "in_msg": {
        "@type": "raw.message",
        "source": "",
        "destination": "EQCQCLTvR0XYTyM0uxh_H8kLAR7u7v98pEKZKpbq8w2peuNY",
        "value": "0",
        "fwd_fee": "0",
        "ihr_fee": "0",
        "created_lt": "0",
        "body_hash": "1OsoqYqQ3CKxC/j6Zafknp+XHqKpL3nXqg73C545+eQ=",
        "msg_data": {
          "@type": "msg.dataRaw",
          "body": "te6cckEBAwEAkAABnJ5E6TDiYZc/ySbEaB8EjkeoR2aGaMUIXyaebXWFrXlNU4CdHotNMhLvq3nTz06l9Q7J1dWA9qNSsB+ZZwuMIgQpqaMXZlVa9wAAAAEAAwEBaGIAEawCw9FvIVfLDu0Vwxv8VqLdp5juQvJ+kgbmP2rnAh+gL68IAAAAAAAAAAAAAAAAAAECAAwAAAAAMTE5S5cH",
          "init_state": ""
        },
        "message": "nkTpMOJhlz/JJsRoHwSOR6hHZoZoxQhfJp5tdYWteU1TgJ0ei00yEu+redPPTqX1DsnV1YD2o1Kw\nH5lnC4wiBCmpoxdmVVr3AAAAAQAD\n"
      },
      "out_msgs": [
        {
          "@type": "raw.message",
          "source": "EQCQCLTvR0XYTyM0uxh_H8kLAR7u7v98pEKZKpbq8w2peuNY",
          "destination": "EQAjWAWHot5Cr5Yd2iuGN_itRbtPMdyF5P0kDcx-1c4EP-AO",
          "value": "100000000",
          "fwd_fee": "306136",
          "ihr_fee": "0",
          "created_lt": "46761114000002",
          "body_hash": "oVvmxCMChT1Hm7a89lTZPTmVNlFBkPGvdJezX0j+px0=",
          "msg_data": {
            "@type": "msg.dataText",
            "text": "MTE="
          },
          "message": "11"
        }
      ]
    },
    {
      "@type": "raw.transaction",
      "address": {
        "@type": "accountAddress",
        "account_address": "EQCQCLTvR0XYTyM0uxh_H8kLAR7u7v98pEKZKpbq8w2peuNY"
      },
      "utime": 1716816048,
      "data": "",
      "transaction_id": {
        "@type": "internal.transactionId",
        "lt": "46749642000001",
        "hash": "TGyYc/7q76CG3yC+m+FYv3SZaCDmOCm/WLbKwKyQ9RQ="
      },
      "fee": "5814031",
      "storage_fee": "31",
      "other_fee": "5814000",
      "in_msg": {
        "@type": "raw.message",
        "source": "",
        "destination": "EQCQCLTvR0XYTyM0uxh_H8kLAR7u7v98pEKZKpbq8w2peuNY",
        "value": "0",
        "fwd_fee": "0",
        "ihr_fee": "0",
        "created_lt": "0",
        "body_hash": "NszjfZ3Xs/JcfwcJTchKX1I+qKGV8quAbqo+fDXUg9M=",
        "msg_data": {
          "@type": "msg.dataRaw",
          "body": "te6cckEBAwEAigABnCOvwXSAi8x/8s0cjJl/6PLCfIjW9ek2w6gqbYkVqMPeOGqLFcLSMPVGPDkeoxSvG3hui9uajIdC1UgcM5JqeQApqaMX/////wAAAAAAAwEBaEIAARbQkOC/zRq7PYMSPkDgZvLt1Tzz0PQ66go6ck2j3SugL68IAAAAAAAAAAAAAAAAAAECAACkihgn",
          "init_state": ""
        },
        "message": "I6/BdICLzH/yzRyMmX/o8sJ8iNb16TbDqCptiRWow944aosVwtIw9UY8OR6jFK8beG6L25qMh0LV\nSBwzkmp5ACmpoxf/////AAAAAAAD\n"
      },
      "out_msgs": [
        {
          "@type": "raw.message",
          "source": "EQCQCLTvR0XYTyM0uxh_H8kLAR7u7v98pEKZKpbq8w2peuNY",
          "destination": "EQACLaEhwX-aNXZ7BiR8gcDN5duqeeeh6HXUFHTkm0e6V8BO",
          "value": "100000000",
          "fwd_fee": "293336",
          "ihr_fee": "0",
          "created_lt": "46749642000002",
          "body_hash": "lqKW0iTyhcZ77pPDD4owkVfw2qNdxbh+QQt4YwoJz8c=",
          "msg_data": {
            "@type": "msg.dataRaw",
            "body": "te6cckEBAQEAAgAAAEysuc0=",
            "init_state": ""
          },
          "message": ""
        }
      ]
    },
    {
      "@type": "raw.transaction",
      "address": {
        "@type": "accountAddress",
        "account_address": "EQCQCLTvR0XYTyM0uxh_H8kLAR7u7v98pEKZKpbq8w2peuNY"
      },
      "utime": 1716812693,
      "data": "",
      "transaction_id": {
        "@type": "internal.transactionId",
        "lt": "46748895000001",
        "hash": "iGEO9MSNROalIPhXSg1OS1AsLWn1+m11NCD6gCvpW8U="
      },
      "fee": "0",
      "storage_fee": "0",
      "other_fee": "0",
      "in_msg": {
        "@type": "raw.message",
        "source": "EQAamDt640mf7Hj6VtbtaLQjSXz9Bs421WmI1z1RnK0QMNcC",
        "destination": "EQCQCLTvR0XYTyM0uxh_H8kLAR7u7v98pEKZKpbq8w2peuNY",
        "value": "1000000000",
        "fwd_fee": "266669",
        "ihr_fee": "0",
        "created_lt": "46748890000002",
        "body_hash": "K8XqZjod5FnmvUSfKACUVPdO4EKbE8qJKMrFGgno7iU=",
        "msg_data": {
          "@type": "msg.dataText",
          "text": "bWVtZQ=="
        },
        "message": "meme"
      },
      "out_msgs": []
    }
  ]
}
```

👋 返回参数 

👋👋 in_msg：转入信息； out_msgs：转出 

👋👋 source 转出地址 

👋👋 destination 转入地址 

👋👋 value：转账金额 

👋👋 带 fee 的字段消耗的手续费

## 6. 发送交易到区块链网络

- 接口名称：sendBoc
- 接口参数 boc：签名编码之后的交易

```bash
curl -X 'POST' \
  'https://toncenter.com/api/v2/sendBoc' \
  -H 'accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "boc": "te6cckEBAgEAsgAB4YgAKADuKNN9dB0nK7DNJ/oB3JNQUmOkT6Y9NGTHb/vnqmYAXEr/fHZwmdDy2OyXSyiRSFcG877GI/TUh4zellK6O/gx22B7JjWiRjCmCpK1iFqOiQcAMb14wQDph47L5ezgIU1NGLsyqu6IEitX4AAcAQB4QgBIBFp3o6LsJ5GaXYw/j+SFgI93d3++UiFMlUt1eYbUvSAvrwgAAAAAAAAAAAAAAAAAAAAAAABtZW1vLp59qw=="
}'
```

- 返回值：成功返回交易 Hash

```bash
{
  "ok": true,
  "msg": "7CybhNEPAFmnaeJypOBhfpbGhnfZCCX4Ub1wJhXiMi4=",
  "code": 200
}
```

## 7. 获取预估手续费

- 请求示范

```bash
curl -X 'POST' \
  'https://toncenter.com/api/v2/estimateFee' \
  -H 'accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "address": "EQAUAHcUab66DpOV2GaT_QDuSagpMdIn0x6aMmO3_fPVMyD8",
  "body": "te6cckEBAgEAsgAB4YgAKADuKNN9dB0nK7DNJ/oB3JNQUmOkT6Y9NGTHb/vnqmYAXEr/fHZwmdDy2OyXSyiRSFcG877GI/TUh4zellK6O/gx22B7JjWiRjCmCpK1iFqOiQcAMb14wQDph47L5ezgIU1NGLsyqu6IEitX4AAcAQB4QgBIBFp3o6LsJ5GaXYw/j+SFgI93d3++UiFMlUt1eYbUvSAvrwgAAAAAAAAAAAAAAAAAAAAAAABtZW1vLp59qw==",
  "init_code": "",
  "init_data": "",
  "ignore_chksig": true
}'
```

- 返回值

```json
{
    "ok": true,
    "result": {
        "@type": "query.fees",
        "source_fees": {
            "@type": "fees",
            "in_fwd_fee": 1032400,
            "storage_fee": 1060,
            "gas_fee": 0,
            "fwd_fee": 0
        },
        "destination_fees": [],
        "@extra": "1716985542.8194273:7:0.5980081558327054"
    }
}
```

# 五. 中心化钱包开发

## **1.离线地址生成**

- 调度签名机生成密钥对，签名机吐出公钥
- 使用公钥匙导出地址

## **2. 充值逻辑**

- 获得最新块高；更新到数据库
- 从数据库中获取上次解析交易的块高做为起始块高，最新块高为截止块高，如果数据库中没有记录，说明需要从头开始扫，起始块高为 0；
- 解析区块里面的交易，to 地址是系统内部的用户地址，说明用户充值，更新交易到数据库中，将交易的状态设置为待确认。
- 所在块的交易过了确认位，将交易状态更新位充值成功并通知业务层。
- 解析到的充值交易需要在钱包的数据库里面维护 nonce, 当然也可以不维护，签名的时候去链上获取

## **3. 提现逻辑**

- 获取离线签名需要的参数，给合适的手续费
- 构建未签名的交易消息摘要，将消息摘要递给签名机签名
- 构建完整的交易并进行序列化
- 发送交易到区块链网络
- 扫链获取到交易之后更新交易状态并上报业务层

## **4. 归集逻辑**

- 将用户地址上的资金转到归集地址，签名流程类似提现
- 发送交易到区块链网络
- 扫链获取到交易之后更新交易状态

## **5. 转冷逻辑**

- 将热钱包地址上的资金转到冷钱包地址，签名流程类似提现
- 发送交易到区块链网络
- 扫链获取到交易之后更新交易状态

## **6. 冷转热逻辑**

- 手动操作转账到热钱包地址
- 扫链获取到交易之后更新交易状态

**注意：交费的学员需要完整的项目实战代码可寻求 The Web3 社区索取**

# 六 HD 钱包开发

## **1.离线地生成和离线签名**

参考上面的代码

## **2. 和链上交互的接口**

- 获取账户余额
- 根据地址获取交易记录
- 获取预估手续费

# 七. **总结**

HD 钱包和交易所钱包不同之处有以下几点

## **1. 密钥管理方式不同**

- HD 钱包私钥在本地设备，私钥用户自己控制
- 交易所钱包中心化服务器(CloadHSM, TEE 等)，私钥项目方控制

## **2. 资金存在方式不同**

- HD 资金在用户钱包地址
- 交易所钱包资金在交易所热钱包或者冷钱包里面, 用户提现的时候从交易所热钱包提取

## **3. 业务逻辑不一致**

- 中心化钱包：实时不断扫链更新交易数据和状态
- HD 钱包：根据用户的操作通过请求接口实现业务逻辑

# 八.附录

- 官方文档：https://docs.ton.org/
- V2 API 文档：https://toncenter.com/api/v2/#/
- V3 API 文档：https://toncenter.com/api/v3/
- 浏览器：https://tonscan.org/
