# KDA 钱包开发详细教程

# 一. KDA 简介

“KDA链”指的是Kadena（KDA）区块链平台。Kadena是一个基于区块链技术的高性能智能合约平台，旨在提供安全、高效和可扩展的区块链解决方案。Kadena项目由Stuart Popejoy和Will Martino于2016年创立，他们曾在摩根大通工作，并参与了JPMorgan的区块链项目Juno的开发。

## 1.**主要特性和功能**

- **Pact智能合约语言：** Kadena使用一种名为Pact的智能合约语言，它强调安全性和易用性。Pact支持正式验证（Formal Verification），这意味着开发者可以数学上证明合约的正确性，从而减少漏洞和错误。
- **Chainweb并行共识机制：** Kadena采用一种独特的共识机制称为Chainweb，这是一种并行化的工作量证明（PoW）共识协议。通过将多个独立的链并行运行并定期交叉链接，Chainweb能够实现高吞吐量和可扩展性。
- **可扩展性和高性能：** Kadena设计旨在解决传统区块链在扩展性方面的瓶颈。通过Chainweb架构，它能够在保持去中心化和安全性的同时，实现大规模扩展和高交易处理速度。
- **混合区块链架构：** Kadena提供公共链和私有链的解决方案，允许企业在私有链上运行内部应用，同时利用公共链进行更广泛的交互和验证。
- **可编程性和互操作性：** Pact智能合约支持模块化编程和互操作性，开发者可以方便地创建和连接不同的区块链应用。

## 2.**核心组件**

- **公共链（Public Chain）：** Kadena的公共链是一个去中心化的区块链网络，适用于广泛的公共和商业应用。
- **私有链（Private Chain）：** Kadena的私有链解决方案为企业提供了一个安全、可控的区块链环境，适合处理敏感数据和内部业务流程。
- **KDA代币：** KDA 是 Kadena区块链的原生加密货币，用于支付交易费用、激励矿工以及参与平台治理。

## 3.Kadena（KDA）的 **Chainweb 共识机制**

Kadena（KDA）采用一种独特的共识算法称为 **Chainweb**，这是一个并行化的工作量证明（Proof of Work, PoW）共识协议。Chainweb设计的目标是提高区块链的吞吐量和可扩展性，同时保持去中心化和安全性。

- **并行链（Parallel Chains）：** Chainweb 架构由多条并行运行的链组成，每条链都有自己的区块和哈希链。
- **交叉链接（Cross-Chain Links）：** 这些并行链定期通过交叉链接进行连接和同步。每个区块不仅包含自己的前区块的哈希，还包含来自其他并行链的区块哈希。这些交叉链接提供了一种机制，使得整个网络能够协调一致地进行共识。
- **工作量证明（PoW）：** 和传统的 PoW 一样，矿工通过解决复杂的数学问题来挖掘区块，并获得奖励。Chainweb在此基础上，通过多个链并行工作，使得网络整体上能够处理更多的交易。

3.1.**运行机制**

- **独立挖矿：** 每条链上的区块独立挖矿。矿工选择一条链并尝试挖掘下一个区块，这个过程与传统的PoW相似。
- **区块验证：** 每个新生成的区块除了包含自己的交易记录和前区块的哈希，还包括与其交叉链接的其他链的最新区块的哈希。这样，验证一个区块不仅需要验证其自身的PoW，还需要验证其交叉链接的区块。
- **全网共识：** 通过交叉链接，Chainweb 架构保证了所有并行链之间的共识。即使某一条链出现分叉，其他链的状态也能通过交叉链接进行验证和同步，从而维持整个网络的一致性。

3.2.**优势**

- **高吞吐量：** 由于多个链并行运行，Chainweb 架构能够显著提高交易处理速度，增强区块链的吞吐量。
- **高可扩展性：** 新链可以根据需要添加到网络中，从而进一步提高系统的处理能力。这种设计使得 Kadena 能够扩展以满足更大的交易需求。
- **安全性：** 通过交叉链接，Chainweb 提供了额外的安全层。攻击者需要同时攻击多个并行链，增加了攻击的难度和成本。
- **去中心化：** Chainweb 保持了 PoW 机制的去中心化特性，避免了集中化风险。

3.3.**比较与传统 PoW**

- **传统 PoW：** 比特币等传统 PoW 网络依赖单条链进行共识，处理能力受限于单链的区块生成速度和大小。
- **Chainweb：** 通过并行链和交叉链接，Chainweb 突破了单链的限制，能够并行处理大量交易，同时保持去中心化和安全性。

## 二. KDA 离线地址生成

```javascript
export function createKdaAddress (seedHex: string, addressIndex: string) {
    const { key } = derivePath("m/44'/626'/0'/0'" + addressIndex + "'", seedHex);
    const publicKey = getPublicKey(new Uint8Array(key), false).toString('hex');
    const hdWallet = {
        privateKey: key.toString('hex') + publicKey,
        publicKey,
        address: "k:" + publicKey
    };
    return JSON.stringify(hdWallet);
}
```



```javascript
export function pubKeyToAddress(params: any): string {
    const { pubKey } = params;
    return "k:" + pubKey
}
```

# 三. KDA 离线签名

