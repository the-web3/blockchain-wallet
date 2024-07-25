# 以太坊中心化钱包项目实战

# 一. 以太坊中心化钱包概述

我们都知道，中心化钱包主要用在交易所使用，中心化钱包功能主要包含：批量地址生成，充值，提现，归集，热转冷，冷转热等功能。

## **1. 离线地址生成**

- 调度签名机生成密钥对，签名机吐出公钥
- 使用公钥导出地址

## **2.充值逻辑**

- 获取链上最新块高，从数据库中获取上次解析交易的块高做为起始块高，如果链上的最新块和数据内部的块高一致，则等到下一个块出现了之后再开始去扫块；如果数据库中没有记录，则按照配置的块高开始扫块，起始块高配置默认为 0，如果用户没有配置，那就从 0 开始扫块。
- 获取块里面的交易，判断交易是否为代币转账，如果 to 地址是 Token Address, 则说明为 Token 转账，真正的 To 地址需要从 data 字段里面解出来；如果 To 地址为空，则为合约创建交易，直接跳过。
- 第二步中处理过的交易里面 to 地址是系统内部的用户地址，from 地址是外部地址，说明用户充值，更新交易到数据库中，并更新改地址的余额(注意，如果用户充值金额小于最小充值金额，不给入账)，将交易的状态设置为待确认。
- 交易所在块的过了确认位，将交易状态更新位充值成功并通知业务层。

## **3.归集逻辑**

- 将用户地址上资金全部转到归集地址，这里需要注意的是，有的项目方的归集地址也是热钱包地址
- 使用用户地址构建一笔待签名的交易，将交易编码成 32 位的 Msg, 将 Msg 递交给签名机进行签名，签名成功之后返回 signature
- 使用返回的 signature 解析出来 r, s, v; 使用 r,s,v和交易体构建出完整的交易并将交易发送到区块链网络，将用户地址上的资金所动，余额减掉。
- 扫链获取到交易之后更新交易状态：如果交易成功，更新交易状态到成功，并把用户地址锁定的资金减掉，将资金加到归集地址上；如果交易失败，将用户地址锁定的资金返还到余额，更新交易状态为失败，等待下一次重新归集。

## **4. 提现逻辑**

- 用户在业务层发起提现，业务层把该笔提交到钱包层的提现表，钱包层使用热钱包地址签名一笔交易并发送到区块链网络，并且更新提现表里面的交易位已发送，将交易也同时放到 transaction 表， 将热钱包地址里面提现的资金锁定。

- 扫链扫到这笔之后，通过 TxHash 查到这笔交易，然后匹配 from 地址和 to 地址,  提现的 from 地址是系统内部热钱包地址，to 地址系统外部地址，根据扫链交易状体更新数据库表的状态。 

  💡成功：更新 transaction 和 withdraw 表的交易状态为成功并且将热钱包地址锁定资金减掉 

  💡失败：更新 transaction 表的交易状态为失败并且将热钱包地址锁定资金退回到热钱地址里重新发起提现过程

## **5. 热转冷逻辑**

- 将热钱包地址上的资金转到冷钱包地址，签名流程类似归集，交易签名完成之后将交易发送到区块链网络

- 扫链进程扫到这笔之后，通过 TxHash 查到这笔交易，然后匹配 from 地址和 to 地址,  热转冷的 from 地址是系统内部热钱包地址，to 系统内配置的冷钱包地址，根据扫链交易状体更新数据库表的状态。 

  💡成功：更新 transaction 表的交易状态为成功并且将热钱包地址锁定资金减掉 

  💡失败：更新 transaction 表的交易状态为失败并且将热钱包地址锁定资金退回到热钱地址里重新发起热转冷的过程

## **6.冷转热逻辑**

- 手动操作转账到热钱包地址

- 扫链进程扫到这笔交易之后，匹配 from 和 to 地址,  冷转热的 from 地址是冷钱包，to 地址系统内配置的热钱包地址，根据扫链交易状体更新数据库表的状态。 

  💡成功：更新 transaction 表的交易状态为成功，并将余额加到热钱包地址上去 

  💡失败：更新 transaction 表的交易状态为失败，不用处理热钱包余额

# 二.以太坊钱包代码实战

- 项目代码：https://github.com/the-web3/eth-wallet

## 1.代码简介

