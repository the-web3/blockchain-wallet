# Solana 钱包开发详细教程

Solana 是一个高性能的区块链平台，旨在实现快速、安全且可扩展的去中心化应用（dApps）和加密货币交易。它的设计初衷是解决传统区块链网络在扩展性和速度方面的局限，特别是比特币和以太坊在交易吞吐量和确认时间上的瓶颈。

# 一. Solana 核心特点

- **高吞吐量**：Solana 通过独特的共识机制和优化的网络协议，能够处理高达 65,000 笔交易每秒（TPS），远高于比特币的 7 TPS 和以太坊的 15 TPS。
- **低延迟**：Solana 网络的交易确认时间通常在 400 毫秒左右，确保了几乎即时的交易确认
- **低交易费用：**由于其高效的网络设计，Solana 的交易费用非常低，通常只有几美分。这使得它在处理大量小额交易时非常经济。



# 二. 技术亮点

- **Proof of History (PoH):** Solana 的 PoH 是一种时间戳机制，创建了一个历史记录，证明了事件发生的顺序。这减少了节点之间的通信需求，极大地提高了网络的效率。
- **Tower BFT:** 基于 PoH，Solana 实现了一种改进的拜占庭容错机制，称为 Tower BFT。这种机制确保了网络的安全性和一致性。
- **Turbine:** 这是一种区块传播协议，通过将数据拆分成小包并在节点之间传输，优化了数据传播的速度和效率。
- **Gulf Stream：**Solana 采用的这项技术可以提前确认和转发交易，从而减少内存池中的未确认交易数量，提高网络吞吐量。
- **Sealevel:** Solana 的智能合约运行环境，允许并行处理数千个智能合约调用，从而实现高效的计算性能。
- **Pipelining:** 通过流水线处理，Solana 能够优化区块验证和传播的速度，使得整个网络保持高效运行。
- **Cloudbreak:** 这是一种水平扩展的账户数据库，支持并行读取和写入操作，从而优化了存储访问的性能。
- **Archivers:** 用于存储区块链数据的去中心化节点，确保数据的可靠存储和访问。

## 1. 共识算法流程

- **PoH 生成时间序列**：Solana 网络通过 PoH 生成一个全局时间序列，所有节点都遵循这个时间序列。PoH 是通过连续的 SHA-256 哈希运算产生的，每个哈希值都是前一个哈希值的输入，从而形成一个不可篡改的时间链。
- **节点状态同步**：所有参与共识的节点都使用 PoH 时间序列来同步它们的状态。这意味着节点可以在不频繁通信的情况下验证和确认交易顺序。
- **验证者投票**：网络中的验证者（validators）对每个区块的有效性进行投票。每个验证者根据其在时间序列中的位置，对当前区块进行投票。如果区块被足够多的验证者投票通过，它将被确认并添加到区块链中。
- **投票计数和锁定机制**：Tower BFT 使用一种称为“锁定投票”的机制。验证者在投票时会锁定其投票，如果某个区块在指定的时间内没有获得足够多的投票，验证者将锁定其投票，直到达成共识。锁定机制防止网络分叉，并确保共识的稳定性。
- **区块确认和传播**：一旦区块获得足够多的验证者投票通过，它将被确认并添加到区块链中。确认后的区块通过网络传播给其他节点，确保全网状态一致。
- **冲突处理**：如果出现分叉或投票冲突，Tower BFT 依赖于 PoH 时间序列来决定最优链。节点会选择包含更多已确认区块且时间戳最新的链作为主链，并放弃较短或较旧的分叉链。

## **2.算法优势**

- **高效性**：由于 PoH 提供了一个全局时间序列，Tower BFT 大幅减少了节点之间的通信需求，提高了共识达成的效率。
- **安全性**：Tower BFT 的锁定投票机制和基于 PoH 的冲突处理策略确保了网络的安全性，能够抵抗拜占庭节点的攻击。
- **低延迟**：Tower BFT 结合 PoH 使得区块确认时间显著降低，通常在几百毫秒内完成交易确认。



# 三. **生态系统**