```javascript
export async function signTransaction(params) {
    const {
        txObj: { toPub, from, to, amount, chainId, targetChainId, gasPrice, gasLimit, decimal, spv, pactId },
        network,
        privs
    } = params;
    const decimals = new BigNumber(10).pow(Number(decimal));
    const amountBig = new BigNumber(amount).times(decimals);
    if (amountBig.toString().indexOf(".") !== -1 || amountBig.comparedTo(new BigNumber(MAX_AMOUNT_SAFE)) > 0 || amountBig.comparedTo(new BigNumber(0)) <= 0) {
        throw new Error(`参数错误: 转账数额无效(${amountBig.toString()})`);
    }
    if (chains.indexOf(chainId.toString()) === -1) {
        throw new Error(`chainId 错误, 请选择 0 到 19`);
    }
    if (chains.indexOf(targetChainId.toString()) === -1) {
        throw new Error(`targetChainId 错误, 请选择 0 到 19`);
    }
    if (!privs || privs.length === 0) {
        throw new Error('must have private key');
    }
    let keypair = nacl.sign.keyPair.fromSecretKey(new Uint8Array(Buffer.from(privs[0].key, 'hex')));
    const sendPublickey = Buffer.from(keypair.publicKey).toString("hex");
    const privateKey = privs?.[0]?.key.length === 64 ? privs?.[0]?.key : privs?.[0]?.key.slice(0, 64);
    const networkId = network === "main_net" ? 'mainnet01' : 'testnet04';
    if (chainId === targetChainId) {
        let method = 'transferOffline';
        if (toPub && toPub.length === 64) {
            method = 'transferCreateOffline';
        }
        const params = {
            SignObj: {
                sender: from,
                senderPubKey: sendPublickey,
                senderSecretKey: privateKey,
                receiver: to,
                receiverPubKey: toPub,
                amount: amount,
                chainId: chainId.toString(),
                targetChainId: targetChainId.toString(),
                networkId: networkId,
                gasPrice: Number(gasPrice),
                gasLimit: gasLimit,
                spv: "",
                pactId: "",
            },
            method: method,
        }
        return JSON.stringify(CreateOfflineSignTx(params))
    } else {
        const params = {
            SignObj: {
                sender: from,
                senderPubKey: sendPublickey,
                senderSecretKey: privateKey,
                receiver: to,
                receiverPubKey: toPub,
                amount: amount,
                chainId: chainId.toString(),
                targetChainId: targetChainId.toString(),
                networkId: networkId,
                gasPrice: Number(gasPrice),
                gasLimit: gasLimit,
                spv: spv,
                pactId: pactId,
            },
            method: 'crossTransferOffline',
        }
        return JSON.stringify(CreateOfflineSignTx(params))
    }
}
```

**注意：若代码不完整，付费学员可以联系 The Web3 社区寻求完整代码**

# 四.  KDA 钱包对接相关接口

## 1.获取账户余额

- 接口作用：获取账户余额
- 接口参数构造 python 代码示范

```python
# -*- coding: utf-8 -*-
import json
from hashlib import blake2b
from base64 import b64encode
from datetime import datetime
import calendar

def hashbin(s):
    if not isinstance(s, bytes):
        s = s.encode('utf8')
    i = blake2b(digest_size=32)
    i.update(s)
    return i.digest()

def hash_blake(s):
    return b64encode(hashbin(s))


def str_to_timestamp(s):
    s = s[:19]
    dt = datetime.strptime(s, '%Y-%m-%d %H:%M:%S').timetuple()
    return int(calendar.timegm(dt))


def assemble_balance_payload(account):
    payload = {
        "exec": {
            "data": None,
            "code": "(coin.get-balance \"{}\")".format(account)
        }
    }
    signers = []

    nonce = datetime.utcnow()
    targe_nonce = str(nonce.strftime('%Y-%m-%d %H:%M:%S.%f UTC'))
    creation_time = str_to_timestamp(targe_nonce)
    meta = {
        "creationTime": creation_time,
        "ttl": 1800,
        "gasLimit": 1000,
        "chainId": "",
        "gasPrice": 1.0e-2,
        "sender": ""
    }

    cmd = {
        "networkId": "mainnet01",
        "payload": payload,
        "signers": signers,
        "meta": meta,
        "nonce": targe_nonce
    }
    cmd_str = json.dumps(cmd, separators=(',', ':'))
    hash_str = hash_blake(cmd_str)
    tx_hash = hash_str.decode().replace('+', '-').replace('/', '_').replace('=', '')

    raw_transaction = {
        "hash": tx_hash,
    }
    raw_transaction["sigs"] = []
    raw_transaction["cmd"] = json.dumps(cmd, separators=(',', ':'))
    return json.dumps(raw_transaction, separators=(',', ':'))


print(assemble_balance_payload("k:259e184f5c7cecf261063dd298e250b2303cf896a3d6705eedd35cc8b97cee9b"))
```

- 请求示范

```bash
curl --location 'https://api.chainweb.com/chainweb/0.0/mainnet01/chain/2/pact/api/v1/local' \
--header 'Content-Type: application/json' \
--data '{"hash":"2I4-ePSHkVU6L9YZqK6nbN-ez02gCkdtXTwzZllwpAM","sigs":[],"cmd":"{\"networkId\":\"mainnet01\",\"payload\":{\"exec\":{\"data\":null,\"code\":\"(coin.get-balance \\\"k:259e184f5c7cecf261063dd298e250b2303cf896a3d6705eedd35cc8b97cee9b\\\")\"}},\"signers\":[],\"meta\":{\"creationTime\":1717726460,\"ttl\":1800,\"gasLimit\":1000,\"chainId\":\"\",\"gasPrice\":0.01,\"sender\":\"\"},\"nonce\":\"2024-06-07 02:14:20.222256 UTC\"}"}
'
```