- 表结构定义：https://github.com/the-web3/eth-wallet/blob/main/migrations/00001_create_schema.sql
- 充值逻辑：https://github.com/the-web3/eth-wallet/blob/main/wallet/deposit.go
- 提现逻辑： https://github.com/the-web3/eth-wallet/blob/main/wallet/withdraw.go
- 归集转冷逻辑：https://github.com/the-web3/eth-wallet/blob/main/wallet/collection_cold.go

## 2.必要依赖和代码构建

**2.1.必要依赖**

- 数据库：postgres
- Golang: [Go 1.21+](https://golang.org/dl/)

**2.2.构建代码**

```text
make eth-wallet
```

**2.3.构建效果展示**

```text
guoshijiang@192 eth-wallet % ./eth-wallet
NAME:
   eth-wallet - A new cli application

USAGE:
   eth-wallet [global options] command [command options] [arguments...]

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
ETH_WALLET_MIGRATIONS_DIR="./migrations"

ETH_WALLET_CHAIN_ID=17000
ETH_WALLET_RPC_RUL="please type your ethereum rpc url"
ETH_WALLET_STARTING_HEIGHT=1960574
ETH_WALLET_CONFIRMATIONS=32
ETH_WALLET_DEPOSIT_INTERVAL=5s
ETH_WALLET_WITHDRAW_INTERVAL=5s
ETH_WALLET_COLLECT_INTERVAL=5s
ETH_WALLET_BLOCKS_STEP=5

ETH_WALLET_HTTP_PORT=8989
ETH_WALLET_HTTP_HOST="127.0.0.1"
ETH_WALLET_RPC_PORT=8980
ETH_WALLET_RPC_HOST="127.0.0.1"
ETH_WALLET_METRICS_PORT=8990
ETH_WALLET_METRICS_HOST="127.0.0.1"

ETH_WALLET_SLAVE_DB_ENABLE=false

ETH_WALLET_MASTER_DB_HOST="127.0.0.1"
ETH_WALLET_MASTER_DB_PORT=5432
ETH_WALLET_MASTER_DB_USER="guoshijiang"
ETH_WALLET_MASTER_DB_PASSWORD=""
ETH_WALLET_MASTER_DB_NAME="eth_wallet"

ETH_WALLET_SLAVE_DB_HOST="127.0.0.1"
ETH_WALLET_SLAVE_DB_PORT=5432
ETH_WALLET_SLAVE_DB_USER="guoshijiang"
ETH_WALLET_SLAVE_DB_PASSWORD=""
ETH_WALLET_SLAVE_DB_NAME="eth_wallet"

ETH_WALLET_API_CACHE_LIST_SIZE=0
ETH_WALLET_API_CACHE_LIST_DETAIL=0
ETH_WALLET_API_CACHE_LIST_EXPIRE_TIME=0
ETH_WALLET_API_CACHE_DETAIL_EXPIRE_TIME=0
```

**3.2.创建数据库**

```text
create database eth_wallet;
```

**3.3.迁移数据库**

```text
./eth-wallet migrate
```

执行结果

```text
postgres=# \c eth_wallet
您现在已经连接到数据库 "eth_wallet",用户 "guoshijiang".
eth_wallet=# \d
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

eth_wallet=#
```

**3.4.批量地址生成**

```text
./eth-wallet generate-address
```

查看效果

```text
eth_wallet=# select count(*) from addresses;
 count
-------
   100
(1 行记录)

eth_wallet=#
```

3.5.启动钱包充值，提现，归集，转冷等命令

```text
./eth-wallet wallet
```

启动效果如下：

```text
INFO [07-20|16:26:52.255] exec wallet sync
INFO [07-20|16:26:52.255] loaded chain config                      config="{ChainID:17000 RpcUrl: StartingHeight:1964509 Confirmations:64 DepositInterval:5000 WithdrawInterval:500 CollectInterval:500 ColdInterval:500 BlocksStep:5}"

2024/07/20 16:26:52 /Users/guoshijiang/theweb3/eth-wallet/database/blocks.go:56 record not found
[5.827ms] [rows:0] SELECT * FROM "blocks" ORDER BY number DESC LIMIT 1
INFO [07-20|16:26:52.758] no sync indexed state starting from supplied ethereum height height=1,964,509
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
./eth-wallet rpc
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
  "fromAddress": "0xc144779fa97544872879ec162fcd7367141db17f",
  "toAddress": "0xc144779fa97544872879ec162fcd7367141db171",
  "tokenAddress": "0x00",
  "amount": "10000000000000000000"
}' 127.0.0.1:8980 services.thewebthree.wallet.WalletService.submitWithdrawInfo
```

- 结果

```text
{
  "code": "2000",
  "msg": "submit withdraw success",
  "hash": "0x0000000000000000000000000000000000000000000000000000000000000000"
}
```

4.2.**API**

启动 API 服务

```text
./eth-wallet api
```

获取充值列表

- 请求示例

```text
curl --location --request GET 'http://127.0.0.1:8989/api/v1/deposits?address=0xc144779fa97544872879ec162fcd7367141db17f&page=1&pageSize=10' \
--form 'address="0x62a58ec98bbc1a1b348554a19996305edc224e32"' \
--form 'page="1"' \
--form 'pageSize="10"'
```

- 返回值

```text
{
    "Current": 1,
    "Size": 10,
    "Total": 2,
    "Records": [
        {
            "guid": "e6277fd2-4672-11ef-a12f-0e6e6b1f0bae",
            "block_hash": "0x2515a19d875ffdacae62d20a0f6eddfd3a35206aa07b5cfdc0c49abe7148a9cf",
            "BlockNumber": 1964544,
            "hash": "0x19a97821f6337789294eae9f8ce21455aa05b9e31737e3f48394413e3d0d36a1",
            "from_address": "0x72ffaa289993bcada2e01612995e5c75dd81cdbc",
            "to_address": "0xc144779fa97544872879ec162fcd7367141db17f",
            "token_address": "0x0000000000000000000000000000000000000000",
            "Fee": 176820000,
            "Amount": 100000000000000000,
            "status": 1,
            "TransactionIndex": 59,
            "Timestamp": 1721464476
        },
        {
            "guid": "3835ecd8-4672-11ef-a12f-0e6e6b1f0bae",
            "block_hash": "0xcd8835a2f98bd51f3f88be4b248e52534c251b86aaa6bf0ed2785280d67c2f81",
            "BlockNumber": 1964522,
            "hash": "0x6841a22be3ae7b1c138057ae522017f652f49b579b6c5a26b8727e1f1ce899c8",
            "from_address": "0x72ffaa289993bcada2e01612995e5c75dd81cdbc",
            "to_address": "0xc144779fa97544872879ec162fcd7367141db17f",
            "token_address": "0x0000000000000000000000000000000000000000",
            "Fee": 182301000,
            "Amount": 10000000000000000,
            "status": 1,
            "TransactionIndex": 26,
            "Timestamp": 1721464185
        }
    ]
}
```

获取提现列表

- 请求示例

```text
curl --location --request GET 'http://127.0.0.1:8989/api/v1/withdrawals?address=0x62a58ec98bbc1a1b348554a19996305edc224e32&page=1&pageSize=10' \
--form 'address="0x62a58ec98bbc1a1b348554a19996305edc224e32"' \
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
            "block_hash": "0x0000000000000000000000000000000000000000000000000000000000000000",
            "BlockNumber": 1,
            "hash": "0x0000000000000000000000000000000000000000000000000000000000000000",
            "from_address": "0x62a58ec98bbc1a1b348554a19996305edc224e32",
            "to_address": "0x62a58ec98bbc1a1b348554a19996305edc224e32",
            "token_address": "0x62a58ec98bbc1a1b348554a19996305edc224e32",
            "Fee": 1,
            "Amount": 1000000000000000000,
            "status": 0,
            "TransactionIndex": 1,
            "tx_sign_hex": "",
            "Timestamp": 1721466415
        }
    ]
}
```

提交提现交易

- 请求示例

```text
curl --location --request POST 'http://127.0.0.1:8989/api/v1/submit/withdrawals?fromAddress=0x62a58ec98bbc1a1b348554a19996305edc224e32&toAddress=0x62a58ec98bbc1a1b348554a19996305edc224e32&tokenAddress=0x62a58ec98bbc1a1b348554a19996305edc224e32&amount=1000000000000000000'
```

- 结果

```text
{
    "code": 2000,
    "msg": "submit transaction success"
}
```

# 三. 总结

本代码改造之后是可以做为生产代码来用的，可以根据自己的需求改造和业务层的对接，本项目中归集地址和热钱包是共用一个地址，这里可能需要根据具体的业务需求进行改造成多归集地址，多热钱包地址之类的处理。有的项目中可能还设置温钱包这样的业务，也需要根据业务调整一些细节的代码。