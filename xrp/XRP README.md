# XRP 钱包开发详细教程

# 一. XRP 简介

XRP是一个由Ripple Labs开发的数字资产和支付协议

## **1.背景**

- **Ripple Labs**: 于2012年创建，旨在解决现有金融系统中的支付问题。他们开发了一系列解决方案，其中包括Ripple支付协议和XRP数字资产。
- **支付问题**: 传统跨境支付通常昂贵、缓慢和不透明。Ripple旨在通过其协议和数字资产解决这些问题，使国际支付更快速、便宜和可追踪。

## **2. 技术架构**

- **Ripple 协议**: 一种开放式支付协议，使实时交换任何形式的货币、商品或价值成为可能。它基于分布式总账技术，称为Ripple共识账本，这意味着交易可以在网络中快速验证和确认，而无需中央控制机构。
- **XRP Ledger**: Ripple的公共分布式总账，用于记录和验证XRP交易。它是一种开放源代码协议，任何人都可以查看和参与。

## **3.XRP 的用途**

- **中继货币**: XRP 最初设计为一种中继货币，用于促进跨境支付。通过使用 XRP，机构可以快速转移资金，而无需预先持有大量本地货币。
- **流动性提供者**: XRP还可以用作流动性提供者，支持Ripple网络上的交易。流动性提供者通过在XRP Ledger上发布交易来提供资金，从中获得一定的费用。
- **代币发行**: XRP还可以用于发行新的数字代币，这些代币可以代表任何形式的价值，如股票、债券或商品。这种功能使得XRP可以作为资产发行平台。

## **4. 生态系统和合作伙伴关系**

- **金融机构**: Ripple与世界各地的金融机构建立了合作关系，以提供更快速、便宜的跨境支付解决方案。这些机构可以直接使用Ripple协议和XRP数字资产，也可以通过Ripple提供的解决方案来接入。
- **技术公司**: Ripple与其他技术公司合作，推动区块链和数字支付技术的发展。这些合作伙伴关系有助于扩大Ripple生态系统的影响力和可用性。

## **5. 法律和监管**

- **合规性**: Ripple致力于遵守全球各地的法律和监管要求。他们与监管机构合作，确保其解决方案和数字资产符合法规。
- **监管挑战**: 由于加密货币行业的快速发展，监管环境可能会发生变化。Ripple必须不断适应这些变化，并确保其业务的合规性。

## **6. XRP 的共识算法**

XRP 的共识算法是称为“XRP 共识协议”（XRP Consensus Protocol）的一种机制，旨在通过一种高效、快速和安全的方式对交易进行验证和确认。这种共识算法与比特币的工作证明（Proof of Work）和以太坊的权益证明（Proof of Stake）等不同，其核心概念是一种称为“一致性”的协议，这是一种更快速的达成共识的方法。

以下是XRP共识算法的一些关键特点：

- **一致性：**XRP共识算法通过一种称为“联合验证”的过程来实现一致性。在这个过程中，网络中的验证节点通过投票达成一致，以确定哪些交易可以被包含在下一个账本中。
- **联合验证：** 在联合验证中，验证节点会在特定的时间间隔内共同选择提案中的交易，并验证这些交易的有效性。他们会通过投票表达自己的选择，并试图达成共识。
- **比较快速：**XRP共识算法的设计使得交易确认速度非常快。通常，一笔交易的确认时间大约为3到5秒，远远快于比特币等其他加密货币的确认时间。
- **无需“挖矿”：**与工作证明或权益证明机制不同，XRP共识算法无需“挖矿”，因此不需要大量的计算资源。这使得XRP 网络更加环保和高效。
- **分布式网络：**XRP 共识算法建立在一个分布式网络之上，没有中心化的控制机构。这意味着网络中的任何节点都可以参与共识过程，而不需要特殊的权限或身份。

# 二.  XRP 离线地址生成