- 返回值

```json
{
    "gas": 20,
    "result": {
        "status": "success",
        "data": 1.459869
    },
    "reqKey": "2I4-ePSHkVU6L9YZqK6nbN-ez02gCkdtXTwzZllwpAM",
    "logs": "wsATyGqckuIvlm89hhd2j4t6RMkCrcwJe_oeCYr7Th8",
    "metaData": {
        "publicMeta": {
            "creationTime": 1717726460,
            "ttl": 1800,
            "gasLimit": 1000,
            "chainId": "",
            "gasPrice": 1.0e-2,
            "sender": ""
        },
        "blockTime": 1717103631588214,
        "prevBlockHash": "bE0BbAubTPj9bMbODafRRlvYvuo-r-0WAVzlakDMHbM",
        "blockHeight": 4821734
    },
    "continuation": null,
    "txId": null
}
```

- data 为账户余额

## 2.获取 SPV

- 接口作用：获取跨链交易需要使用的 SPV
- 接口参数： requestKey：源链交易 Hash targetChainId 目标链 ID
- 请求示范

```bash
curl --location 'https://api.chainweb.com/chainweb/0.0/mainnet01/chain/2/pact/spv' \
--header 'Content-Type: application/json' \
--data '{
  "requestKey": "POfJqi3iIEoX8vo8CWvAxtJUQvMA3OfyRWYTDSFuU4Q",
  "targetChainId": "1"
}'
```

- 返回值

```
"eyJjaGFpbiI6MSwib2JqZWN0IjoiQUFBQUVBQUFBQUFBQUFBQkFMLVBJNHJHaEp3OXF2MkN2bUdhX0stcmkzU2tmQ3NCLXlfbW5QeGl0aEdlQU9RaExvVl84MkRiUEphV3JNLU5YVGJwSXgtWVBnLXM5WlpzTlk3enp2X1dBVjhtRkcyc1RyVVN5bGVzVmlqWUxVVmFWSE1wbE1zRlhPRHRyTGNYOVdxbkFjSDBXOTREU1RyUVd0MEZ2LXh5ZDVOWWRTUHQ4M21vNjdvOGdHaTVoQXBpQUJubGVSMFFtcG5BSzl0YkJERHl3emNhVE9MUmNMWmE2N25TNUJlbUt4THdBU1ZmOU55ZFlDVGZncXNhZUYzdTliVk1uMDN3ajljbGtsOFF0NU1PY0ZueUFBVXQtTXNGOWdpNDI5TmFVVDBzMm9wWDFWdGlUQV8yX0dfLUI1VzJDQUpkQUFmclBwUm5FbDJiSkh6VWc5SFJiS3RyNnZzOUkxYnpkWHdyWGc2elJjTGJBYkhMNkhEVEJOcDREUVJsWEZ4UGwwb3A3TlBFYVJKc1Z5NXdNSkV3Sm5TY0FCOTV5cWFNeDZnXzNQNlRGZW9zYlZEZmtQU2F1elptVGZMc2pfWVlsU3lsQU5KMFBjVmFvaUxZd3ZwbG1md1hHdWxkZUpHMUJiZjV5Z0xIUExLRFF1bGpBT0ItdGhMVHM1TlpvckVJMGMwR3ZXQ01uaVVrMmJWbEI5dmJBTXlBLWlLOEFMdmdSZlA2ejJaVVRpczVBd2hUX1ZQYnUzNGRLckJEVVNYX0tueWg2ZzRuQURtZnQwMGtBUUpMT3luSzZEZWJoLXNjck9NaFYyX25qN3cyMU5SWnRYVXJBTVJoNlBDdXd2TmQyVzZFY2prcHZMMkdzMjFMQlBkM1NNMEt1bE1EcDRUVUFMbnRUcnNHNjJDaWI3X1RteG1WbDNPczRxMnBjeER3UzA3YVZIOWtwandCIiwic3ViamVjdCI6eyJpbnB1dCI6IkFCUjdJbWRoY3lJNk5qQXdMQ0p5WlhOMWJIUWlPbnNpYzNSaGRIVnpJam9pWm1GcGJIVnlaU0lzSW1WeWNtOXlJanA3SW1OaGJHeFRkR0ZqYXlJNlcxMHNJblI1Y0dVaU9pSkhZWE5GY25KdmNpSXNJbTFsYzNOaFoyVWlPaUpIWVhNZ2JHbHRhWFFnS0RZd01Da2daWGhqWldWa1pXUTZJRFl4T0NJc0ltbHVabThpT2lJaWZYMHNJbkpsY1V0bGVTSTZJbEJQWmtweGFUTnBTVVZ2V0RoMmJ6aERWM1pCZUhSS1ZWRjJUVUV6VDJaNVVsZFpWRVJUUm5WVk5GRWlMQ0pzYjJkeklqb2lVMng0TVZsSWVIaDBiMWxTVVUxUVFVUTNPRFp0UTBNMmVFSnVkM05FTjNsUU0xY3RZMHhsV0V4cU1DSXNJbVYyWlc1MGN5STZXM3NpY0dGeVlXMXpJanBiSW1zNk1qVTVaVEU0TkdZMVl6ZGpaV05tTWpZeE1EWXpaR1F5T1RobE1qVXdZakl6TUROalpqZzVObUV6WkRZM01EVmxaV1JrTXpWall6aGlPVGRqWldVNVlpSXNJams1WTJJM01EQTRaRGRrTnpCak9UUm1NVE00WTJNek5qWmhPREkxWmpCa09XTTRNMkU0WVRKbU5HSmhPREpqT0Raak5qWTJaVEJoWWpabVpXTm1NMkVpTERZdU1HVXRNbDBzSW01aGJXVWlPaUpVVWtGT1UwWkZVaUlzSW0xdlpIVnNaU0k2ZXlKdVlXMWxjM0JoWTJVaU9tNTFiR3dzSW01aGJXVWlPaUpqYjJsdUluMHNJbTF2WkhWc1pVaGhjMmdpT2lKcmJFWnJja3htY0hsTVZ5MU5NM2hxVmxCVFpIRllSVTFuZUZCUVNtbGlVblJmUkRaeGFVSjNjelp6SW4xZExDSnRaWFJoUkdGMFlTSTZiblZzYkN3aVkyOXVkR2x1ZFdGMGFXOXVJanB1ZFd4c0xDSjBlRWxrSWpwdWRXeHNmUSJ9LCJhbGdvcml0aG0iOiJTSEE1MTJ0XzI1NiJ9"
```

