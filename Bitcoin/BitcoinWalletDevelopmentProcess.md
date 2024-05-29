# Bitcoin 钱包开发流程

# 1.地址生成

```javascript
import * as bitcoin from 'bitcoinjs-lib';
import * as ecc from 'tiny-secp256k1';
const { BIP32Factory } = require('bip32');
const bip32 = BIP32Factory(ecc);

export function createAddress (params: any): any {
  const { seedHex, receiveOrChange, addressIndex, network, method } = params;
  const root = bip32.fromSeed(Buffer.from(seedHex, 'hex'));
  let path = "m/44'/0'/0'/0/" + addressIndex + '';
  if (receiveOrChange === '1') {
    path = "m/44'/0'/0'/1/" + addressIndex + '';
  }
  const child = root.derivePath(path);
  let address: string;
  switch (method) {
    case 'p2pkh':
      // eslint-disable-next-line no-case-declarations
      const p2pkhAddress = bitcoin.payments.p2pkh({
        pubkey: child.publicKey,
        network: bitcoin.networks[network]
      });
      address = p2pkhAddress.address;
      break;
    case 'p2wpkh':
      // eslint-disable-next-line no-case-declarations
      const p2wpkhAddress = bitcoin.payments.p2wpkh({
        pubkey: child.publicKey,
        network: bitcoin.networks[network]
      });
      address = p2wpkhAddress.address;
      break;
    case 'p2sh':
      // eslint-disable-next-line no-case-declarations
      const p2shAddress = bitcoin.payments.p2sh({
        redeem: bitcoin.payments.p2wpkh({
          pubkey: child.publicKey,
          network: bitcoin.networks[network]
        })
      });
      address = p2shAddress.address;
      break;
    default:
      console.log('This way can not support');
  }

  return {
    privateKey: Buffer.from(child.privateKey).toString('hex'),
    publicKey: Buffer.from(child.publicKey).toString('hex'),
    address
  };
}

export function createMultiSignAddress (params: any): string {
  const { pubkeys, network, method, threshold } = params;
  switch (method) {
    case 'p2pkh':
      return bitcoin.payments.p2sh({
        redeem: bitcoin.payments.p2ms({
          m: threshold,
          network: bitcoin.networks[network],
          pubkeys
        })
      }).address;
    case 'p2wpkh':
      return bitcoin.payments.p2wsh({
        redeem: bitcoin.payments.p2ms({
          m: threshold,
          network: bitcoin.networks[network],
          pubkeys
        })
      }).address;
    case 'p2sh':
      return bitcoin.payments.p2sh({
        redeem: bitcoin.payments.p2wsh({
          redeem: bitcoin.payments.p2ms({
            m: threshold,
            network: bitcoin.networks[network],
            pubkeys
          })
        })
      }).address;
    default:
      console.log('This way can not support');
      return '0x00';
  }
}

export function createSchnorrAddress (params: any): any {
  bitcoin.initEccLib(ecc);

  const { seedHex, receiveOrChange, addressIndex } = params;
  const root = bip32.fromSeed(Buffer.from(seedHex, 'hex'));
  let path = "m/44'/0'/0'/0/" + addressIndex + '';
  if (receiveOrChange === '1') {
    path = "m/44'/0'/0'/1/" + addressIndex + '';
  }
  const childKey = root.derivePath(path);
  const privateKey = childKey.privateKey;
  if (!privateKey) throw new Error('No private key found');

  const publicKey = childKey.publicKey;

  const tweak = bitcoin.crypto.taggedHash('TapTweak', publicKey.slice(1, 33));
  const tweakedPublicKey = Buffer.from(publicKey);
  for (let i = 0; i < 32; ++i) {
    tweakedPublicKey[1 + i] ^= tweak[i];
  }

  const { address } = bitcoin.payments.p2tr({
    internalPubkey: tweakedPublicKey.slice(1, 33)
  });

  return {
    privateKey: Buffer.from(childKey.privateKey).toString('hex'),
    publicKey: Buffer.from(childKey.publicKey).toString('hex'),
    address
  };
}
```

