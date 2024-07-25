# Solana 中心化钱包项目实战

# 一. Solana 中心化钱包概述

我们都知道，中心化钱包主要用在交易所使用，中心化钱包功能主要包含：批量地址生成，充值，提现，归集，热转冷，冷转热等功能。

## **1. 离线地址生成**

- 调度签名机生成密钥对，签名机吐出公钥, 签名算法是：Ed25519
- 使用公钥导出地址

## **2.充值逻辑**

- 获取链上最新块高，从数据库中获取上次解析交易的块高做为起始块高，如果链上的最新块和数据内部的块高一致，则等到下一个块出现了之后再开始去扫块；如果数据库中没有记录，则按照配置的块高开始扫块，起始块高配置默认为 0，如果用户没有配置，那就从 0 开始扫块。

- 获取块里面的交易，判断是 native token 或者是代币充值

  💡native token：主链币充值 program 等于 system 并且 type 为 transfer 

  💡Token：代币充值 program 等于 spl-token 并且 type 为 transfer 或者 transferChecked。 

  💡Nft:  source 和  destination 里面的 mint 为合约地址

  

```js
for(let i=0; i<instructions.length; i++){
const instruction = instructions[i];
const obj = {};
postTokenBalances?.forEach(item=>{
  // tokenAddress 对应 owner
  obj[accountKeys[item.accountIndex].pubkey] = {owner: item.owner, mint: item.mint};
});
if(instruction.parsed && instruction.program){
  const {parsed:{type, info}, program} = instruction;
  if(program==="system" && type==="transfer"){
    fromAddresses.push(info.source);
    toAddresses.push(info.destination);
    amounts.push(info.lamports);
  }else if(program==="spl-token" && (type==="transfer" || type==="transferChecked") && obj[info.source].mint === contractAddr && obj[info.destination].mint === contractAddr){
    fromAddresses.push(obj[info.source].owner || info.authority || info.multisigAuthority);
    let toAddr = obj[info.destination].owner;
    if(!toAddr){
      const toAddrObj = instructions.find(ele => {
        return ele.program === "spl-associated-token-account"
          && ele.parsed.type === "create"
          && ele.parsed.info.account === info.destination
      });
      toAddr = toAddrObj.parsed.info.wallet;
    }
    toAddresses.push(toAddr);
    amounts.push(info.amount || info.tokenAmount.amount);
  }
}
```

本次实战课程中只实现主币充值和代币充值，不实现 NFT 充值

- 交易里面 to 地址是系统内部的用户地址，from 地址是外部地址，说明用户充值，更新交易到数据库中，并更新改地址的余额(注意，如果用户充值金额小于最小充值金额，不给入账)，将交易的状态设置为待确认。
- 交易所在块的过了确认位，将交易状态更新位充值成功并通知业务层，solana 的确认位 60 个。

## **3.归集**

功能：每隔一段时间将用户地址里面资金归集到归集地址

流程：

- 使用用户的地址发起一笔交易，构建完交易之后生成待签名 Msg, 将签名 Msg 递交给签名机进行签名，签名机签名完成之后返回 signature,  将 signature 与交易一起构建成完整的交易，然后将交易发送到区块链网络，并且将这笔交易记录到 transaction 表，将用户地址里面资金锁定。

- 扫链扫到这笔之后，通过 TxHash 查到这笔交易，然后匹配 from 和 to,  归集的交易 from 是系统的用户地址，to 地址交易所的归集地址，根据扫链交易状体更新数据库表的状态。 

  💡成功：更新 transaction 表的交易状态为成功并且将用户地址锁定资金清 0 

  💡失败：更新 transaction 表的交易状态为失败并且将用户地址锁定资金退回到用户地址里等待下一次归集

## **4.提现**

功能：将热钱包地址里面资金提现到用户的外部地址

流程：

- 用户在业务层发起提现，业务层把该笔提交到钱包层的提现表，钱包层使用热钱包地址签名一笔交易并发送到区块链网络，并且更新提现表里面的交易位已发送，将交易也同时放到 transaction 表， 将热钱包地址里面提现的资金锁定。

- 扫链扫到这笔之后，通过 TxHash 查到这笔交易，然后匹配 from 和 to,  提现的 from 是系统内部热钱包地址，to系统外部地址，根据扫链交易状体更新数据库表的状态。 

  💡成功：更新 transaction 和 withdraw 表的交易状态为成功并且将热钱包地址锁定资金减掉 

  💡失败：更新 transaction 表的交易状态为失败并且将热钱包地址锁定资金退回到热钱地址里重新发起提现过程