- 返回值即为 spv, 用户签名跨链交易发送到目标链上

## 3.获取各条链的最新块高

- 接口作用：获取 10 条链的块高
- 请求参数：无
- 请求示范

```bash
curl --location 'https://api.chainweb.com/chainweb/0.0/mainnet01/cut'
```

- 返回值

```json
{
    "hashes": {
        "17": {
            "height": 4841223,
            "hash": "8pQ-3FO29fK-rY8ba6L7xRzOPXSYwsW0Aeva_vsarM0"
        },
        "16": {
            "height": 4841224,
            "hash": "CWk5WWEkO8UTKjRzSuGUd9aRDYLTrGAfQnQ_MpJKrdA"
        },
        "19": {
            "height": 4841224,
            "hash": "rX8tLNp5lfhjBgNw3-HJGBu5NNtP8wSwZm3DLSlKE1Y"
        },
        "18": {
            "height": 4841224,
            "hash": "oLbJGcbKjM9HTVrKRpXwnQ8ievPyQjjYVe6JHQMf9U8"
        },
        "5": {
            "height": 4841223,
            "hash": "jThvZbClD2ye686iEjFY2oiFokbkbKOS8wRnE0LAyDg"
        },
        "4": {
            "height": 4841223,
            "hash": "ENJFCjL_1Yp0Iu9J3nd-qQpn9bRRei_jVIQ6hQewUqQ"
        },
        "7": {
            "height": 4841223,
            "hash": "B07c5QthGdvoLqE9Y59YmVrzslwViDZwF-jGz_5O2Ws"
        },
        "6": {
            "height": 4841224,
            "hash": "2uEikksdlPehPMfSXnfWmGMvIVnr3VvAHNt2DMqMFCc"
        },
        "1": {
            "height": 4841224,
            "hash": "g9gP4wqPPylvOCKEsdaOwnj0asA9VFkQF-zFuT2dZBY"
        },
        "0": {
            "height": 4841222,
            "hash": "RmsCuFPPPPs8NQ7u7LMR4Q_LUVklz35JILGp00PMeNg"
        },
        "3": {
            "height": 4841224,
            "hash": "G1YGper1Nwuv7orbZC2WaLopaPt1g3Qzn3kgeXE--P8"
        },
        "2": {
            "height": 4841223,
            "hash": "nsXjqewBt1fyMUovlqZ0e7PIRu8eoqvR08QDsynJf_g"
        },
        "13": {
            "height": 4841223,
            "hash": "3kVa-MelRan6U6ykEytgTpMHDPQz3zSPa5N1HepEJxQ"
        },
        "12": {
            "height": 4841224,
            "hash": "q-h33GYgEUVpFLAmBvf3eMF_iu4pmeEjHkoYMI27Nus"
        },
        "15": {
            "height": 4841223,
            "hash": "x3GOSps1hUopLPazZsrAHamvvkyw_aHJEBXGoxo7Dnk"
        },
        "14": {
            "height": 4841222,
            "hash": "Nq34vAcyzUh528914_g64gWx5crV8Py-NhResg9S-2U"
        },
        "9": {
            "height": 4841223,
            "hash": "uNZgb8dTzYrDZisAkLkoe2M8aD40E6pnhh8KbekZCOI"
        },
        "8": {
            "height": 4841223,
            "hash": "QJyADFef-9mk9wHCprRVDaw1JAaD70-BIWDw0gUbozU"
        },
        "11": {
            "height": 4841224,
            "hash": "e_utLD8zc1kGaLWDcVOIqbD_HIszEGzTa4i89jNarB4"
        },
        "10": {
            "height": 4841223,
            "hash": "DWjkLhp2Ql3rDuHA0r5yd6ji7jHp1ZZAMzErPA-ve7g"
        }
    },
    "origin": null,
    "weight": "5rRNkAj_c03zfRcAAAAAAAAAAAAAAAAAAAAAAAAAAAA",
    "height": 96824466,
    "instance": "mainnet01",
    "id": "X7QnBOavHldNR3GPlJgQ3DZq5C_PyIjIhlnX212jPPE"
}
```