# 2. 离线签名

```javascript
const ecc = require('tiny-secp256k1');
const { BIP32Factory } = require('bip32');
BIP32Factory(ecc);
const bitcoin = require('bitcoinjs-lib');
const bitcore = require('bitcore-lib');

/**
 * @returns
 * @param params
 */
export function buildAndSignTx (params: { privateKey: string; signObj: any; network: string; }): string {
  const { privateKey, signObj, network } = params;
  const net = bitcore.Networks[network];
  const inputs = signObj.inputs.map(input => {
    return {
      address: input.address,
      txId: input.txid,
      outputIndex: input.vout,
      // eslint-disable-next-line new-cap
      script: new bitcore.Script.fromAddress(input.address).toHex(),
      satoshis: input.amount
    };
  });
  const outputs = signObj.outputs.map(output => {
    return {
      address: output.address,
      satoshis: output.amount
    };
  });
  const transaction = new bitcore.Transaction(net).from(inputs).to(outputs);
  transaction.version = 2;
  transaction.sign(privateKey);
  return transaction.toString();
}

export function buildUnsignTxAndSign (params) {
  const { keyPair, signObj, network } = params;
  const psbt = new bitcoin.Psbt({ network });
  const inputs = signObj.inputs.map(input => {
    return {
      address: input.address,
      txId: input.txid,
      outputIndex: input.vout,
      // eslint-disable-next-line new-cap
      script: new bitcore.Script.fromAddress(input.address).toHex(),
      satoshis: input.amount
    };
  });
  psbt.addInput(inputs);

  const outputs = signObj.outputs.map(output => {
    return {
      address: output.address,
      satoshis: output.amount
    };
  });
  psbt.addOutput(outputs);
  psbt.toBase64();

  psbt.signInput(0, keyPair);
  psbt.finalizeAllInputs();

  const signedTransaction = psbt.extractTransaction().toHex();
  console.log('signedTransaction==', signedTransaction);
}
```

# 3. 扫链接口

## 3.1.原生 Bitcoin 接口

**获取活跃的最新区块**

请求参数

```bash
curl --data-binary '{"jsonrpc": "1.0", "id": "curltest", "method": "getchaintips", "params": []}' -H 'content-type: text/plain;' https://thrilling-spring-bush.btc.quiknode.pro/c0d9254cfb049224abd0ece400635e62b791a388/
```

返回值

```json
{
   "result":[
      {
         "height":845198,
         "hash":"00000000000000000000301d584ec5f1c16e89487c05baf035f01875cb763d75",
         "branchlen":0,
         "status":"active"
      },
      {
         "height":841424,
         "hash":"000000000000000000010998fc2714f8ae10ffb73f1986eecc58f5afc457ee07",
         "branchlen":1,
         "status":"valid-headers"
      },
      {
         "height":838792,
         "hash":"00000000000000000002af7214c8796e102b0e9074a5d469266d7afe5af2f087",
         "branchlen":1,
         "status":"headers-only"
      },
      {
         "height":816358,
         "hash":"00000000000000000001d5f92e2dbbfcbc1e859873117e7983dd574857da5e14",
         "branchlen":1,
         "status":"valid-headers"
      },
      {
         "height":815202,
         "hash":"0000000000000000000093917031004a140b6db5c6adec217f814db98d7f0bde",
         "branchlen":1,
         "status":"valid-fork"
      },
   ],
   "error":null,
   "id":"curltest"
}
```

- “invalid” 该分支至少包含一个无效块
- “headers-only” 并非该分支的所有块都可用，但 headers 有效
- “valid-headers”所有块都可用于此分支，但它们从未经过完全验证
- “valid-fork” 该分支不是活动链的一部分，但经过充分验证
- “active”这是活跃主链的提示，这当然有效

**获取区块信息**

请求示范

```bash
curl --data-binary '{"jsonrpc": "1.0", "id": "curltest", "method": "getblockchaininfo", "params": []}' -H 'content-type: text/plain;' https://thrilling-spring-bush.btc.quiknode.pro/c0d9254cfb049224abd0ece400635e62b791a388/
```