# 二. 签名机服务

## 1.签名机器功能概述

Solana 钱包实战中我们用 NodeJs 写了一个签名机服务,  主要实现密钥对和地址的生成，nonceAccount 的签名和交易签名。如果整个项目在生成环境中实施，需要把这个服务部署到 TEE 环境； 整个项目的功能如下：

- 地址生成：接口请求地址，签名机会根据传入的参数需要生成多少个地址生成对应的地址，目前接口中会返回私钥，正常的生产环境中实施的话，需要把私钥留在 TEE 环境里面，并且进行加密存储。
- Nonce 账户创建：做一笔 Nonce 账户创建的交易，目前接口需要传入私钥进行进行签名，实际生产中需要改造，私钥留在 TEE 环境里面，从 TEE 环境里面取加密的私钥解密之后对交易进行签名。
- 交易签名：离线交易签名，目前接口需要传入私钥进行进行签名，实际生产中需要改造，私钥留在 TEE 环境里面，从 TEE 环境里面取加密的私钥解密之后对交易进行签名。

## 2.代码介绍

- Solana 地址生成和签名代码封装： https://github.com/the-web3/solana-sign-service/blob/main/src/solana/solana.service.ts
- 地址生成接口代码: https://github.com/the-web3/solana-sign-service/blob/42b6538ea663d5a0c8d0f732b6d2cd3b423f88e1/src/solana/solana.controller.ts#L8
- Nonce 账户创建代码：https://github.com/the-web3/solana-sign-service/blob/42b6538ea663d5a0c8d0f732b6d2cd3b423f88e1/src/solana/solana.controller.ts#L48
- 交易签名的代码：https://github.com/the-web3/solana-sign-service/blob/42b6538ea663d5a0c8d0f732b6d2cd3b423f88e1/src/solana/solana.controller.ts#L21

目前代码里面集成数据库，但是没有完全搞好，也欢迎小伙伴来提 PR

# 三. Solana 交易所钱包代码实战

- 项目代码：https://github.com/the-web3/sol-wallet

## 1.代码简介

- 表结构定义：https://github.com/the-web3/sol-wallet/blob/main/migrations/00001_create_schema.sql
- 充值逻辑：https://github.com/the-web3/sol-wallet/blob/main/wallet/deposit.go
- 提现逻辑： https://github.com/the-web3/sol-wallet/blob/main/wallet/withdraw.go
- 归集转冷逻辑：https://github.com/the-web3/sol-wallet/blob/main/wallet/collection_cold.go

## 2.必要依赖和代码构建

**2.1.必要依赖**