- height：最新块高
- hash：区块 Hash
- height 和 hash 前面的数字是链的标号

## 4.根据块高获取块信息

- 接口作用：根据块高获取块信息
- 参数： minheight：起始区块 maxheight：结束区块
- 请求示范

```bash
curl --location 'https://api.chainweb.com/chainweb/0.0/mainnet01/chain/15/header?minheight=4841250&maxheight=4841250'
```

- 返回值

```json
{
    "limit": 2,
    "items": [
        "AAAAAAAAAADN4aO8OhoGAOBzcvf5wbn8rH8ZCxqeb4TKlOzccJXCnFMKe5aZgSnjAwAAAAAAGw4ykoZXsXlRGOkKc4fMD_0nB7K_J-wNfkI00WJyQBwOAAAAQ44Ls8M9WyUK1eRNhKcrbCaT1mDvhiMO-vtn8TLz0OAQAAAAz4vkS4PDBpMYWuUeSll4xLaH_xOBWNheawlHmeJfFvQENyJen40iRX2wyr2O_0dbSp4vqECmLFUOAAAAAAAAALtu3jpxRJ8BxPj0a_uT3vfIJc1oXF6c2YLfz0vQVRGQDwAAAIYiESH39Z_5qCwBAAAAAAAAAAAAAAAAAAAAAAAAAAAAIt9JAAAAAAAFAAAAuQz4FzoaBgCON9nm9rGPmKLtKYIyIgQUZnsku_r99c7nyMf5ccHVK-vG8LGXzMwp",
        "AAAAAAAAAADTpay8OhoGAOBzcvf5wbn8rH8ZCxqeb4TKlOzccJXCnFMKe5aZgSnjAwAAAAAAGw4ykoZXsXlRGOkKc4fMD_0nB7K_J-wNfkI00WJyQBwOAAAAQ44Ls8M9WyUK1eRNhKcrbCaT1mDvhiMO-vtn8TLz0OAQAAAAz4vkS4PDBpMYWuUeSll4xLaH_xOBWNheawlHmeJfFvQENyJen40iRX2wyr2O_0dbSp4vqECmLFUOAAAAAAAAANNH3pUUkZL8iQkYg0ar5mUjvmg0hAjPjgW-Fir0kDpBDwAAAIYiESH39Z_5qCwBAAAAAAAAAAAAAAAAAAAAAAAAAAAAIt9JAAAAAAAFAAAAuQz4FzoaBgAPbU8qDExYIu77NSBtjkJdLVYYPKuobJLHS8v-Fo6ZbjkiTqhgmR8c"
    ],
    "next": "exclusive:7vs1IG2OQl0tVhg8q6hsksdLy_4WjpluOSJOqGCZHxw"
}
```

- Item 里面的 190 到 222 取出来之后做 base64 编码之后为 Payload Hash

## 5.根据 payload hash 获取交易详情

- 接口作用：根据 payload hash 获取交易详情
- 接口参数：payload hash
- 请求示范

```bash
curl --location 'https://api.chainweb.com/chainweb/0.0/mainnet01/chain/15/payload/u27eOnFEnwHE-PRr-5Pe98glzWhcXpzZgt_PS9BVEZA/outputs'
```

- 返回值