返回值

```json
{
   "result":{
      "chain":"main",
      "blocks":845200,
      "headers":845200,
      "bestblockhash":"000000000000000000027a970865a12b12e4da473011e2033eeca871c957a747",
      "difficulty":84381461788831.34,
      "time":1716706327,
      "mediantime":1716703878,
      "verificationprogress":0.999998974207445,
      "initialblockdownload":false,
      "chainwork":"00000000000000000000000000000000000000007b695dedb46255cb840f5cb6",
      "size_on_disk":652535688171,
      "pruned":false,
      "warnings":""
   },
   "error":null,
   "id":"curltest"
}
```

**列出未花费的输入输出**

请求示范

```bash
curl --data-binary '{"jsonrpc": "1.0", "id": "curltest", "method": "listunspent", "params": [845000, 845200, [] , true, { "minimumAmount": 0.005 } ]}' -H 'content-type: text/plain;'  https://thrilling-spring-bush.btc.quiknode.pro/c0d9254cfb049224abd0ece400635e62b791a388/
```

返回值

```json
[                                
  {                              
    "txid" : "",             
    "vout" : 1,                 
    "address" : "str",           
    "label" : "str",             
    "scriptPubKey" : "str",     
    "amount" : 10000,               
    "confirmations" : 12,        
    "redeemScript" : "hex",     
    "witnessScript" : "str",    
    "spendable" : false,    
    "solvable" : false,     
    "reused" : false,       
    "desc" : "str",              
    "safe" : true                                      
  },{                              
    "txid" : "",             
    "vout" : 1,                 
    "address" : "str",           
    "label" : "str",             
    "scriptPubKey" : "str",     
    "amount" : 10000,               
    "confirmations" : 12,        
    "redeemScript" : "hex",     
    "witnessScript" : "str",    
    "spendable" : false,    
    "solvable" : false,     
    "reused" : false,       
    "desc" : "str",              
    "safe" : true                                      
  },
]
```

**发送交易到区块链网络**

请求参数

```bash
curl --data-binary '{"jsonrpc": "1.0", "id": "curltest", "method": "sendrawtransaction", "params": ["signedhex"]}' -H 'content-type: text/plain;' https://thrilling-spring-bush.btc.quiknode.pro/c0d9254cfb049224abd0ece400635e62b791a388/
```

返回值

```
成功返回交易 Hash
```

## 3.2.Rosetta Api

Bitcoin Rosetta API 是由 Coinbase 提出的 Rosetta 标准的一部分，旨在为区块链和钱包提供一个统一的接口标准。这个标准化的接口使得与各种区块链的交互更加容易和一致，无论是对交易数据的读取还是写入。目前已经支持很多链，包含比特币，以太坊等主流链，也包含像 IoTex 和 Oasis 这样的非主流链。

**3.2.1.Rosetta API 概述**

Rosetta API 分为两部分：

- Data API：用于读取区块链数据。
- Construction API：用于构建和提交交易。

**3.2.2. Data API**

Data API 提供了一组端点，用于检索区块链数据，如区块、交易、余额等。主要端点包括：

- /network/list：返回支持的网络列表。
- /network/status：返回当前网络的状态信息。
- /network/options：返回支持的网络选项和版本信息。
- /block：返回指定区块的数据。
- /block/transaction：返回指定交易的数据。
- /account/balance：返回指定账户的余额。
- /mempool：返回当前未确认的交易池。
- /mempool/transaction：返回指定未确认交易的数据。

**3.2.3. Construction API**

Construction API 提供了一组端点，用于创建、签名和提交交易。主要端点包括：

- /construction/preprocess：分析交易需求并返回交易所需的元数据。
- /construction/metadata：返回构建交易所需的元数据。
- /construction/payloads：生成待签名的交易有效载荷。
- /construction/parse：解析交易并返回其操作。
- /construction/combine：将签名与待签名交易合并。
- /construction/hash：返回交易的唯一标识符（哈希）。
- /construction/submit：提交签名后的交易。