- **Solana 的原生代币 SOL**：SOL 是 Solana 网络的原生加密货币，用于支付交易费用和参与网络共识（通过质押）。
- **dApps 和项目**：Solana 上已经构建了多个去中心化应用和项目，包括去中心化金融（DeFi）平台、NFT 市场、游戏等。例如，知名的 Serum 去中心化交易所就是基于 Solana 构建的。
- **社区和开发者支持**：Solana 具有活跃的开发者社区，并提供了丰富的开发工具和资源，如 Solana SDK 和开发者文档，以支持开发者在其平台上构建应用。



# 四. **Solana 的优势与挑战**

## **1.优势**

- 高性能和可扩展性使其适合处理大量交易和复杂应用。
- 低交易费用吸引了大量用户和开发者。
- 强大的技术基础和不断扩展的生态系统增加了其市场竞争力。

## **2. 挑战**

- 与其他区块链平台（如以太坊、Cardano 等）的竞争依然激烈。
- 面临着去中心化程度和安全性的平衡问题，需要持续改进和创新。



# 五. 离线地址

```javascript
export function createSolAddress (seedHex: string, addressIndex: string) {
  const { key } = derivePath("m/44'/501'/1'/" + addressIndex + "'", seedHex);
  const publicKey = getPublicKey(new Uint8Array(key), false).toString('hex');
  const buffer = Buffer.from(getPublicKey(new Uint8Array(key), false).toString('hex'), 'hex');
  const address = bs58.encode(buffer);
  const hdWallet = {
    privateKey: key.toString('hex') + publicKey,
    publicKey,
    address
  };
  return JSON.stringify(hdWallet);
}
```

Solana 的 BIP 44 编号是 501；并且将 BIP44 协议中的是否找零项目去掉了。



# 六. 离线签名

```javascript
export async function signSolTransaction (params) {
  const { from, amount, to, mintAddress, nonce, decimal, privateKey } = params;
  const fromAccount = Keypair.fromSecretKey(new Uint8Array(Buffer.from(privateKey, 'hex')), { skipValidation: true });
  const calcAmount = new BigNumber(amount).times(new BigNumber(10).pow(decimal)).toString();
  if (calcAmount.indexOf('.') !== -1) throw new Error('decimal 无效');
  const tx = new Transaction();
  const toPubkey = new PublicKey(to);
  const fromPubkey = new PublicKey(from);
  tx.recentBlockhash = nonce;
  if (mintAddress) {
    const mint = new PublicKey(mintAddress);
    const fromTokenAccount = await SPLToken.Token.getAssociatedTokenAddress(
      SPLToken.ASSOCIATED_TOKEN_PROGRAM_ID,
      SPLToken.TOKEN_PROGRAM_ID,
      mint,
      fromPubkey
    );
    const toTokenAccount = await SPLToken.Token.getAssociatedTokenAddress(
      SPLToken.ASSOCIATED_TOKEN_PROGRAM_ID,
      SPLToken.TOKEN_PROGRAM_ID,
      mint,
      toPubkey
    );
    tx.add(
      SPLToken.Token.createTransferInstruction(
        SPLToken.TOKEN_PROGRAM_ID,
        fromTokenAccount,
        toTokenAccount,
        fromPubkey,
        [fromAccount],
        calcAmount
      )
    );
  } else {
    tx.add(
      SystemProgram.transfer({
        fromPubkey: fromAccount.publicKey,
        toPubkey: new PublicKey(to),
        lamports: calcAmount
      })
    );
  }
  tx.sign(fromAccount);
  return tx.serialize().toString('base64');
}
```

本代码中有一点值得注意的是，solana 交易签名中的 nonce 是使用 recentBlockHash 做为签名的 nonce, 而且这个 recentBlockHash 签名的交易在一定时间内交易发出去有效，过了这个时间之后再发送交易会报 Block Not Found 的错误。

要解决交易 recentBlockHash 失效问题，需要授权一个新的地址做为获取签名的 Nonce 的地址，这样签名的消息才能很长时间内都有效。

代码如下：