```json
{
    "transactions": [
        [
            "eyJoYXNoIjoid2FiVnB5Y2pLSXRWUlRqdTRPMnlHZFFNTTdPVHYyT1VkMVlwQmZCNjVlbyIsInNpZ3MiOlt7InNpZyI6IjA1YWY3NGRlNzIzYjMwMzE2ZWRhNDVkN2JiMGNhZmYxNjc3NmFkZDMzMGI1YzE1NjhiMTcyYjNkODU0NjAzNDRjZDA1MmYwNDg0ZDIzYzE3Mjk5Mjk2OTIyMmYzOTgxNjQwMjU2M2I3MWY5MTE5ZGJjZWJlZTEyM2RhN2M1YjA4In1dLCJjbWQiOiJ7XCJuZXR3b3JrSWRcIjpcIm1haW5uZXQwMVwiLFwicGF5bG9hZFwiOntcImV4ZWNcIjp7XCJkYXRhXCI6e1wia3NcIjp7XCJwcmVkXCI6XCJrZXlzLWFsbFwiLFwia2V5c1wiOltcImJhOTcxZDQxYjUzZTY1MWU2NzQ2ZmFlZjljODg4NTYxNmQ2OWFmNjkyMDA1NTdkNDI4MTBjYTRhMWY1NTkxMWNcIl19fSxcImNvZGVcIjpcIihjb2luLmNyZWF0ZS1hY2NvdW50IFxcXCJrOmJhOTcxZDQxYjUzZTY1MWU2NzQ2ZmFlZjljODg4NTYxNmQ2OWFmNjkyMDA1NTdkNDI4MTBjYTRhMWY1NTkxMWNcXFwiIChyZWFkLWtleXNldCBcXFwia3NcXFwiKSlcIn19LFwic2lnbmVyc1wiOlt7XCJjbGlzdFwiOlt7XCJuYW1lXCI6XCJjb2luLkdBU1wiLFwiYXJnc1wiOltdfV0sXCJwdWJLZXlcIjpcIjVhZGIxNjY2MzA3MzI4MGFjZjYzYmMyYTRiZjQ3N2FkMTM5MTczNmRjZDYyMTdiMDk0OTI2ODYyYzcyZDE1YzlcIn1dLFwibWV0YVwiOntcImNyZWF0aW9uVGltZVwiOjE3MTc2ODkyMjYsXCJ0dGxcIjoxODAwLFwiZ2FzTGltaXRcIjozMDAwLFwiY2hhaW5JZFwiOlwiMTVcIixcImdhc1ByaWNlXCI6MWUtOCxcInNlbmRlclwiOlwiazo1YWRiMTY2NjMwNzMyODBhY2Y2M2JjMmE0YmY0NzdhZDEzOTE3MzZkY2Q2MjE3YjA5NDkyNjg2MmM3MmQxNWM5XCJ9LFwibm9uY2VcIjpcIlxcXCIyMDI0LTA2LTA2VDE1OjU0OjAxLjI3N1pcXFwiXCJ9In0",
            "eyJnYXMiOjQzMywicmVzdWx0Ijp7InN0YXR1cyI6InN1Y2Nlc3MiLCJkYXRhIjoiV3JpdGUgc3VjY2VlZGVkIn0sInJlcUtleSI6IndhYlZweWNqS0l0VlJUanU0TzJ5R2RRTU03T1R2Mk9VZDFZcEJmQjY1ZW8iLCJsb2dzIjoiR0dnRlRXR3N3Um1tTVNpWk1weGotUkJWUUFHcXRvdGh6SzItM0RQOWF4OCIsImV2ZW50cyI6W3sicGFyYW1zIjpbIms6NWFkYjE2NjYzMDczMjgwYWNmNjNiYzJhNGJmNDc3YWQxMzkxNzM2ZGNkNjIxN2IwOTQ5MjY4NjJjNzJkMTVjOSIsIms6MjUxZWZiMDZmM2I3OThkYmU3YmIzZjU4ZjUzNWI2N2IwYTllZDJkYTlhYTRlMjM2N2JlNGFiYzA3Y2M5MjdmYSIsNC4zM2UtNl0sIm5hbWUiOiJUUkFOU0ZFUiIsIm1vZHVsZSI6eyJuYW1lc3BhY2UiOm51bGwsIm5hbWUiOiJjb2luIn0sIm1vZHVsZUhhc2giOiJrbEZrckxmcHlMVy1NM3hqVlBTZHFYRU1neFBQSmliUnRfRDZxaUJ3czZzIn1dLCJtZXRhRGF0YSI6bnVsbCwiY29udGludWF0aW9uIjpudWxsLCJ0eElkIjo0NDEwNzI0fQ"
        ],
        [
            "eyJoYXNoIjoiMlMzM2l5T1E3QTNDemxOUmFBLWNoQ0llSk1Cd1o1ZzNzcWlBYV8tV2ctOCIsInNpZ3MiOlt7InNpZyI6IjMyN2ZhMjA4Yzc1YTA4MDMxZDZlY2JlNzdkZTRiMmI4N2NmMDBlNmU2NWZiMDc2OWQ3MGY0ZTlhYTNiNDFhOGI1ZTI0OTJhNjI2NzY0NDE1ODgwOGUzZTViMTNiM2Q1ODkyOGY5MGM5NjU5OGRhNjBiMDU4NmUxYTkwZjY2NjA4In1dLCJjbWQiOiJ7XCJuZXR3b3JrSWRcIjpcIm1haW5uZXQwMVwiLFwicGF5bG9hZFwiOntcImV4ZWNcIjp7XCJkYXRhXCI6e1wia3NcIjp7XCJwcmVkXCI6XCJrZXlzLWFsbFwiLFwia2V5c1wiOltcIjk4ZWM5YWQ4YzIzOGEzYmQ1MDk5NGM0OWJiNzUzMDI4NmQyNDIxNGEyNDllZTY0ZjBlNTJiYmVjZWQ3YjMxZjlcIl19fSxcImNvZGVcIjpcIihjb2luLmNyZWF0ZS1hY2NvdW50IFxcXCJrOjk4ZWM5YWQ4YzIzOGEzYmQ1MDk5NGM0OWJiNzUzMDI4NmQyNDIxNGEyNDllZTY0ZjBlNTJiYmVjZWQ3YjMxZjlcXFwiIChyZWFkLWtleXNldCBcXFwia3NcXFwiKSlcIn19LFwic2lnbmVyc1wiOlt7XCJjbGlzdFwiOlt7XCJuYW1lXCI6XCJjb2luLkdBU1wiLFwiYXJnc1wiOltdfV0sXCJwdWJLZXlcIjpcIjVhZGIxNjY2MzA3MzI4MGFjZjYzYmMyYTRiZjQ3N2FkMTM5MTczNmRjZDYyMTdiMDk0OTI2ODYyYzcyZDE1YzlcIn1dLFwibWV0YVwiOntcImNyZWF0aW9uVGltZVwiOjE3MTc2ODkzNjEsXCJ0dGxcIjoxODAwLFwiZ2FzTGltaXRcIjozMDAwLFwiY2hhaW5JZFwiOlwiMTVcIixcImdhc1ByaWNlXCI6MWUtOCxcInNlbmRlclwiOlwiazo1YWRiMTY2NjMwNzMyODBhY2Y2M2JjMmE0YmY0NzdhZDEzOTE3MzZkY2Q2MjE3YjA5NDkyNjg2MmM3MmQxNWM5XCJ9LFwibm9uY2VcIjpcIlxcXCIyMDI0LTA2LTA2VDE1OjU2OjE1Ljc4M1pcXFwiXCJ9In0",
            "eyJnYXMiOjQzMywicmVzdWx0Ijp7InN0YXR1cyI6InN1Y2Nlc3MiLCJkYXRhIjoiV3JpdGUgc3VjY2VlZGVkIn0sInJlcUtleSI6IjJTMzNpeU9RN0EzQ3psTlJhQS1jaENJZUpNQndaNWczc3FpQWFfLVdnLTgiLCJsb2dzIjoiZ3R3ZERZaHRvM2NQZDJQLXo1NHR0MGxYTlN4WW93d3lBX210TlJqTGszZyIsImV2ZW50cyI6W3sicGFyYW1zIjpbIms6NWFkYjE2NjYzMDczMjgwYWNmNjNiYzJhNGJmNDc3YWQxMzkxNzM2ZGNkNjIxN2IwOTQ5MjY4NjJjNzJkMTVjOSIsIms6MjUxZWZiMDZmM2I3OThkYmU3YmIzZjU4ZjUzNWI2N2IwYTllZDJkYTlhYTRlMjM2N2JlNGFiYzA3Y2M5MjdmYSIsNC4zM2UtNl0sIm5hbWUiOiJUUkFOU0ZFUiIsIm1vZHVsZSI6eyJuYW1lc3BhY2UiOm51bGwsIm5hbWUiOiJjb2luIn0sIm1vZHVsZUhhc2giOiJrbEZrckxmcHlMVy1NM3hqVlBTZHFYRU1neFBQSmliUnRfRDZxaUJ3czZzIn1dLCJtZXRhRGF0YSI6bnVsbCwiY29udGludWF0aW9uIjpudWxsLCJ0eElkIjo0NDEwNzI3fQ"
        ]
    ],
    "minerData": "eyJhY2NvdW50IjoiazoyNTFlZmIwNmYzYjc5OGRiZTdiYjNmNThmNTM1YjY3YjBhOWVkMmRhOWFhNGUyMzY3YmU0YWJjMDdjYzkyN2ZhIiwicHJlZGljYXRlIjoia2V5cy1hbGwiLCJwdWJsaWMta2V5cyI6WyIyNTFlZmIwNmYzYjc5OGRiZTdiYjNmNThmNTM1YjY3YjBhOWVkMmRhOWFhNGUyMzY3YmU0YWJjMDdjYzkyN2ZhIl19",
    "transactionsHash": "2sXMEnpo9UDZRQV2ZNfhiwWYiYK98tOqRXPe6QXxkrs",
    "outputsHash": "EwbSZ1XlkLFYTRz3auf9spIrXxruVVpjvMf6-JhttQM",
    "payloadHash": "u27eOnFEnwHE-PRr-5Pe98glzWhcXpzZgt_PS9BVEZA",
    "coinbase": "eyJnYXMiOjAsInJlc3VsdCI6eyJzdGF0dXMiOiJzdWNjZXNzIiwiZGF0YSI6IldyaXRlIHN1Y2NlZWRlZCJ9LCJyZXFLZXkiOiJJalJJVG5rNVgyNUNkV1o1YzJaNGEweEhjRFYyYUUxeFZUZE9lSGRzWTB0alZYZHdOMnh3YlVKTFpVMGkiLCJsb2dzIjoieDhNWWRnZXljSDBmTTRSWXhoenF3M05kWmlyMDFYNHdobWlPRzB0eVo4NCIsImV2ZW50cyI6W3sicGFyYW1zIjpbIiIsIms6MjUxZWZiMDZmM2I3OThkYmU3YmIzZjU4ZjUzNWI2N2IwYTllZDJkYTlhYTRlMjM2N2JlNGFiYzA3Y2M5MjdmYSIsMC45ODMwMjY1XSwibmFtZSI6IlRSQU5TRkVSIiwibW9kdWxlIjp7Im5hbWVzcGFjZSI6bnVsbCwibmFtZSI6ImNvaW4ifSwibW9kdWxlSGFzaCI6ImtsRmtyTGZweUxXLU0zeGpWUFNkcVhFTWd4UFBKaWJSdF9ENnFpQndzNnMifV0sIm1ldGFEYXRhIjpudWxsLCJjb250aW51YXRpb24iOm51bGwsInR4SWQiOjQ0MTA3MjJ9"
}
```