**3.2.4.开发 BTC 钱包使用到的 Rosetta Api**

为了具体实现 Rosetta API，开发者需要遵循 Rosetta 标准并根据比特币区块链的特性进行适配。以下是一些具体实现细节

数据结构：

- 区块：包含区块哈希、前一个区块哈希、区块高度、时间戳、交易列表等。
- 交易：包含交易哈希、输入输出列表、金额、地址等。
- 账户：包含账户地址和余额信息。

**用到的接口**

- /network/list：返回比特币主网和测试网信息。
- /network/status：返回当前最新区块、已同步区块高度、区块链处理器的状态等。
- /block 和 /block/transaction：返回区块和交易的详细信息，包括交易的输入输出、金额、地址等。
- /account/balance：通过查询比特币节点，返回指定地址的余额。

**发送交易到区块链网络**

- /construction/submit：通过比特币节点提交签名后的交易。

## 3.3. 文档资料

- 比特币开发文档：https://developer.bitcoin.org/reference/rpc/

- Rosetta 开发文档：https://docs.cdp.coinbase.com/rosetta/reference/networklist/

- Rosetta 开发文档：https://github.com/coinbase/mesh-ecosystem/blob/master/implementations.md

- 浏览器：https://btc.com/zh-CN


# 4.中心化钱包开发

## 4.1.离线地址生成

- 调度签名机生成密钥对，签名机吐出公钥
- 使用公钥匙导出地址

## 4.2.充值逻辑

- 获得最新块高；更新到数据库
- 从数据库中获取上次解析交易的块高做为起始块高，最新块高为截止块高，如果数据库中没有记录，说明需要从头开始扫，起始块高为 0；
- 解析区块里面的交易，to 地址是系统内部的用户地址，说明用户充值，更新交易到数据库中，将交易的状态设置为待确认。
- 所在块的交易过了确认位，将交易状态更新位充值成功并通知业务层。
- 解析到的充值交易需要在钱包的数据库里面维护 UTXO

## **4.2.**提现逻辑

- 获取离线签名需要的参数，给合适的手续费
- 构建未签名的交易消息摘要，将消息摘要递给签名机签名
- 构建完整的交易并进行序列化
- 发送交易到区块链网络
- 扫链获取到交易之后更新交易状态并上报业务层

## **4.3.**归集逻辑

- 将用户地址上的资金转到归集地址，签名流程类似提现
- 发送交易到区块链网络
- 扫链获取到交易之后更新交易状态

## **4.4.**转冷逻辑

- 将热钱包地址上的资金转到冷钱包地址，签名流程类似提现
- 发送交易到区块链网络
- 扫链获取到交易之后更新交易状态

## **4.5.**冷转热逻辑

- 手动操作转账到热钱包地址
- 扫链获取到交易之后更新交易状态

**注意：交费的学员需要完整的项目实战代码可寻求 The Web3 社区索取**

# **5.HD 钱包开发**

## 5.1. 离线地生成和离线签名

参考上面的代码

## 5.2. 和链上交互的接口

- 获取账户余额

- 根据地址获取交易记录

- 根据交易 Hash 获取交易详情

- 获取未花费的输入输出

- 获取交易手续费

- 以上接口请参考代码库：https://github.com/the-web3/wallet-chain-node/tree/develop/wallet/bitcoin


# **6.总结**

HD 钱包和交易所钱包不同之处有以下几点

## **6.1.密钥管理方式不同**

- HD 钱包私钥在本地设备，私钥用户自己控制
- 交易所钱包中心化服务器(CloadHSM, TEE 等)，私钥项目方控制

## 6.2.资金存在方式不同

- HD 资金在用户钱包地址
- 交易所钱包资金在交易所热钱包或者冷钱包里面

## 6.3.业务逻辑不一致

- 中心化钱包：实时不断扫链更新交易数据和状态
- HD 钱包：根据用户的操作通过请求接口实现业务逻辑