```javascript
export function prepareAccount(params){
  const {
    authorAddress, from, recentBlockhash, minBalanceForRentExemption, privs,
  } = params;

  const authorPrivateKey = (privs?.find(ele=>ele.address===authorAddress))?.key;
  if(!authorPrivateKey) throw new Error("authorPrivateKey 为空");
  const nonceAcctPrivateKey = (privs?.find(ele=>ele.address===from))?.key;
  if(!nonceAcctPrivateKey) throw new Error("nonceAcctPrivateKey 为空");

  const author = Keypair.fromSecretKey(new Uint8Array(Buffer.from(authorPrivateKey, "hex")));
  const nonceAccount = Keypair.fromSecretKey(new Uint8Array(Buffer.from(nonceAcctPrivateKey, "hex")));


  let tx = new Transaction();
  tx.add(
      SystemProgram.createAccount({
        fromPubkey: author.publicKey,
        newAccountPubkey: nonceAccount.publicKey,
        lamports: minBalanceForRentExemption,
        space: NONCE_ACCOUNT_LENGTH,
        programId: SystemProgram.programId,
      }),
  
      SystemProgram.nonceInitialize({
        noncePubkey: nonceAccount.publicKey,  
        authorizedPubkey: author.publicKey, 
      })
  );
  tx.recentBlockhash = recentBlockhash;


  tx.sign(author, nonceAccount);
  return tx.serialize().toString("base64");
}
```



# 七. 钱包开发相关的 RPC 接口

## 1.获取账户信息

- 接口作用：本接口的作用是判断账户是否可用，如果 value 为 null, 说明不可用，否则可用
- 接口名称：getAccountInfo
- 接口参数：地址
- 请求示范

```bash
curl --location 'https://sly-yolo-dinghy.solana-mainnet.quiknode.pro/2ac2af5b8c2e5e9e74c7906e949f1976314aa996' \
--header 'Content-Type: application/json' \
--data '{
    "jsonrpc": "2.0",
    "id": 1,
    "method": "getAccountInfo",
    "params": [
      "4wHd9tf4x4FkQ3JtgsMKyiEofEHSaZH5rYzfFKLvtESD",
      {
        "encoding": "base58"
      }
    ]
  }'
```

- 返回值

```json
{
    "jsonrpc": "2.0",
    "result": {
        "context": {
            "apiVersion": "1.17.34",
            "slot": 268835746
        },
        "value": {
            "data": [
                "",
                "base58"
            ],
            "executable": false,
            "lamports": 289995950,
            "owner": "11111111111111111111111111111111",
            "rentEpoch": 18446744073709551615,
            "space": 0
        }
    },
    "id": 1
}
```

- value 不为 null, 说明该地址能用

## 2.获取 recentBlochHash,  直接签名的话 recentBlochHash 相当于 nonce

- 接口作用：获取最近的区块的 blockHash, 做为交易签名的 Nonce
- 接口名称：getRecentBlockhash
- 接口参数：无
- 请求示范

```bash
curl --location 'https://sly-yolo-dinghy.solana-mainnet.quiknode.pro/2ac2af5b8c2e5e9e74c7906e949f1976314aa996' \
--header 'Content-Type: application/json' \
--data '{
    "jsonrpc":"2.0",
    "id":1, 
    "method":"getRecentBlockhash"
}'
```

- 返回值

```json
{
    "jsonrpc": "2.0",
    "result": {
        "context": {
            "apiVersion": "1.17.34",
            "slot": 268809612
        },
        "value": {
            "blockhash": "5k5Hh3gfRX4hJv9dqxfdMa6wSFkkZ631sf71kPqtCFv7",
            "feeCalculator": {
                "lamportsPerSignature": 5000
            }
        }
    },
    "id": 1
}
```

- blockhash 为要使用的值

## 3. 获取准备 nonce 账户的 Minimum Balance For Rent 数据

- 接口作用：获取 rent 账户的最小 Rent
- 接口名称：getMinimumBalanceForRentExemption
- 接口参数：账户的数据长度
- 请求示范

```bash
curl --location 'https://sly-yolo-dinghy.solana-mainnet.quiknode.pro/2ac2af5b8c2e5e9e74c7906e949f1976314aa996' \
--header 'Content-Type: application/json' \
--data ' {
    "jsonrpc": "2.0", "id": 1,
    "method": "getMinimumBalanceForRentExemption",
    "params": [50]
  }'
```

- 返回值

```json
{
    "jsonrpc": "2.0",
    "result": 1238880,
    "id": 1
}
```

- result 的结果为 *prepareAccount*  的 minBalanceForRentExemption 值

## 4. 获取最新块高 （Slot）

- 接口作用：获取最新的 slot
- 接口名称：getSlot
- 接口参数：无
- 请求示范