- 返回的接口交易解析伪代码如下

```javascript
let timeStamp;
let fromAddrs: any = [];
let toAddrs: any = [];
let amounts: any = [];
const dataBuf = base64url.toBuffer(blockTxResJson.items[i]);
const payloadReq = base64url.encode(dataBuf.slice(190, 222));
const txReqOps = {
    url: config.host + `/chainweb/0.0/mainnet01/chain/${chainId}/payload/${payloadReq}/outputs`,
};
let txResOpsJson: any = await requestApi(txReqOps, true, { get: true });
for(let j =0; j < txResOpsJson.transactions.length; j++) {
    const signCmd = JSON.parse(base64url.decode(txResOpsJson.transactions[j][0]));
    const txInfo = JSON.parse(base64url.decode(txResOpsJson.transactions[j][1]));
    if (txInfo.result.status === "success" && txInfo.reqKey === txHash) {
        const payload = JSON.parse(signCmd.cmd).payload
        timeStamp = JSON.parse(signCmd.cmd).meta.creationTime
        for (let key in payload){
            if(key === "cont") {
                if (payload.cont && payload.cont.proof !== undefined && payload.cont.step !== undefined && payload.cont.step == 1) {
                    if(txInfo.continuation !== null) {
                        const transfer = txInfo.continuation.continuation
                        if (transfer.def === "coin.transfer-crosschain") {
                            fromAddrs.push(transfer.args[0])
                            toAddrs.push(transfer.args[1])
                            amounts.push(transfer.args[4].toString())
                        }
                    }
                }
            }
            if(key === "exec") {
                const payload_code = payload.exec.code
                const m = payload_code.match(/\((.*?) /)
                if (m.indexOf("coin.transfer-crosschain") !== -1 || m.indexOf("coin.transfer-create") !== -1 || m.indexOf("coin.transfer") !== -1) {
                    const signers = JSON.parse(signCmd.cmd).signers
                    if (signers.length === 0 || signers[0].clist.length >= 3) {
                        continue
                    }
                    for(let k = 0; k < signers[0].clist.length; k++) {
                        if (signers[0].clist[k].name === "coin.TRANSFER" || signers[0].clist[k].name === "coin.TRANSFER_XCHAIN") {
                            const transferArgs = signers[0].clist[k].args
                            if(transferArgs.length === 4 || transferArgs.length === 3) {
                                fromAddrs.push(transferArgs[0])
                                toAddrs.push(transferArgs[1])
                                let amount = transferArgs[2]
                                for (let key in transferArgs[2]){
                                    if(key === "decimal") {
                                        amount = transferArgs[2].decimal
                                    }
                                }
                                amounts.push(amount.toString())
                            }
                        }
                    }
                }
            }
        }
    }
}
    
fromAddress: fromAddrs ,
toAddress: toAddrs ,
amount: amounts,
timestamp: timeStamp,
confirms: lastestBlock - Number(blockHeight),  
```