```javascript
export function createHdWallet(seedHex, addressIndex) {
    const root = bip32.fromSeed(Buffer.from(seedHex, 'hex'));
    let path = "m/44'/144'/0'/0'/" + addressIndex + '';
    const child = root.derivePath(path);
    const params = {
        pubKey: Buffer.from(child.publicKey).toString('hex')
    }
    const address = pubKeyToAddress(params)
    return {
        privateKey: Buffer.from(child.privateKey).toString('hex'),
        publicKey: Buffer.from(child.publicKey).toString('hex'),
        address: address
    };
}

export function pubKeyToAddress({ pubKey }): string {
    if (!pubKey) {
        throw new Error("pubKey 不能为空 ");
    }
    try {
        return Keypair.deriveAddress(pubKey);
    } catch (error) {
        throw Error("Invalid public key.");
    }
}
```

# 三.  XRP 离线签名

```javascript
export function signTransaction(params) {
    const {
        txObj: {
            from, to, amount, tag, sequence, fee, decimal, currency, issuer, transactionType,
            sendMaxValue,
        },
        privateKey
    } = params;

    const calcAmount = new BigNumber(amount).times(new BigNumber(10).pow(decimal)).toString();
    if (calcAmount.indexOf(".") !== -1) throw new Error("decimal 无效");

    const publicKey = getPubKeyByPrivateKey(privateKey);

    let txnObj: any;
    if(transactionType==="TransferTokens"){
        txnObj = {
            TransactionType: 'Payment',
            Account: from,
            Destination: to,
            SendMax: {
                currency,
                issuer,
                value: sendMaxValue
            },
            Amount: {
                currency,
                issuer,
                value: amount
            },
            Fee: fee,
            Sequence: sequence,
            SigningPubKey: publicKey,
        };
    }else{
        txnObj = {
            TransactionType: 'Payment',
            Account: from,
            Destination: to,
            Amount: calcAmount,
            Fee: fee,
            Sequence: sequence,
            SigningPubKey: publicKey,
        };
    }
    if(tag !== undefined && tag !== null) txnObj.DestinationTag =  tag;
    return generateRawT(txnObj, privateKey);
}
```

# 四.  XRP 和钱包相关的接口

## 1.获取签名的 currency 和 issuer

- 接口方法：account_offers
- 接口参数 account：用户地址 ledger_index：账本索引
- 请求示范

```bash
curl --location 'https://s1.ripple.com:51234/' \
--header 'Content-Type: application/json' \
--data '{
    "method": "account_offers",
    "params": [
        {
            "account": "rGDreBvnHrX1get7na3J4oowN19ny4GzFn",
            "ledger_index":"current"
        }
    ]
}'
```

- 返回值

```json
{
  "id": 9,
  "status": "success",
  "type": "response",
  "result": {
    "account": "rpP2JgiMyTF5jR5hLG3xHCPi1knBb1v9cM",
    "ledger_current_index": 18539550,
    "offers": [
      {
        "flags": 0,
        "quality": "0.00000000574666765650638",
        "seq": 6577664,
        "taker_gets": "33687728098",
        "taker_pays": {
          "currency": "EUR",
          "issuer": "rhub8VRN55s94qWKDv6jmDy1pUykJzF3wq",
          "value": "193.5921774819578"
        }
      }
      ... trimmed for length ...
    ],
    "validated": false
  }
}
```

- currency：货币名称（签名需要的参数）
- issuer：发行者（签名需要的参数）
- value：账户金额

## 2.获取账户的 Sequence

- 接口方法：account_info
- 接口参数 account：账户地址 ledger_index：账本索引
- 请求示范

```bash
curl --location 'https://s1.ripple.com:51234/' \
--header 'Content-Type: application/json' \
--data '{
    "method": "account_info",
    "params": [
        {
            "account": "rLPHHJh3Cin2E7D3aZPgMX62YS16RHBAGG",
            "ledger_index": "current",
            "queue": true
        }
    ]
}'
```

- 返回值