- 数据库：postgres
- Golang: [Go 1.21+](https://golang.org/dl/)

**2.2.构建代码**

```text
make sol-wallet
```

**2.3.构建效果展示**

```text
guoshijiang@192 sol-wallet % ./sol-wallet
NAME:
   eth-wallet - A new cli application

USAGE:
   sol-wallet [global options] command [command options] [arguments...]

VERSION:
   1.14.6-stable-8b3f4433

DESCRIPTION:
   An exchange wallet scanner services with rpc and rest api server

COMMANDS:
   api
   rpc
   generate-address
   wallet
   migrate
   version
   help, h           Shows a list of commands or help for one command

GLOBAL OPTIONS:
   --help, -h     show help
   --version, -v  print the version
```

- api:  启动 http 服务
- rpc: 启动 rpc 服务
- generate-address: 批量生成地址命令
- migrate: 数据库 migrate
- wallet: 启动充值，提现，归集与转冷等进程

## **3.运行代码**

3.1.**配置环境**

```text
export SOL_WALLET_MIGRATIONS_DIR="./migrations"

export SOL_WALLET_CHAIN_ID=1
export SOL_WALLET_RPC_RUL="https://docs-demo.solana-mainnet.quiknode.pro"
export SOL_WALLET_STARTING_HEIGHT=1950545
export SOL_WALLET_CONFIRMATIONS=64
export SOL_WALLET_DEPOSIT_INTERVAL=5s
export SOL_WALLET_WITHDRAW_INTERVAL=5s
export SOL_WALLET_COLLECT_INTERVAL=5s
export SOL_WALLET_BLOCKS_STEP=1

export SOL_WALLET_HTTP_PORT=8989
export SOL_WALLET_HTTP_HOST="127.0.0.1"
export SOL_WALLET_RPC_PORT=8980
export SOL_WALLET_RPC_HOST="127.0.0.1"
export SOL_WALLET_METRICS_PORT=8990
export SOL_WALLET_METRICS_HOST="127.0.0.1"

export SOL_WALLET_SLAVE_DB_ENABLE=false

export SOL_WALLET_MASTER_DB_HOST="127.0.0.1"
export SOL_WALLET_MASTER_DB_PORT=5432
export SOL_WALLET_MASTER_DB_USER="guoshijiang"
export SOL_WALLET_MASTER_DB_PASSWORD=""
export SOL_WALLET_MASTER_DB_NAME="sol_wallet"

export SOL_WALLET_SLAVE_DB_HOST="127.0.0.1"
export SOL_WALLET_SLAVE_DB_PORT=5432
export SOL_WALLET_SLAVE_DB_USER="guoshijiang"
export SOL_WALLET_SLAVE_DB_PASSWORD=""
export SOL_WALLET_SLAVE_DB_NAME="sol_wallet"

export SOL_WALLET_API_CACHE_LIST_SIZE=0
export SOL_WALLET_API_CACHE_LIST_DETAIL=0
export SOL_WALLET_API_CACHE_LIST_EXPIRE_TIME=0
export SOL_WALLET_API_CACHE_DETAIL_EXPIRE_TIME=0
```

**3.2.创建数据库**

```text
create database sol_wallet;
```

**3.3.迁移数据库**

```text
./sol-wallet migrate
```

执行结果

```text
postgres=# \c sol_wallet
您现在已经连接到数据库 "sol_wallet",用户 "guoshijiang".
sol_wallet=# \d
                    关联列表
 架构模式 |     名称     |  类型  |   拥有者
----------+--------------+--------+-------------
 public   | addresses    | 数据表 | guoshijiang
 public   | balances     | 数据表 | guoshijiang
 public   | blocks       | 数据表 | guoshijiang
 public   | deposits     | 数据表 | guoshijiang
 public   | tokens       | 数据表 | guoshijiang
 public   | transactions | 数据表 | guoshijiang
 public   | withdraws    | 数据表 | guoshijiang
(7 行记录)
```

**3.4.批量地址生成**

```text
./sol-wallet generate-address
```

查看效果

```text
sol_wallet=# select count(*) from addresses;
 count
-------
   100
(1 行记录)
```

**3.5.启动钱包充值，提现，归集，转冷等命令**

```text
./sol-wallet wallet
```

启动效果如下：

```text
INFO [07-20|16:26:52.255] exec wallet sync
INFO [07-20|16:26:52.255] loaded chain config                      config="{ChainID:17000 RpcUrl:https://eth-holesky.g.alchemy.com/v2/BvSZ5ZfdIwB-5SDXMz8PfGcbICYQqwrl StartingHeight:1964509 Confirmations:64 DepositInterval:5000 WithdrawInterval:500 CollectInterval:500 ColdInterval:500 BlocksStep:5}"

2024/07/20 16:26:52 /Users/guoshijiang/theweb3/sol-wallet/database/blocks.go:56 record not found
[5.827ms] [rows:0] SELECT * FROM "blocks" ORDER BY number DESC LIMIT 1
INFO [07-20|16:26:52.758] no sync indexed state starting from supplied solana height height=1,964,509
INFO [07-20|16:26:53.471] start deposit......
INFO [07-20|16:26:53.472] start withdraw......
```

## 4.API 和 RPC 服务

**4.1. RPC**

如果修改wallet.proto代码，需要执行[proto.sh](https://proto.sh/)将其编译为 golang 语言。

```text
./proto.sh
```

启动 rpc 服务

```text
./sol-wallet rpc
```

使用grpcui作为工具来请求rpc服务器

```text
grpcui -plaintext 127.0.0.1:8089
```

调用 rpc 提交提现信息

- 请求示例

```text
grpcurl -plaintext -d '{
  "requestId": "11111",
  "chainId": "11",
  "fromAddress": "AqHAt2nQZNbHYjnSUyYkPdzSAybwcFYGjFb3XuQVm6YU",
  "toAddress": "Dx7VEjFSVS4F1LiUJRqUXjZbYryRWNqdEU8dTrna4YWS",
  "tokenAddress": "0x00",
  "amount": "10000000000000000000"
}' 127.0.0.1:8980 services.thewebthree.wallet.WalletService.submitWithdrawInfo
```

- 结果

```text
{
  "code": "2000",
  "msg": "submit withdraw success",
  "hash": "000000"
}
```

**4.2.API**

启动 API 服务

```text
./sol-wallet api
```

获取充值列表

- 请求示例

```text
curl --location --request GET 'http://127.0.0.1:8989/api/v1/deposits?address=AqHAt2nQZNbHYjnSUyYkPdzSAybwcFYGjFb3XuQVm6YU&page=1&pageSize=10' \
--form 'address="AqHAt2nQZNbHYjnSUyYkPdzSAybwcFYGjFb3XuQVm6YU"' \
--form 'page="1"' \
--form 'pageSize="10"'
```

- 返回值

```text
{
    "Current": 1,
    "Size": 10,
    "Total": 1,
    "Records": [
        {
           "guid": "803fd471-e74a-403e-a934-1b917e801518",
            "block_hash": "7UYTQjPtvoHoa9v5rd22LqbUMMi3Ne7SzscwFtzoXcfs",
            "BlockNumber": 1,
            "hash": "48mrFicPuHXjmdbzvNxjGPgasoP6eQbvpQPYFoMnZXfTYTgw7oJAhtgoVpdwu4ep3ZCpf6FxYYgVqYqBicBUFqJK",
            "from_address": "Dx7VEjFSVS4F1LiUJRqUXjZbYryRWNqdEU8dTrna4YWS",
            "to_address": "AqHAt2nQZNbHYjnSUyYkPdzSAybwcFYGjFb3XuQVm6YU",
            "token_address": "519p6BrurRFL3WBZPkGwCikHEoPhtf24H4kryLdspEXd",
            "Fee": 1,
            "Amount": 1000000000000000000,
            "status": 0,
            "tx_sign_hex": "",
            "Timestamp": 1721466415
        }
    ]
}
```

获取提现列表

- 请求示例

```text
curl --location --request GET 'http://127.0.0.1:8989/api/v1/withdrawals?address=Dx7VEjFSVS4F1LiUJRqUXjZbYryRWNqdEU8dTrna4YWS&page=1&pageSize=10' \
--form 'address="Dx7VEjFSVS4F1LiUJRqUXjZbYryRWNqdEU8dTrna4YWS"' \
--form 'page="1"' \
--form 'pageSize="10"'
```

- 结果

```text
{
    "Current": 1,
    "Size": 10,
    "Total": 1,
    "Records": [
        {
            "guid": "803fd471-e74a-403e-a934-1b917e801518",
            "block_hash": "7UYTQjPtvoHoa9v5rd22LqbUMMi3Ne7SzscwFtzoXcfs",
            "BlockNumber": 1,
            "hash": "48mrFicPuHXjmdbzvNxjGPgasoP6eQbvpQPYFoMnZXfTYTgw7oJAhtgoVpdwu4ep3ZCpf6FxYYgVqYqBicBUFqJK",
            "from_address": "Dx7VEjFSVS4F1LiUJRqUXjZbYryRWNqdEU8dTrna4YWS",
            "to_address": "AqHAt2nQZNbHYjnSUyYkPdzSAybwcFYGjFb3XuQVm6YU",
            "token_address": "519p6BrurRFL3WBZPkGwCikHEoPhtf24H4kryLdspEXd",
            "Fee": 1,
            "Amount": 1000000000000000000,
            "status": 0,
            "tx_sign_hex": "",
            "Timestamp": 1721466415
        }
    ]
}
```

提交提现交易

- 请求示例

```text
curl --location --request POST 'http://127.0.0.1:8989/api/v1/submit/withdrawals?fromAddress=Dx7VEjFSVS4F1LiUJRqUXjZbYryRWNqdEU8dTrna4YWS&toAddress=Dx7VEjFSVS4F1LiUJRqUXjZbYryRWNqdEU8dTrna4YWS&tokenAddress=Dx7VEjFSVS4F1LiUJRqUXjZbYryRWNqdEU8dTrna4YWS&amount=1000000000000000000'
```

- 结果

```text
{
    "code": 2000,
    "msg": "submit transaction success"
}
```

# 四.总结

本代码改造之后是可以做为生产代码来用的，可以根据自己的需求改造和业务层的对接，本项目中归集地址和热钱包是共用一个地址，这里可能需要根据具体的业务需求进行改造成多归集地址，多热钱包地址之类的处理。有的项目中可能还设置温钱包这样的业务，也需要根据业务调整一些细节的代码。