```bash
curl --location 'https://sly-yolo-dinghy.solana-mainnet.quiknode.pro/2ac2af5b8c2e5e9e74c7906e949f1976314aa996' \
--header 'Content-Type: application/json' \
--data '{"jsonrpc":"2.0","id":1, "method":"getSlot"}'
```

- 返回值

```json
{
    "jsonrpc": "2.0",
    "result": 268840179,
    "id": 1
}
```

- 返回值为最新的块高（Slot）

## 5. 根据块高获取交易

- 接口作用：根据区块号获取里面的交易
- 接口名称：getConfirmedBlock
- 接口参数：区块高度和编码方式
- 请求示范

```bash
curl --location 'https://api.devnet.solana.com' \
--header 'Content-Type: application/json' \
--data '{
    "jsonrpc": "2.0", "id": 1,
    "method": "getConfirmedBlock",
    "params": [
        268992938, 
         {
          "encoding":"base64",
           "maxSupportedTransactionVersion": 0
      }
    ]
  }'
```

- 返回值

```json
{
  "jsonrpc": "2.0",
  "result": {
    "blockTime": null,
    "blockhash": "3Eq21vXNB5s86c62bVuUfTeaMif1N2kUqRPBmGRJhyTA",
    "parentSlot": 429,
    "previousBlockhash": "mfcyqEXB3DnHXki6KjjmZck6YjmZLvpAByy2fj4nh6B",
    "rewards": [],
    "transactions": [
      {
        "meta": {
          "err": null,
          "fee": 5000,
          "innerInstructions": [],
          "logMessages": [],
          "postBalances": [499998932500, 26858640, 1, 1, 1],
          "postTokenBalances": [],
          "preBalances": [499998937500, 26858640, 1, 1, 1],
          "preTokenBalances": [],
          "status": {
            "Ok": null
          }
        },
        "transaction": [
          "AVj7dxHlQ9IrvdYVIjuiRFs1jLaDMHixgrv+qtHBwz51L4/ImLZhszwiyEJDIp7xeBSpm/TX5B7mYzxa+fPOMw0BAAMFJMJVqLw+hJYheizSoYlLm53KzgT82cDVmazarqQKG2GQsLgiqktA+a+FDR4/7xnDX7rsusMwryYVUdixfz1B1Qan1RcZLwqvxvJl4/t3zHragsUp0L47E24tAFUgAAAABqfVFxjHdMkoVmOYaR1etoteuKObS21cc1VbIQAAAAAHYUgdNXR0u3xNdiTr072z2DVec9EQQ/wNo1OAAAAAAAtxOUhPBp2WSjUNJEgfvy70BbxI00fZyEPvFHNfxrtEAQQEAQIDADUCAAAAAQAAAAAAAACtAQAAAAAAAAdUE18R96XTJCe+YfRfUp6WP+YKCy/72ucOL8AoBFSpAA==",
          "base64"
        ]
      }
    ]
  },
  "id": 1
}
```

或者内部包含

```json
{
   "parsed":{
      "info":{
         "amount":"12500",
         "authority":"CyZuD7RPDcrqCGbNvLCyqk6Py9cEZTKmNKujfPi3ynDd",
         "destination":"Ezts8ufHLpKmJsvXzPmguvvdtC18G8aQngQ9EyUAJrHf",
         "source":"FLffM77tZpRP7eRqQGfH7UA1j1j31csZ4f9CwQ4qbVjp"
      },
      "type":"transfer"
   },
   "program":"spl-token",
   "programId":"TokenkegQfeZyiNwAJbNbGKPFXCWuBvf9Ss623VQ5DA",
   "stackHeight":2
}
```

- source 是转出地址
- destination 转入地址
- amount 是转账金额
- type：转账类型

## 6. 根据交易 Hash 获取交易详情

- 接口作用：根据交易 Hash 获取交易详情
- 接口名称：getConfirmedTransaction
- 接口参数：区块高度和编码方式
- 请求示范

```bash
curl --location 'https://sly-yolo-dinghy.solana-mainnet.quiknode.pro/2ac2af5b8c2e5e9e74c7906e949f1976314aa996' \
--header 'Content-Type: application/json' \
--data ' {
    "jsonrpc": "2.0",
    "id": 1,
    "method": "getConfirmedTransaction",
    "params": [
      "5X6y7aVzC93nfHbPbcy1yU8jHeFj996ZrVzMfNFYqC59jJrVMPMj3zzCtfwnoJB8H7PcgZRhyMbz1znVt8CSoT35",
      {
          "encoding":"jsonParsed",
           "maxSupportedTransactionVersion": 0
      }
    ]
  }'
```