## 6.广播交易到区块链网络

- 接口作用：发送交易到区块链网络
- 接口参数：离线签名的交易体
- 请求示范

```bash
curl --location 'https://api.chainweb.com/chainweb/0.0/mainnet01/chain/2/pact/api/v1/send' \
--header 'Content-Type: application/json' \
--data '{"cmds":[{"hash":"uSOYjbztP-C1_zEZk0Gka-RTKyuozwiQWt9IskTQv1I","sigs":[{"sig":"adeb64689c43cbd3c27ff4a692803cf990433554fe4b4d379894c2f9ebc934f1689ee3df6e66416308fe4403893ecb0bf83399b71942044eec521cf01eec9809"}],"cmd":"{\"networkId\":\"mainnet01\",\"payload\":{\"exec\":{\"data\":{},\"code\":\"(coin.transfer \\\"k:259e184f5c7cecf261063dd298e250b2303cf896a3d6705eedd35cc8b97cee9b\\\" \\\"k:6a7bb957fedd2c7e14edb76e66be3c588881f121185aa5d7cb1cb436155c6164\\\" 0.01)\"}},\"signers\":[{\"clist\":[{\"name\":\"coin.TRANSFER\",\"args\":[\"k:259e184f5c7cecf261063dd298e250b2303cf896a3d6705eedd35cc8b97cee9b\",\"k:6a7bb957fedd2c7e14edb76e66be3c588881f121185aa5d7cb1cb436155c6164\",0.01]},{\"name\":\"coin.GAS\",\"args\":[]}],\"pubKey\":\"259e184f5c7cecf261063dd298e250b2303cf896a3d6705eedd35cc8b97cee9b\"}],\"meta\":{\"creationTime\":1717723428,\"ttl\":600,\"gasLimit\":600,\"chainId\":\"2\",\"gasPrice\":0.0001,\"sender\":\"k:259e184f5c7cecf261063dd298e250b2303cf896a3d6705eedd35cc8b97cee9b\"},\"nonce\":\"\\\"2024-06-07T01:24:02.801Z\\\"\"}"}]}
'
```

- 返回值

```json
{
    "requestKeys": [
        "uSOYjbztP-C1_zEZk0Gka-RTKyuozwiQWt9IskTQv1I"
    ]
}
```

- requestKeys：交易 Hash

# 五.  和钱包相关的特点总结

## 1.Gas 经验值

- STATIC_FEE = dec('0.006') 
- STATIC_GAS_PRICE = dec('0.00003')

## 2. 跨链交易特点

- KDA 整个项目由 20 条链组成，链与链之间的资产独立，可以互相跨转

跨链交易流程如下：

- 源链发送一笔离线签名交易
- 使用 RequestKey 和目标链 chainID 做为接口参数获取 spv
- 使用 SPV 和 PactId(RequestKey 做为 PactId )离线签名之后目标链上再发一笔交易

## **3. 其他特点**

- 算法：Ed25519
- 代币精度：12

# 六. 附录

- Github: https://github.com/kadena-io/
- 开放 API 节点：[https://api.chainweb.com](https://api.chainweb.com/)
- 浏览器：https://explorer.chainweb.com/mainnet
- 钱包：https://chainweaver.kadena.network/
- API 文档：https://docs.kadena.io/reference/chainweb-ref
- 官方 docs: https://docs.kadena.io/