```json
{
    "result": {
        "account_data": {
            "Account": "rLPHHJh3Cin2E7D3aZPgMX62YS16RHBAGG",
            "Balance": "19569500",
            "Flags": 0,
            "LedgerEntryType": "AccountRoot",
            "OwnerCount": 0,
            "PreviousTxnID": "D52061577250FC306C784E37179ACE0EFF1688C7FD55F8972D90AE219CA19F12",
            "PreviousTxnLgrSeq": 88545344,
            "Sequence": 88545344,
            "index": "A2231B25F5C66D0288F4130F63D468D2BCE7BD28A4FD25BBE92C187EB2BF540D"
        },
        "account_flags": {
            "allowTrustLineClawback": false,
            "defaultRipple": false,
            "depositAuth": false,
            "disableMasterKey": false,
            "disallowIncomingCheck": false,
            "disallowIncomingNFTokenOffer": false,
            "disallowIncomingPayChan": false,
            "disallowIncomingTrustline": false,
            "disallowIncomingXRP": false,
            "globalFreeze": false,
            "noFreeze": false,
            "passwordSpent": false,
            "requireAuthorization": false,
            "requireDestinationTag": false
        },
        "ledger_current_index": 88545752,
        "queue_data": {
            "txn_count": 0
        },
        "validated": false,
        "status": "success"
    },
    "status": "success",
    "type": "response",
    "forwarded": true,
    "warnings": [
        {
            "id": 2001,
            "message": "This is a clio server. clio only serves validated data. If you want to talk to rippled, include 'ledger_index':'current' in your request"
        }
    ]
}
```

- Sequence：为签名需要的参数

## 3. 获取最新块

- 请求示范

```bash
curl --location 'https://s1.ripple.com:51234/' \
--header 'Content-Type: application/json' \
--data '{
    "method": "ledger",
    "params": [
        {
            "ledger_index": "validated",
            "transactions": false,
            "expand": false,
            "owner_funds": false
        }
    ]
}'
```

- 返回值

```json
{
    "result": {
        "ledger_hash": "AE211D4F9DB6A8E66FE93D1689B051E13BAD9D6377F2D449FB09C6C90F883FCE",
        "ledger_index": 88550384,
        "validated": true,
        "ledger": {
            "account_hash": "E3EF05EDA3397B2A31B3A1366AD13292916D3DA23955C55AE1B6E749773F9D52",
            "close_flags": 0,
            "close_time": 771175011,
            "close_time_human": "2024-Jun-08 15:16:51.000000000 UTC",
            "close_time_resolution": 10,
            "close_time_iso": "2024-06-08T15:16:51Z",
            "ledger_hash": "AE211D4F9DB6A8E66FE93D1689B051E13BAD9D6377F2D449FB09C6C90F883FCE",
            "parent_close_time": 771175010,
            "parent_hash": "F12AB71EFE9BAB87FA01DA21F59EAD36104D297C0E89D1B60296A736B6B57B0D",
            "total_coins": "99987515687904183",
            "transaction_hash": "03EE4C16E0C54A1B4C4DF0DED2A8E627DE06A8949360726B075CD2001A24B2F6",
            "ledger_index": "88550384",
            "closed": true
        },
        "status": "success"
    },
    "warnings": [
        {
            "id": 2001,
            "message": "This is a clio server. clio only serves validated data. If you want to talk to rippled, include 'ledger_index':'current' in your request"
        }
    ]
}
```

ledger_index：为最新块高

- ledger_hash：为最新块高 Hash

## 4. 根据块号获取块里面的信息

- 请求参数 ledger_hash：账本 Hash,  类似其他链的区块 Hash
- 请求示范

```bash
curl --location 'https://s1.ripple.com:51234/' \
--header 'Content-Type: application/json' \
--data '{
    "method": "ledger_data",
    "params": [
        {
            "binary": true,
            "ledger_hash": "AE211D4F9DB6A8E66FE93D1689B051E13BAD9D6377F2D449FB09C6C90F883FCE",
            "limit": 1
        }
    ]
}'
```

- 返回值

```json
{
    "result": {
        "ledger_hash": "AE211D4F9DB6A8E66FE93D1689B051E13BAD9D6377F2D449FB09C6C90F883FCE",
        "ledger_index": 88550384,
        "validated": true,
        "state": [
            {
                "data": "11007222002200002504D5FD0B37000000000000002838000000000000000055EEF262B9485368FD2DB14F0A4EEC7D06C0D979F541F26A9CDD54965A4D0B4C4B6294E10A4CFC6940000000000000000000000000005345430000000000000000000000000000000000000000000000000166800000000000000000000000000000000000000053454300000000000250260AE1E4202439AFB5F9927067D48F8B17DC67D61C6B728800DCA4000000000000000000000000534543000000000047021569ECC348FD11FECFF66610D1F4249A3D41",
                "index": "000000E42EDA8F440C16D376B5FC6DE1370CE38C8A5D2965107E8DDBEAF4B007"
            }
        ],
        "ledger": {
            "ledger_data": "05472BF001633A1DA28D23B7F12AB71EFE9BAB87FA01DA21F59EAD36104D297C0E89D1B60296A736B6B57B0D03EE4C16E0C54A1B4C4DF0DED2A8E627DE06A8949360726B075CD2001A24B2F6E3EF05EDA3397B2A31B3A1366AD13292916D3DA23955C55AE1B6E749773F9D522DF732622DF732630A00",
            "closed": true
        },
        "marker": "000000E42EDA8F440C16D376B5FC6DE1370CE38C8A5D2965107E8DDBEAF4B007",
        "status": "success"
    },
    "warnings": [
        {
            "id": 2001,
            "message": "This is a clio server. clio only serves validated data. If you want to talk to rippled, include 'ledger_index':'current' in your request"
        }
    ]
}
```