- 返回值

```json
{
   "jsonrpc":"2.0",
   "result":{
      "blockTime":1717073604,
      "transaction":{
         "message":{
            "accountKeys":[
               
            ],
            "instructions":[
               {
                  "parsed":{
                     "info":{
                        "amount":"5000000000",
                        "authority":"7JnucyofTX4Zk74jJ18HRhfKoBc8GgPM6ofQZ1EQLSpZ",
                        "destination":"EgScKCKKWX1d7yEp7SBpErdvYsBXzYYgxtMWiSdbZL9S",
                        "source":"FSkKxc5WBa7g6obBmXMavNmpuNPs25nukJz32DyHXuTM"
                     },
                     "type":"transfer"
                  },
                  "program":"spl-token",
                  "programId":"TokenkegQfeZyiNwAJbNbGKPFXCWuBvf9Ss623VQ5DA",
                  "stackHeight":null
               },
               {
                  "accounts":[
                     
                  ],
                  "data":"3MuSQQNShkST",
                  "programId":"ComputeBudget111111111111111111111111111111",
                  "stackHeight":null
               },
               {
                  "accounts":[
                     
                  ],
                  "data":"FtDxo9",
                  "programId":"ComputeBudget111111111111111111111111111111",
                  "stackHeight":null
               }
            ],
            "recentBlockhash":"HMzYzdmwMez5ueZA9yrWHT6V5ZT4VtD3CC4hsaN7a9m4"
         },
         "signatures":[
            "5X6y7aVzC93nfHbPbcy1yU8jHeFj996ZrVzMfNFYqC59jJrVMPMj3zzCtfwnoJB8H7PcgZRhyMbz1znVt8CSoT35"
         ]
      }
   },
   "id":1
}
```

- source 是转出地址
- destination 转入地址
- amount 是转账金额
- type：转账类型

## 7.发送交易到区块链网络

- 接口作用：发送交易到区块链网络
- 接口名称：sendTransaction
- 接口参数：区块高度和编码方式
- 请求示范

```bash
curl --location 'https://sly-yolo-dinghy.solana-mainnet.quiknode.pro/2ac2af5b8c2e5e9e74c7906e949f1976314aa996' \
--header 'Content-Type: application/json' \
--data '{
    "jsonrpc": "2.0",
    "id": 1,
    "method": "sendTransaction",
    "params": [
      "ASG4jVQEsZhgMGnkXIYEvyhvGMYBfwL8lFd40mPz4AjpeWqZlVM122Vx8iCZIUNmbGdqDcdplm0xMUGri4WiCAIBAAEDOns4dLpGe+a4HqNh49dFOvi4HIiu3SS1Ax/doLxxrTJa8yfsHKLzR/zaYPv8xS5VGKfAp4PkO8pQN4Jn396IRgAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAKy6/vKD5x9Dmbz1bBJ85fYZK94CfRhjYwMBlQyb4mz8BAgIAAQwCAAAAQEIPAAAAAAA=",
      {
        "encoding":"base64"
      }
     
    ]
  }'
```

- 返回值

```json
{
    "jsonrpc": "2.0",
    "result": "g6yMJUd16jAcPiQfi7QK1M5tN7yheeKAC7NbUh3BQCSfav3pgv74ovCSGuhgApzma1s6ew8WEmX1Bzfk6sCfg9P",
    "id": 1
}
```

- 成功返回交易 Hash,  失败返回各种错误



# 八. 中心化钱包开发

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

**注意：交费的学员需要完整的项目实战代码可寻求 The Web3 社区索取**



# 九. HD 钱包开发

## **1.离线地生成和离线签名**

参考上面的代码

## **2.和链上交互的接口**

- 获取账户余额
- 根据地址获取交易记录
- 获取预估手续费

对应代码库：https://github.com/the-web3/wallet-chain-node



# 十. **总结**

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



# 十一. 附录

- RPC 文档：https://solana.com/docs/rpc
- github: https://github.com/solana-labs
- 浏览器 1：https://explorer.solana.com/
- 浏览器 2: https://solscan.io/
- 钱包相关的资料：https://solana.com/developers/cookbook
- solana web3js: https://github.com/solana-labs/solana-web3.js