- state 里面为交易 Json 格式编码的 hex

## 5.根据交易 Hash 获取交易详情

- 接口名称：tx
- 接口参数 transaction：交易 Hash
- 请求示范

```bash
curl --location 'https://s1.ripple.com:51234/' \
--header 'Content-Type: application/json' \
--data '{
    "method": "tx",
    "params": [
        {
            "transaction": "0342E45A7CF2A2D605CC76FE98309479AAF1E32779446F15A27B6CE9B6F5AAA8",
            "binary": false
        }
    ]
}'
```

- 返回值

```json
{
    "result": {
        "Account": "rLPHHJh3Cin2E7D3aZPgMX62YS16RHBAGG",
        "Amount": "1000000",
        "Destination": "rwsZXRC9vj9W9XJY3kuKNiHUBnctSTiXV9",
        "DestinationTag": 0,
        "Fee": "1000",
        "Sequence": 88545346,
        "SigningPubKey": "021E8DCCC213247D1C42662EF7A38B63713BFF154560ED1BF8E908268DCCFABD4E",
        "TransactionType": "Payment",
        "TxnSignature": "3044022068D277180D26C29D5AFCE3917441AC777C73BC86309BEA5530F13BA51C4BD9CE0220066B3C51C1329BA752157EDFA11BA104D0CAA4F83FF7FDA26F4556AE10580D10",
        "hash": "0342E45A7CF2A2D605CC76FE98309479AAF1E32779446F15A27B6CE9B6F5AAA8",
        "DeliverMax": "1000000",
        "ctid": "C547229A00430000",
        "meta": {
            "AffectedNodes": [
                {
                    "ModifiedNode": {
                        "FinalFields": {
                            "Account": "rwsZXRC9vj9W9XJY3kuKNiHUBnctSTiXV9",
                            "Balance": "12999500",
                            "Flags": 0,
                            "OwnerCount": 0,
                            "Sequence": 88545188
                        },
                        "LedgerEntryType": "AccountRoot",
                        "LedgerIndex": "78FDFCD71766ED44492916B0FDE5B35F8D60CF15108EA152B267C91F84D8316A",
                        "PreviousFields": {
                            "Balance": "11999500"
                        },
                        "PreviousTxnID": "0027A9B9AFA3BCD0CC2EF66D83649349B3D2CC149EE62DB8F26E94FF946E642A",
                        "PreviousTxnLgrSeq": 88547978
                    }
                },
                {
                    "ModifiedNode": {
                        "FinalFields": {
                            "Account": "rLPHHJh3Cin2E7D3aZPgMX62YS16RHBAGG",
                            "Balance": "16567400",
                            "Flags": 0,
                            "OwnerCount": 0,
                            "Sequence": 88545347
                        },
                        "LedgerEntryType": "AccountRoot",
                        "LedgerIndex": "A2231B25F5C66D0288F4130F63D468D2BCE7BD28A4FD25BBE92C187EB2BF540D",
                        "PreviousFields": {
                            "Balance": "17568400",
                            "Sequence": 88545346
                        },
                        "PreviousTxnID": "0027A9B9AFA3BCD0CC2EF66D83649349B3D2CC149EE62DB8F26E94FF946E642A",
                        "PreviousTxnLgrSeq": 88547978
                    }
                }
            ],
            "TransactionIndex": 67,
            "TransactionResult": "tesSUCCESS",
            "delivered_amount": "1000000"
        },
        "validated": true,
        "date": 771165772,
        "ledger_index": 88547994,
        "inLedger": 88547994,
        "status": "success"
    },
    "warnings": [
        {
            "id": 2001,
            "message": "This is a clio server. clio only serves validated data. If you want to talk to rippled, include 'ledger_index':'current' in your request"
        }
    ]
}
```

- Account：出金地址
- Amount：转账金额
- Destination：目标地址
- Fee：消耗的手续费
- DestinationTag: 转账标签

## 6.发送交易到区块链网络

- 接口方法：submit
- 接口参数 tx_blob：离线签名之后的交易
- 请求示范

```bash
curl --location 'https://s1.ripple.com:51234/' \
--header 'Content-Type: application/json' \
--data '{
    "method": "submit",
    "params": [
        {
            "tx_blob": "12000024054718422E000000006140000000000F42406840000000000003E87321021E8DCCC213247D1C42662EF7A38B63713BFF154560ED1BF8E908268DCCFABD4E74463044022068D277180D26C29D5AFCE3917441AC777C73BC86309BEA5530F13BA51C4BD9CE0220066B3C51C1329BA752157EDFA11BA104D0CAA4F83FF7FDA26F4556AE10580D108114D4A121B65577D6BF511C9A10159B4605CD277CF1831463351D37207D16BC7B3D9AE8271AF320259D7AE6"
        }
    ]
}'
```

- 返回值

```json
{
    "result": {
        "accepted": true,
        "account_sequence_available": 88545347,
        "account_sequence_next": 88545347,
        "applied": true,
        "broadcast": true,
        "engine_result": "tesSUCCESS",
        "engine_result_code": 0,
        "engine_result_message": "The transaction was applied. Only final in a validated ledger.",
        "kept": true,
        "open_ledger_cost": "10",
        "queued": false,
        "tx_blob": "12000024054718422E000000006140000000000F42406840000000000003E87321021E8DCCC213247D1C42662EF7A38B63713BFF154560ED1BF8E908268DCCFABD4E74463044022068D277180D26C29D5AFCE3917441AC777C73BC86309BEA5530F13BA51C4BD9CE0220066B3C51C1329BA752157EDFA11BA104D0CAA4F83FF7FDA26F4556AE10580D108114D4A121B65577D6BF511C9A10159B4605CD277CF1831463351D37207D16BC7B3D9AE8271AF320259D7AE6",
        "tx_json": {
            "Account": "rLPHHJh3Cin2E7D3aZPgMX62YS16RHBAGG",
            "Amount": "1000000",
            "Destination": "rwsZXRC9vj9W9XJY3kuKNiHUBnctSTiXV9",
            "DestinationTag": 0,
            "Fee": "1000",
            "Sequence": 88545346,
            "SigningPubKey": "021E8DCCC213247D1C42662EF7A38B63713BFF154560ED1BF8E908268DCCFABD4E",
            "TransactionType": "Payment",
            "TxnSignature": "3044022068D277180D26C29D5AFCE3917441AC777C73BC86309BEA5530F13BA51C4BD9CE0220066B3C51C1329BA752157EDFA11BA104D0CAA4F83FF7FDA26F4556AE10580D10",
            "hash": "0342E45A7CF2A2D605CC76FE98309479AAF1E32779446F15A27B6CE9B6F5AAA8"
        },
        "validated_ledger_index": 88547992,
        "status": "success"
    },
    "status": "success",
    "type": "response",
    "forwarded": true,
    "warnings": [
        {
            "id": 2001,
            "message": "This is a clio server. clio only serves validated data. If you want to talk to rippled, include 'ledger_index':'current' in your request"
        }
    ]
}
```

- engine_result：tesSUCCESS 表示执行成
- accepted：表示交易已经被接受

# 五.  XRP 钱包开发特点

- 交易带 Memo
- 有代币，代币签名提需要有 issuer 的信息

# 六.  附录

- Github:  https://github.com/XRPLF

- 官网：https://xrpl.org/

  基金会：https://foundation.xrpl.org/

- 开发者工具：https://xrpl.org/resources/dev-tools/

- Aptos 浏览器：

  https://xrpscan.com/

  https://bithomp.com/en

  https://livenet.xrpl.org/

- API 文档：https://xrpl.org/docs/references/http-websocket-apis/