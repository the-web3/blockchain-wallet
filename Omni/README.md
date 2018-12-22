# 第九章：omni-USDT钱包开发

USDT是一种将加密货币与法定货币美元挂钩的虚拟货币，是一种保存在外汇储备账户、获得法定货币支持的虚拟货币。也是目前数字货币中最稳定的币，USDT目前发行了两种代币，一种是基于以太坊标准的ERC20 Token，另一种是基于Omni Layer协议的代币，在omni上，USDT代币ID为31。omni上的代币也是目前交易所支持最广泛的代币，而ERC20的USDT Token使用的人似乎很少。关于以太坊ERC20代币的开发在前面的文章中已经提到过，在这里我们就不再多做叙述。

Omni是一个创建和交易自定义数字资产和货币的平台。 它是建立在最受欢迎，最受审核，最安全的区块链之上的软件层-比特币。 Omni交易是比特币交易，可在比特币区块链上实现下一代功能。 Omni Core是一个增强的比特币核心，提供比特币的所有功能以及进化版的Omni Layer功能。

下面我们来讲解一下整个omni—USDT在钱包中接入过程。

## 什么是Omni Layer

Omni Layer是一种通信协议，使用比特币区块链实现智能合约，用户货币和分散式点对点交换等功能。用于描述Omni Layer与比特币关系的常见类比是HTTP到TCP/IP：HTTP，如Omni Layer，是TCP/IP的更基础的传输和互联网层的应用层，如比特币。

## 什么是Omni Core

Omni Core是一种快速，便携的Omni Layer实现，基于比特币核心代码库（目前为0.13）。 这种实现不需要与比特币核心无关的外部依赖性，并且像其他比特币节点一样是比特币网络的原生。 它目前支持钱包模式，可在三个平台上无缝使用：Windows，Linux和Mac OS。 Omni Layer扩展通过JSON-RPC接口公开。 开发已整合到Omni Core产品上。


## 一.omni钱包节点搭建

由于omni没有裸露的钱包节点可用，如果您需要开发omni钱包，那么就得搭建自己的钱包节点。

### 1.测试网


Testnet模式允许Omni Core在比特币testnet区块链上运行以进行安全测试。

* 要在testnet模式下运行Omni Core，请使用以下选项运行Omni Core：-testnet。
* 要在testnet上接收OMNI（和TOMNI），请将TBTC发送到moneyqMan7uh8FqdCA2BV5yZ8qVrc9ikLP。每1 TBTC，您将获得100 OMNI和100 TOMNI。

### 2.钱包节点搭建


#### 2.1.安装环境说明

* Centos系统
* C++ 和 java
* OmniCore 0.3.1 fork bitcoin 0.13

#### 2.2.必须依赖项

* libssl
加密库，用于随机数产生和椭圆曲线加密

* libboost
应用库，主要用里面的线程、数据结构等,版本需大于1.53

* libevent
网络，OS独立的异步网络

#### 2.3.可选依赖项

* miniupnpc	
防火墙穿越支持

* libdb4.8
钱包存储（仅仅在钱包可以使用的时候需要）

* qt
界面支撑（仅仅在界面能使用的时候需要）

* protobuf 
支付协议中的数据交换格式（仅仅在界面能使用的时候需要）

* libqrencode
生成QR码（二维码，仅仅在界面可用的情况下需要）

* univalue
解析与生成

* libzmq3	
生成zmq消息（ZMQ，ZeroMQ，消息队列），版本号需大于4.x

如果你的系统上没有g++，请您自行安装

#### 2.4.内存要求

C ++编译器需要大量内存。 在编译比特币核心时，建议至少有1.5 GB的内存可用。 在较少的系统上，可以通过额外的CXXFLAGS调整gcc以节省内存：`./configure CXXFLAGS="--param ggc-min-expand=1 --param ggc-min-heapsize=32768"
`
#### 2.5.安装与配置

##### 2.4.1.安装git,如果有git，此步可以略过

    sudo yum install git
    sudo yum install pkg-config

##### 2.4.2.安装依赖项

    sudo yum install build-essential libtool autotools-dev automake pkg-config libssl-dev libevent-dev bsdmainutils
  
##### 2.4.3.安装boost库

    sudo yum install libboost-system-dev libboost-filesystem-dev libboost-chrono-dev libboost-program-options-dev libboost-test-dev libboost-thread-dev

为了兼容各个系统版本，建议安装所有的boost开发包

    sudo yum install libboost-all-dev

##### 2.4.4. 钱包需要BerkeleyDB。 db4.8包可以在这里找到。 您可以使用以下命令添加存储库并进行安装：

    sudo yum install software-properties-common
    sudo add-apt-repository ppa:bitcoin/bitcoin
    sudo yum update
    sudo yum install libdb4.8-dev libdb4.8++-dev

##### 2.4.5.可选安装项

* libminiupnpc 

       sudo yum install libminiupnpc-dev

* ZMQ denpendencies 

      sudo yum install libzmq3-dev


##### 2.4.5. GUI依赖项安装

如果需要编译bitcoin-qt，则需要安装qt开发环境，qt4和qt5都是可以的，如果两者都安装了，则默认使用qt5，也可以在配置时，使用--with-gui=qt4来进行选择使用qt4版本，或者使用--without-gui来选择不编译gui。

qt5的安装方法 

    sudo yum install libqt5gui5 libqt5core5a libqt5dbus5 qttools5-dev qttools5-dev-tools libprotobuf-dev protobuf-compiler
qt4的安装方法 

    sudo yum install libqt4-dev libprotobuf-dev protobuf-compiler
    
libqrencode libqrendoce 是qr码（二维码）的支持模块，可选安装 

    sudo yum install libqrencode-dev
    
如果这些环境包被安装，则会被configure检测到，bitcoin-qt会默认编译生成。

##### 2.4.6.克隆代码

    git clone https://github.com/OmniLayer/omnicore.git


##### 2.4.7.构建OmniCore

    cd omnicore/
    ./autogen.sh
    ./configure
    make && make install 

编译完成之后，在omnicore/src/会有omnicored, omnicore-cli等可执行文件。其来执行方式与bitcoin一样，需要一个名为bitcoin.conf的配置文件。下面是同步数据的命令

    ./omnicored -conf=配置文件目录 -datadir=数据同步目录

启动之后，可以在数`据同步目录/omnicore.log`下面查看日志。

omni同步区块的时间和你的网络相关，我这边同步了3天才同步完成，现在大约有55万多个块。

##### 2.4.8. bitcoin 基本配置
启动之前，需要配置位于工程目录之下的.bitcoin文件夹中的OmniCore配置文件bitcoin.conf

    server=1  
    txindex=1 
    rpcuser=你的rpc用户名
    rpcpassword=你的rpc密码
    rpcallowip=127.0.0.1 
    rpcport=8332
    paytxfee=0.00001
    minrelaytxfee=0.00001
    datacarriersize=80
    logtimestamps=1
    omnidebug=tally  
    omnidebug=packets
    omnidebug=pending


server=1代表开启RPC访问
txindex=1代表事务初始索引
recuser和rpcpassword 代表rpc访问的身份验证，
rpcallowip 和rpcport代表允许访问钱包的ip地址及端口。
paytxfee和minrelattxfee控制bitcoin交易的手续费，Omni交易也属于一种特殊的比特币交易，打包与广播也需要向矿工支付费用。手续费设置过低会造成交易确认慢甚至交易失败，手续费过高会造成资源的浪费(以2018.09.13的BTC价格换算，每多消耗0.0001btc需要浪费4rmb)，所以设置动态配置交易手续费十分必要。预估比特币交易手续费可以使用下面的网址bitcoinfees.earn，buybitcoinworldwide。假设当前预估的比特币交易费率为0.0000001BTC/Byte,那么需要设置paytxfee=0.00001BTC/kByte。


##### 2.4.8.使用节点条用一次RPC接口

导入地址，执行以下命令

    ./omnicore-cli importaddress 1C6UYrmbtvdi8dHZNnZD3YoVwit2mccSgw

然后去你同步数据的目录查看导入日志，同步日志效果如下。

.： 
    ![.： 
](https://github.com/guoshijiang/blockchain-wallet/blob/master/img/640.jpg)


列出UTXO，命令如下：

    /omnicore-cli "listunspent" 0 999999 '["1C6UYrmbtvdi8dHZNnZD3YoVwit2mccSgw"]'


.： 
    ![.： 
](https://github.com/guoshijiang/blockchain-wallet/blob/master/img/641.png)

## 二.Omni浏览器接口

建议重复调用之间的最短等待时间为5-10秒，以防止触发api速率限制器。触发后，系统将响应错误消息： `{‘error’:True, ‘msg’:‘Rate Limit Reached. Please limit consecutive requests to no more than “limit” every “time frame in seconds”.’}` 。以及后续呼叫将被禁止长达一分钟。如果一直重复调用，系统将会禁止你条用API接口。

### API详细说明

#### 1.获取余额

##### 1.1.同时获取多个地址的余额

接口名字：https://api.omniexplorer.info/v2/address/addr
请求方式：POST
调用示例：
* curl方式调用

        curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -H "Content-Type: application/x-www-form-urlencoded" -d "addr=1FoWyxwPXuj4C6abqwhjDWdz6D4PZgYRjA&addr=1KYiKJEfdJtap9QX2v9BXJMpz2SfU4pgZw" "https://api.omniwallet.org/v2/address/addr/"

* http方式调用说明

        POST /v2/address/addr/ HTTP/1.1
        Host: api.omniwallet.org
        Content-Type: application/x-www-form-urlencoded
        Content-Type: application/x-www-form-urlencoded

        addr=1FoWyxwPXuj4C6abqwhjDWdz6D4PZgYRjA&addr=1KYiKJEfdJtap9QX2v9BXJMpz2SfU4pgZw

返回结果：

    {
        "1FoWyxwPXuj4C6abqwhjDWdz6D4PZgYRjA": {
            "balance": [
                {
                    "pendingpos": "231154059052026",
                    "reserved": "0",
                    "divisible": true,
                    "symbol": "SP31",
                    "value": "81672408571806609",
                    "frozen": "0",
                    "pendingneg": "-16422699999141",
                    "id": "31"
                },
                {
                    "pendingpos": "0",
                    "divisible": true,
                    "symbol": "BTC",
                    "value": "860484222",
                    "pendingneg": "0",
                    "error": false,
                    "id": 0
                }
            ]
        },
        "1KYiKJEfdJtap9QX2v9BXJMpz2SfU4pgZw": {
            "balance": [
                {
                    "pendingpos": "0",
                    "reserved": "0",
                    "divisible": true,
                    "symbol": "SP31",
                    "value": "12587438660387443",
                    "frozen": "0",
                    "pendingneg": "0",
                    "id": "31"
                },
                {
                    "pendingpos": "0",
                    "divisible": true,
                    "symbol": "BTC",
                    "value": "8916129408",
                    "pendingneg": "0",
                    "error": false,
                    "id": 0
                }
            ]
        }
    }

##### 1.2.获取单个地址的余额

返回给定地址的余额信息。 对于单个查询中的多个地址，上面的API

接口名字：https://api.omniexplorer.info/v1/address/addr/
请求方式：POST
调用示例：
* curl方式调用

        curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -H "Content-Type: application/x-www-form-urlencoded" -d "addr=1EXoDusjGwvnjZUyKkxZ4UHEf77z6A5S4P" "https://api.omniexplorer.info/v1/address/addr/"


* http方式调用说明

        POST /v1/address/addr/ HTTP/1.1
        Host: api.omniexplorer.info
        Content-Type: application/x-www-form-urlencoded
        Content-Type: application/x-www-form-urlencoded

        addr=1EXoDusjGwvnjZUyKkxZ4UHEf77z6A5S4P


返回结果：

        {
            "balance": [
                {
                    "divisible": true,
                    "frozen": "0",
                    "id": "1",
                    "pendingneg": "0",
                    "pendingpos": "0",
                    "reserved": "0",
                    "symbol": "OMNI",
                    "value": "3054147959984"
                },
                {
                    "divisible": true,
                    "frozen": "0",
                    "id": "2",
                    "pendingneg": "0",
                    "pendingpos": "0",
                    "reserved": "0",
                    "symbol": "T-OMNI",
                    "value": "0"
                },
                {
                    "divisible": true,
                    "error": false,
                    "id": 0,
                    "pendingneg": "0",
                    "pendingpos": "0",
                    "symbol": "BTC",
                    "value": "214236735"
                }
            ]
        }
    
#### 2.返回给出地址的余额信息和交易历史记录

接口名字：https://api.omniexplorer.info/v1/address/addr/details/
请求方式：POST
调用示例：
* curl方式调用

        curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -H "Content-Type: application/x-www-form-urlencoded" -d "addr=1EXoDusjGwvnjZUyKkxZ4UHEf77z6A5S4P" "https://api.omniexplorer.info/v1/address/addr/details/"


* http方式调用说明

        POST /v1/address/addr/details/ HTTP/1.1
        Host: api.omniexplorer.info
        Content-Type: application/x-www-form-urlencoded
        Content-Type: application/x-www-form-urlencoded

        addr=1EXoDusjGwvnjZUyKkxZ4UHEf77z6A5S4P


返回结果：

    {
        "address": "1EXoDusjGwvnjZUyKkxZ4UHEf77z6A5S4P",
        "balance": [
            {
                "divisible": true,
                "frozen": "0",
                "id": "1",
                "pendingneg": "0",
                "pendingpos": "0",
                "reserved": "0",
                "symbol": "OMNI",
                "value": "3054147959984"
            },
            {
                "divisible": true,
                "frozen": "0",
                "id": "2",
                "pendingneg": "0",
                "pendingpos": "0",
                "reserved": "0",
                "symbol": "T-OMNI",
                "value": "0"
            },
            {
                "divisible": true,
                "error": false,
                "id": 0,
                "pendingneg": "0",
                "pendingpos": "0",
                "symbol": "BTC",
                "value": "214236735"
            }
        ],
        "pages": 3,
        "transactions": [
            {
                "amount": "0.00000100",
                "block": 342733,
                "blockhash": "000000000000000014a6858e7da93606ffe624331a2f295732c8c9499ffa1025",
                "blocktime": 1423503818,
                "confirmations": 176418,
                "divisible": true,
                "fee": "0.00001000",
                "ismine": false,
                "positioninblock": 1569,
                "propertyid": 1,
                "propertyname": "Omni",
                "sendingaddress": "1F9jCeixNbK6GEkdQFq1ejkbJjAGwVVVqG",
                "txid": "318fb016144f3dadb16acf39cd7e00089b808418f1c4b343926af55882824966",
                "type": "Send To Owners",
                "type_int": 3,
                "valid": true,
                "version": 0
            },
            {
                "additional records": "..."
            }
        ]
    }


#### 4.返回与Armory离线事务一起使用的未签名事务的Armory编码版本。 数据：unsigned_hex：原始比特币十六进制格式化tx转换为pubkey：发送地址的pubkey

接口名字：https://api.omniexplorer.info/v1/armory/getunsigned
请求方式：POST
调用示例：
* curl方式调用

        curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -H "Content-Type: application/x-www-form-urlencoded" -d "unsigned_hex=01000000011c2d89d15d4853194e0e617e5d6d6d151752b09f72bf62a34453643270ebc763000000001976a914946cb2e08075bcbaf157e47bcb67eb2b2339d24288acffffffff01d8d60000000000001976a914946cb2e08075bcbaf157e47bcb67eb2b2339d24288ac00000000&pubkey=04ad90e5b6bc86b3ec7fac2c5fbda7423fc8ef0d58df594c773fa05e2c281b2bfe877677c668bd13603944e34f4818ee03cadd81a88542b8b4d5431264180e2c28" "https://api.omniexplorer.info/v1/armory/getunsigned"


* http方式调用说明

        POST /v1/armory/getunsigned HTTP/1.1
        Host: api.omniexplorer.info
        Content-Type: application/x-www-form-urlencoded
        Content-Type: application/x-www-form-urlencoded

        unsigned_hex=01000000011c2d89d15d4853194e0e617e5d6d6d151752b09f72bf62a34453643270ebc763000000001976a914946cb2e08075bcbaf157e47bcb67eb2b2339d24288acffffffff01d8d60000000000001976a914946cb2e08075bcbaf157e47bcb67eb2b2339d24288ac00000000&pubkey=04ad90e5b6bc86b3ec7fac2c5fbda7423fc8ef0d58df594c773fa05e2c281b2bfe877677c668bd13603944e34f4818ee03cadd81a88542b8b4d5431264180e2c28


返回结果：

    {
        "armoryUnsigned": "=====TXSIGCOLLECT-7fK9x5wF======================================\nAQAAAPm+tNkAAAAAAf2gAQEAAAD5vrTZHC2J0V1IUxlODmF+XW1tFRdSsJ9yv2Kj\nRFNkMnDrx2MAAAAA/SUBAQAAAAHb6cvxUOY3xjmumbvja4ws2Y91+lIUyjV3kswG\nZmxHNAAAAABqRzBEAiAWH+zK7+MqKy/RpARlwAxlkGmB2oI5jwXCzwXlrgoDdwIg\nRIt5APjkheiGF64yCgGDXHpLdlB7cYRgvuEePdRvee0BIQIx3eVJDpRTFPeTFz1z\nD/sd7M8z0/5dZUFpNFHpPFrinv////8EYOoAAAAAAAAZdqkUlGyy4IB1vLrxV+R7\ny2frKyM50kKIrJB3dAEAAAAAGXapFJHmmj4F756OJPTktKHCmlUL83kWiKxg6gAA\nAAAAABl2qRSCgUFaaIQzk5laBF7zFYAlb2gEyYisYOoAAAAAAAAZdqkUgQAAAAAA\nAAACAAAAAAX14QAAAACIrAAAAAAAAAD/////AUEErZDltryGs+x/rCxfvadCP8jv\nDVjfWUx3P6BeLCgbK/6HdnfGaL0TYDlE409IGO4Dyt2BqIVCuLTVQxJkGA4sKAAA\nATQBAAAA+b602Rl2qRSUbLLggHW8uvFX5HvLZ+srIznSQois2NYAAAAAAAAAAARO\nT05FAAAAAAAAAA==\n================================================================"
    }


#### 5.从armory交易中解码并返回原始十六进制和签名状态。 数据：armory_tx：文本格式的armory交易

接口名字：https://api.omniexplorer.info/v1/armory/getrawtransaction
请求方式：POST
调用示例：
* curl方式调用

        curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -H "Content-Type: application/x-www-form-urlencoded" -d "armory_tx======TXSIGCOLLECT-7fK9x5wF======================================
        AQAAAPm+tNkAAAAAAf2gAQEAAAD5vrTZHC2J0V1IUxlODmF+XW1tFRdSsJ9yv2Kj
        RFNkMnDrx2MAAAAA/SUBAQAAAAHb6cvxUOY3xjmumbvja4ws2Y91+lIUyjV3kswG
        ZmxHNAAAAABqRzBEAiAWH+zK7+MqKy/RpARlwAxlkGmB2oI5jwXCzwXlrgoDdwIg
        RIt5APjkheiGF64yCgGDXHpLdlB7cYRgvuEePdRvee0BIQIx3eVJDpRTFPeTFz1z
        D/sd7M8z0/5dZUFpNFHpPFrinv////8EYOoAAAAAAAAZdqkUlGyy4IB1vLrxV+R7
        y2frKyM50kKIrJB3dAEAAAAAGXapFJHmmj4F756OJPTktKHCmlUL83kWiKxg6gAA
        AAAAABl2qRSCgUFaaIQzk5laBF7zFYAlb2gEyYisYOoAAAAAAAAZdqkUgQAAAAAA
        AAACAAAAAAX14QAAAACIrAAAAAAAAAD/////AUEErZDltryGs+x/rCxfvadCP8jv
        DVjfWUx3P6BeLCgbK/6HdnfGaL0TYDlE409IGO4Dyt2BqIVCuLTVQxJkGA4sKAAA
        ATQBAAAA+b602Rl2qRSUbLLggHW8uvFX5HvLZ+srIznSQois2NYAAAAAAAAAAARO
        T05FAAAAAAAAAA==
        ================================================================" "https://api.omniexplorer.info/v1/armory/getrawtransaction"

* http方式调用说明

        POST /v1/armory/getrawtransaction HTTP/1.1
        Host: api.omniexplorer.info
        Content-Type: application/x-www-form-urlencoded
        Content-Type: application/x-www-form-urlencoded

        armory_tx======TXSIGCOLLECT-7fK9x5wF======================================
        AQAAAPm+tNkAAAAAAf2gAQEAAAD5vrTZHC2J0V1IUxlODmF+XW1tFRdSsJ9yv2Kj
        RFNkMnDrx2MAAAAA/SUBAQAAAAHb6cvxUOY3xjmumbvja4ws2Y91+lIUyjV3kswG
        ZmxHNAAAAABqRzBEAiAWH+zK7+MqKy/RpARlwAxlkGmB2oI5jwXCzwXlrgoDdwIg
        RIt5APjkheiGF64yCgGDXHpLdlB7cYRgvuEePdRvee0BIQIx3eVJDpRTFPeTFz1z
        D/sd7M8z0/5dZUFpNFHpPFrinv////8EYOoAAAAAAAAZdqkUlGyy4IB1vLrxV+R7
        y2frKyM50kKIrJB3dAEAAAAAGXapFJHmmj4F756OJPTktKHCmlUL83kWiKxg6gAA
        AAAAABl2qRSCgUFaaIQzk5laBF7zFYAlb2gEyYisYOoAAAAAAAAZdqkUgQAAAAAA
        AAACAAAAAAX14QAAAACIrAAAAAAAAAD/////AUEErZDltryGs+x/rCxfvadCP8jv
        DVjfWUx3P6BeLCgbK/6HdnfGaL0TYDlE409IGO4Dyt2BqIVCuLTVQxJkGA4sKAAA
        ATQBAAAA+b602Rl2qRSUbLLggHW8uvFX5HvLZ+srIznSQois2NYAAAAAAAAAAARO
        T05FAAAAAAAAAA==
        ================================================================

返回结果：

    {
        "rawTransaction": "01000000011c2d89d15d4853194e0e617e5d6d6d151752b09f72bf62a34453643270ebc7630000000000ffffffff01d8d60000000000001976a914946cb2e08075bcbaf157e47bcb67eb2b2339d24288ac00000000",
        "signed": false
    }


#### 6.解码签名的交易信息

接口名字：https://api.omniexplorer.info/v1/decode/
请求方式：POST
调用示例：
* curl方式调用

        curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -H "Content-Type: application/x-www-form-urlencoded" -d "hex=0100000001b8442ba55406897a5c5751d3b4bb841d1686dbf43de16e1f4d581a2274eecd8e000000006b483045022100ac16e6e938d1353440b88d41e7de9e7ef3d232fb8db04ed639885596d0f8df260220297fc6fb9d39d9042b859b07dd78b634827f8d541213c16c0d68b40b60739372012103124d5b2ba19187be886e4bfa4c5c66cbdac7c6249825ff4fa3c7e05479a08823feffffff030000000000000000166a146f6d6e69000000000000001f000000003b02338075910700000000001976a914025d87dd0602bd86308b354e038f82ba1e9fe94688ac22020000000000001976a91488d924f51033b74a895863a5fb57fd545529df7d88acf9eb0700" "https://api.omniexplorer.info/v1/decode/"


* http方式调用说明

        POST /v1/decode/ HTTP/1.1
        Host: api.omniexplorer.info
        Content-Type: application/x-www-form-urlencoded
        Content-Type: application/x-www-form-urlencoded

        hex=0100000001b8442ba55406897a5c5751d3b4bb841d1686dbf43de16e1f4d581a2274eecd8e000000006b483045022100ac16e6e938d1353440b88d41e7de9e7ef3d232fb8db04ed639885596d0f8df260220297fc6fb9d39d9042b859b07dd78b634827f8d541213c16c0d68b40b60739372012103124d5b2ba19187be886e4bfa4c5c66cbdac7c6249825ff4fa3c7e05479a08823feffffff030000000000000000166a146f6d6e69000000000000001f000000003b02338075910700000000001976a914025d87dd0602bd86308b354e038f82ba1e9fe94688ac22020000000000001976a91488d924f51033b74a895863a5fb57fd545529df7d88acf9eb0700


返回结果：

        {
            "BTC": {
                "hash": "5451c8e67d7ab3f947064337e8cf87c416f1fb163630913dcf307d4ca5d5ccaa",
                "locktime": 519161,
                "size": 257,
                "txid": "5451c8e67d7ab3f947064337e8cf87c416f1fb163630913dcf307d4ca5d5ccaa",
                "version": 1,
                "vin": [
                    {
                        "scriptSig": {
                            "asm": "3045022100ac16e6e938d1353440b88d41e7de9e7ef3d232fb8db04ed639885596d0f8df260220297fc6fb9d39d9042b859b07dd78b634827f8d541213c16c0d68b40b60739372[ALL] 03124d5b2ba19187be886e4bfa4c5c66cbdac7c6249825ff4fa3c7e05479a08823",
                            "hex": "483045022100ac16e6e938d1353440b88d41e7de9e7ef3d232fb8db04ed639885596d0f8df260220297fc6fb9d39d9042b859b07dd78b634827f8d541213c16c0d68b40b60739372012103124d5b2ba19187be886e4bfa4c5c66cbdac7c6249825ff4fa3c7e05479a08823"
                        },
                        "sequence": 4294967294,
                        "txid": "8ecdee74221a584d1f6ee13df4db86161d84bbb4d351575c7a890654a52b44b8",
                        "vout": 0
                    }
                ],
                "vout": [
                    {
                        "n": 0,
                        "scriptPubKey": {
                            "asm": "OP_RETURN 6f6d6e69000000000000001f000000003b023380",
                            "hex": "6a146f6d6e69000000000000001f000000003b023380",
                            "type": "nulldata"
                        },
                        "value": 0.0
                    },
                    {
                        "n": 1,
                        "scriptPubKey": {
                            "addresses": [
                                "1DWQ1gZ8VhL1fUCABqKbXtUZv63roGvXf"
                            ],
                            "asm": "OP_DUP OP_HASH160 025d87dd0602bd86308b354e038f82ba1e9fe946 OP_EQUALVERIFY OP_CHECKSIG",
                            "hex": "76a914025d87dd0602bd86308b354e038f82ba1e9fe94688ac",
                            "reqSigs": 1,
                            "type": "pubkeyhash"
                        },
                        "value": 0.00495989
                    },
                    {
                        "n": 2,
                        "scriptPubKey": {
                            "addresses": [
                                "1DUb2YYbQA1jjaNYzVXLZ7ZioEhLXtbUru"
                            ],
                            "asm": "OP_DUP OP_HASH160 88d924f51033b74a895863a5fb57fd545529df7d OP_EQUALVERIFY OP_CHECKSIG",
                            "hex": "76a91488d924f51033b74a895863a5fb57fd545529df7d88ac",
                            "reqSigs": 1,
                            "type": "pubkeyhash"
                        },
                        "value": 5.46e-06
                    }
                ],
                "vsize": 257
            },
            "OMNI": {
                "amount": "9.90000000",
                "confirmations": 0,
                "divisible": true,
                "fee": "0.00003465",
                "ismine": false,
                "propertyid": 31,
                "referenceaddress": "1DUb2YYbQA1jjaNYzVXLZ7ZioEhLXtbUru",
                "sendingaddress": "1DWQ1gZ8VhL1fUCABqKbXtUZv63roGvXf",
                "txid": "5451c8e67d7ab3f947064337e8cf87c416f1fb163630913dcf307d4ca5d5ccaa",
                "type": "Simple Send",
                "type_int": 0,
                "version": 0
            },
            "Reference": "1DUb2YYbQA1jjaNYzVXLZ7ZioEhLXtbUru",
            "Sender": "1DWQ1gZ8VhL1fUCABqKbXtUZv63roGvXf",
            "error": "None",
            "inputs": {
                "1DWQ1gZ8VhL1fUCABqKbXtUZv63roGvXf": 500000
            }
        }

#### 7.返回omnidex已打开订单的当前有效/可用基础货币列表。 数据：生态系统：主网/生产生态系统为1，测试/开发生态系统为2

接口名字：https://api.omniexplorer.info/v1/omnidex/designatingcurrencies
请求方式：POST
调用示例：
* curl方式调用

        curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -H "Content-Type: application/x-www-form-urlencoded" -d "ecosystem=1" "https://api.omniexplorer.info/v1/omnidex/designatingcurrencies"


* http方式调用说明

        POST /v1/omnidex/designatingcurrencies HTTP/1.1
        Host: api.omniexplorer.info
        Content-Type: application/x-www-form-urlencoded
        Content-Type: application/x-www-form-urlencoded

        ecosystem=1


返回结果：

        {
            "currencies": [
                {
                    "displayname": "Omni #1",
                    "propertyid": 1,
                    "propertyname": "Omni"
                },
                {
                    "displayname": "MaidSafeCoin #3",
                    "propertyid": 3,
                    "propertyname": "MaidSafeCoin"
                },
                {
                    "displayname": "TetherUS #31",
                    "propertyid": 31,
                    "propertyname": "TetherUS"
                },
                {
                    "additional records": "..."
                }
            ],
            "status": 200
        }

#### 8.返回与查询的Property ID相关的事务列表（每页最多10个）。 返回的交易类型包括：创建Tx，变更发行人txs，授予Txs，撤销Txs，Crowdsale参与Txs，早期关闭Crowdsale tx

接口名字：https://api.omniexplorer.info/v1/properties/gethistory
请求方式：POST
调用示例：
* curl方式调用

        curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -H "Content-Type: application/x-www-form-urlencoded" -d "page=0" "https://api.omniexplorer.info/v1/properties/gethistory/3"


* http方式调用说明

        POST /v1/properties/gethistory/3 HTTP/1.1
        Host: api.omniexplorer.info
        Content-Type: application/x-www-form-urlencoded
        Content-Type: application/x-www-form-urlencoded

        page=0


返回结果：

        {
            "pages": 19,
            "total": 193,
            "transactions": [
                {
                    "amount": "0",
                    "block": 297115,
                    "blockhash": "00000000000000004c93a71e1b0dd876c07cbc5ebd385ff8e5ba8379ffa1e377",
                    "blocktime": 1398154020,
                    "category": "Crowdsale",
                    "confirmations": 213140,
                    "data": "SAFE Network Crowdsale (MSAFE)",
                    "deadline": 1400749200,
                    "divisible": false,
                    "earlybonus": 10,
                    "ecosystem": "main",
                    "fee": "0.00010001",
                    "ismine": false,
                    "percenttoissuer": 0,
                    "positioninblock": 215,
                    "propertyid": 3,
                    "propertyiddesired": 1,
                    "propertyname": "MaidSafeCoin",
                    "propertytype": "indivisible",
                    "sendingaddress": "1ARjWDkZ7kT9fwjPrjcQyvbXDkEySzKHwu",
                    "subcategory": "MaidSafe",
                    "tokensperunit": "0.00003400",
                    "txid": "86f214055a7f4f5057922fd1647e00ef31ab0a3ff15217f8b90e295f051873a7",
                    "type": "Create Property - Variable",
                    "type_int": 51,
                    "url": "www.buysafecoins.com",
                    "valid": true,
                    "version": 0
                },
                {
                    "amount": "100.00000000",
                    "block": 297117,
                    "blockhash": "00000000000000002cf2f281f4a69c6757f9e468fd30b50c5c85433a7b17b1c5",
                    "blocktime": 1398155998,
                    "confirmations": 213138,
                    "divisible": true,
                    "fee": "0.00050000",
                    "ismine": false,
                    "issuertokens": "0",
                    "positioninblock": 83,
                    "propertyid": 1,
                    "purchasedpropertydivisible": false,
                    "purchasedpropertyid": 3,
                    "purchasedpropertyname": "MaidSafeCoin",
                    "purchasedtokens": "485781",
                    "referenceaddress": "1ARjWDkZ7kT9fwjPrjcQyvbXDkEySzKHwu",
                    "sendingaddress": "1LEVELTb6dkZmsUXgQE8nqc8naAVotX86D",
                    "txid": "4ec15360a03d23c069c2fd4a035cfee28af5ddfdd5a6839934dd2629129d477e",
                    "type": "Crowdsale Purchase",
                    "type_int": 0,
                    "valid": true,
                    "version": 0
                },
                {
                    "additional records": "..."
                }
            ]
        }

#### 9.返回查询地址创建的属性列表。

接口名字：https://api.omniexplorer.info/v1/properties/listbyowner
请求方式：POST
调用示例：
* curl方式调用

        curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -H "Content-Type: application/x-www-form-urlencoded" -d "addresses=1ARjWDkZ7kT9fwjPrjcQyvbXDkEySzKHwu" "https://api.omniexplorer.info/v1/properties/listbyowner"

* http方式调用说明

        POST /v1/properties/listbyowner HTTP/1.1
        Host: api.omniexplorer.info
        Content-Type: application/x-www-form-urlencoded
        Content-Type: application/x-www-form-urlencoded

        addresses=1ARjWDkZ7kT9fwjPrjcQyvbXDkEySzKHwu

返回结果：

        {
            "properties": [
                {
                    "active": false,
                    "addedissuertokens": "0",
                    "amount": "0",
                    "amountraised": "93249.21601551",
                    "block": 297115,
                    "blockhash": "00000000000000004c93a71e1b0dd876c07cbc5ebd385ff8e5ba8379ffa1e377",
                    "blocktime": 1398154020,
                    "category": "Crowdsale",
                    "closedearly": true,
                    "closetx": "b8864525a2eef4f76a58f33a4af50dc24461445e1a420e21bcc99a1901740e79",
                    "confirmations": 213140,
                    "creationtxid": "86f214055a7f4f5057922fd1647e00ef31ab0a3ff15217f8b90e295f051873a7",
                    "data": "SAFE Network Crowdsale (MSAFE)",
                    "deadline": 1400749200,
                    "divisible": false,
                    "earlybonus": 10,
                    "ecosystem": "main",
                    "endedtime": 1398173740,
                    "fee": "0.00010001",
                    "fixedissuance": false,
                    "ismine": false,
                    "issuer": "1ARjWDkZ7kT9fwjPrjcQyvbXDkEySzKHwu",
                    "managedissuance": false,
                    "maxtokens": false,
                    "name": "MaidSafeCoin",
                    "percenttoissuer": 0,
                    "positioninblock": 215,
                    "propertyid": 3,
                    "propertyiddesired": 1,
                    "propertyname": "MaidSafeCoin",
                    "propertytype": "indivisible",
                    "sendingaddress": "1ARjWDkZ7kT9fwjPrjcQyvbXDkEySzKHwu",
                    "starttime": 1398154020,
                    "subcategory": "MaidSafe",
                    "tokensissued": "452552412",
                    "tokensperunit": "3400",
                    "totaltokens": "452552412",
                    "txid": "86f214055a7f4f5057922fd1647e00ef31ab0a3ff15217f8b90e295f051873a7",
                    "type": "Create Property - Variable",
                    "type_int": 51,
                    "url": "www.buysafecoins.com",
                    "valid": true,
                    "version": 0
                }
        ],
            "status": "OK"
        }

#### 10.返回当前活动的众包列表。 数据：生态系统：1用于生产/主网生态系统。 2用于测试/ 开发生态系统

接口名字：https://api.omniexplorer.info/v1/properties/listactivecrowdsales
请求方式：POST
调用示例：
* curl方式调用

        curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -H "Content-Type: application/x-www-form-urlencoded" -d "ecosystem=1" "https://api.omniexplorer.info/v1/properties/listactivecrowdsales"

* http方式调用说明

        POST /v1/properties/listactivecrowdsales HTTP/1.1
        Host: api.omniexplorer.info
        Content-Type: application/x-www-form-urlencoded
        Content-Type: application/x-www-form-urlencoded

        ecosystem=1

返回结果：

        {
            "crowdsales": [
                {
                    "active": true,
                    "amountraised": "300000000",
                    "category": "Proof of Stake",
                    "creationtxid": "b01d1594a7e2083ebcd428706045df003f290c4dc7bd6d77c93df9fcca68232f",
                    "data": "Introducing ProzCoin which utilizes the new Proof of Action algorithm.  PoA pays users for article submission, moderating forums, interaction and participation.  PoA adjusts to pay out higher rates for higher quality content based on user ratings.",
                    "deadline": 1406967060000,
                    "divisible": true,
                    "earlybonus": 0,
                    "fixedissuance": false,
                    "issuer": "1PRozi3UhpXtC4kZtPD1nfCFXJkXrV27Wp",
                    "name": "ProzCoin",
                    "percenttoissuer": 100,
                    "propertyid": 28,
                    "propertyiddesired": 24,
                    "propertyiddesiredname": "BitStrapAccessToken",
                    "starttime": 1406925999,
                    "subcategory": "Proof of Action",
                    "tokensissued": "600000000.00000000",
                    "tokensperunit": "100000000.00000000",
                    "totaltokens": "600000000.00000000",
                    "url": "http://Coinproz.com/Coinpage.html?alt=proz"
                },
                {
                    "additional records": "..."
                }
            ],
            "status": "OK"
        }


#### 10返回由生态系统过滤的已创建属性列表。 数据：生态系统：1用于生产/主网生态系统。 2用于测试/开发生态系统


接口名字：https://api.omniexplorer.info/v1/properties/listbyecosystem
请求方式：GET
调用示例：
* curl方式调用

        curl -X GET -H "Content-Type: application/x-www-form-urlencoded" "https://api.omniexplorer.info/v1/properties/list"

* http方式调用说明

        POST /v1/properties/listbyecosystem HTTP/1.1
        Host: api.omniexplorer.info
        Content-Type: application/x-www-form-urlencoded
        Content-Type: application/x-www-form-urlencoded

        ecosystem=1
        
返回结果：

        {
            "properties": [
                {
                    "blocktime": 1377994675,
                    "category": "N/A",
                    "creationtxid": "0000000000000000000000000000000000000000000000000000000000000000",
                    "data": "Omni serve as the binding between Bitcoin, smart properties and contracts created on the Omni Layer.",
                    "divisible": true,
                    "fixedissuance": false,
                    "issuer": "1EXoDusjGwvnjZUyKkxZ4UHEf77z6A5S4P",
                    "managedissuance": false,
                    "name": "Omni",
                    "propertyid": 1,
                    "subcategory": "N/A",
                    "totaltokens": "617211.68177584",
                    "url": "http://www.omnilayer.org"
                },
                {
                    "additional records": "..."
                }
            ],
            "status": "OK"
        }

#### 11.返回所有已创建属性的列表。

接口名字：https://api.omniexplorer.info/v1/properties/list
请求方式：GET
调用示例：
* curl方式调用

        curl -X GET -H "Content-Type: application/x-www-form-urlencoded" "https://api.omniexplorer.info/v1/properties/list"

* http方式调用说明

        GET /v1/properties/list HTTP/1.1
        Host: api.omniexplorer.info
        Content-Type: application/x-www-form-urlencoded

返回结果：

        {
            "properties": [
                {
                    "blocktime": 1377994675,
                    "category": "N/A",
                    "creationtxid": "0000000000000000000000000000000000000000000000000000000000000000",
                    "data": "Omni serve as the binding between Bitcoin, smart properties and contracts created on the Omni Layer.",
                    "divisible": true,
                    "fixedissuance": false,
                    "issuer": "1EXoDusjGwvnjZUyKkxZ4UHEf77z6A5S4P",
                    "managedissuance": false,
                    "name": "Omni",
                    "propertyid": 1,
                    "subcategory": "N/A",
                    "totaltokens": "617211.68177584",
                    "url": "http://www.omnilayer.org"
                },
                {
                    "blocktime": 1377994675,
                    "category": "N/A",
                    "creationtxid": "0000000000000000000000000000000000000000000000000000000000000000",
                    "data": "Test Omni serve as the binding between Bitcoin, smart properties and contracts created on the Omni Layer.",
                    "divisible": true,
                    "fixedissuance": false,
                    "issuer": "1EXoDusjGwvnjZUyKkxZ4UHEf77z6A5S4P",
                    "managedissuance": false,
                    "name": "Test Omni",
                    "propertyid": 2,
                    "subcategory": "N/A",
                    "totaltokens": "563162.35759628",
                    "url": "http://www.omnilayer.org"
                },
                {
                    "additional records": "..."
                }
            ],
            "status": "OK"
        }

#### 12.按事务ID，地址或属性ID搜索。 数据：查询：要搜索的交易ID，地址或属性ID的文本字符串

接口名字：https://api.omniexplorer.info/v1/search
请求方式：POST
调用示例：
* curl方式调用

        curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -H "Content-Type: application/x-www-form-urlencoded" -d "query=3" "https://api.omniexplorer.info/v1/search"

* http方式调用说明

        POST /v1/search HTTP/1.1
        Host: api.omniexplorer.info
        Content-Type: application/x-www-form-urlencoded
        Content-Type: application/x-www-form-urlencoded

        query=3


返回结果：

        {
            "data": {
                "address": {},
                "asset": [
                    [
                        3,
                        "MaidSafeCoin",
                        "1ARjWDkZ7kT9fwjPrjcQyvbXDkEySzKHwu"
                    ]
                ],
                "tx": {}
            },
            "query": "3",
            "status": 200
        }

#### 13.返回查询地址的交易列表。 数据：地址：查询页面的地址：循环浏览可用的响应页面（每页10个tx）

接口名字：https://api.omniexplorer.info/v1/transaction/address
请求方式：POST
调用示例：
* curl方式调用

        curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -H "Content-Type: application/x-www-form-urlencoded" -d "addr=1EXoDusjGwvnjZUyKkxZ4UHEf77z6A5S4P&page=0" "https://api.omniexplorer.info/v1/transaction/address"


* http方式调用说明

        POST /v1/transaction/address HTTP/1.1
        Host: api.omniexplorer.info
        Content-Type: application/x-www-form-urlencoded
        Content-Type: application/x-www-form-urlencoded

        addr=1EXoDusjGwvnjZUyKkxZ4UHEf77z6A5S4P&page=0


返回结果：

        {
            "address": "1EXoDusjGwvnjZUyKkxZ4UHEf77z6A5S4P",
            "pages": 3,
            "transactions": [
                {
                    "amount": "0.00000100",
                    "block": 342733,
                    "blockhash": "000000000000000014a6858e7da93606ffe624331a2f295732c8c9499ffa1025",
                    "blocktime": 1423503818,
                    "confirmations": 176424,
                    "divisible": true,
                    "fee": "0.00001000",
                    "ismine": false,
                    "positioninblock": 1569,
                    "propertyid": 1,
                    "propertyname": "Omni",
                    "sendingaddress": "1F9jCeixNbK6GEkdQFq1ejkbJjAGwVVVqG",
                    "txid": "318fb016144f3dadb16acf39cd7e00089b808418f1c4b343926af55882824966",
                    "type": "Send To Owners",
                    "type_int": 3,
                    "valid": true,
                    "version": 0
                },
                {
                    "additional records": "..."
                }
            ]
        }

#### 14.将已签名的事务广播到网络。 数据：signedTransaction：已签名的十六进制广播

接口名字：https://api.omniexplorer.info/v1/transaction/pushtx
请求方式：POST
调用示例：
* curl方式调用

        curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -H "Content-Type: application/x-www-form-urlencoded" -d "signedTransaction=01000000010b7ad3946c6fcd15d39fd4312c4f8dfd68c51a61071c2575cfb0722c4403eef1000000008a47304402206fe4eabf81cc4c6c3cee3dedc6ea9be46bb685c1b61da577bb9aead8188b80b702201404284c52ad2e857bd3931fb0e1646575d1e72c93b2cdcd80a7e0c7bee1c191014104ad90e5b6bc86b3ec7fac2c5fbda7423fc8ef0d58df594c773fa05e2c281b2bfe877677c668bd13603944e34f4818ee03cadd81a88542b8b4d5431264180e2c28ffffffff0470170000000000001976a9143b0000000000000001000000009d828d9600000088ac70170000000000001976a9143c791cc99255509d85788e2bf0e0f6e8b389b3cf88ac40355d5f000000001976a914946cb2e08075bcbaf157e47bcb67eb2b2339d24288ac70170000000000001976a914946cb2e08075bcbaf157e47bcb67eb2b2339d24288ac00000000" "https://api.omniexplorer.info/v1/transaction/pushtx/"

* http方式调用说明

        POST /v1/transaction/pushtx/ HTTP/1.1
        Host: api.omniexplorer.info
        Content-Type: application/x-www-form-urlencoded
        Content-Type: application/x-www-form-urlencoded

        signedTransaction=01000000010b7ad3946c6fcd15d39fd4312c4f8dfd68c51a61071c2575cfb0722c4403eef1000000008a47304402206fe4eabf81cc4c6c3cee3dedc6ea9be46bb685c1b61da577bb9aead8188b80b702201404284c52ad2e857bd3931fb0e1646575d1e72c93b2cdcd80a7e0c7bee1c191014104ad90e5b6bc86b3ec7fac2c5fbda7423fc8ef0d58df594c773fa05e2c281b2bfe877677c668bd13603944e34f4818ee03cadd81a88542b8b4d5431264180e2c28ffffffff0470170000000000001976a9143b0000000000000001000000009d828d9600000088ac70170000000000001976a9143c791cc99255509d85788e2bf0e0f6e8b389b3cf88ac40355d5f000000001976a914946cb2e08075bcbaf157e47bcb67eb2b2339d24288ac70170000000000001976a914946cb2e08075bcbaf157e47bcb67eb2b2339d24288ac00000000


返回结果：

        {
            "status": "OK",
            "pushed": "Success",
            "tx": "9975dcd1f574fe8356183e34eb435df6aa5620e7f9fea98a3c2349a10383a62b"
        }

#### 15.返回查询的事务哈希的事务详细信息

接口名字：https://api.omniexplorer.info/v1/transaction/tx
请求方式：GET
调用示例：
* curl方式调用

        curl -X GET -H "Content-Type: application/x-www-form-urlencoded" "https://api.omniexplorer.info/v1/transaction/tx/e0e3749f4855c341b5139cdcbb4c6b492fcc09c49021b8b15462872b4ba69d1b"

* http方式调用说明

        GET /v1/transaction/tx/e0e3749f4855c341b5139cdcbb4c6b492fcc09c49021b8b15462872b4ba69d1b HTTP/1.1
        Host: api.omniexplorer.info
        Content-Type: application/x-www-form-urlencoded

返回结果：

        {
            "amount": "6167.00000000",
            "block": 511660,
            "blockhash": "0000000000000000003f37e72e599fbdaa14396a2e9251e493f0d7d15b1fd915",
            "blocktime": 1520009505,
            "confirmations": 7499,
            "divisible": true,
            "fee": "0.00009124",
            "ismine": false,
            "positioninblock": 825,
            "propertyid": 31,
            "propertyname": "TetherUS",
            "referenceaddress": "3GyeFJmQynJWd8DeACm4cdEnZcckAtrfcN",
            "sendingaddress": "3D4r9ERiM3HSc4eC4EhcT31tXoSV96HsPg",
            "txid": "e0e3749f4855c341b5139cdcbb4c6b492fcc09c49021b8b15462872b4ba69d1b",
            "type": "Simple Send",
            "type_int": 0,
            "valid": true,
            "version": 0
        }


## 三.钱包开发

### 1.使用钱包节点进行转账开发

如果你的地址不是在节点上创建的，那么你需要导入地址，才能查找UTXO。如果是在节点上创建的地址，转账发生在节点上的，直接获取UTXO就行。

#### 1.1.导入地址

    ./omnicore-cli importaddress 1C6UYrmbtvdi8dHZNnZD3YoVwit2mccSgw

#### 1.2.列出UTXO

需要导入地址才能获取UTXO的前提条件是，上面的地址已经扫描完所有的块并导入。

    ./omnicore-cli "listunspent" 0 999999 '["1C6UYrmbtvdi8dHZNnZD3YoVwit2mccSgw"]'
    
下面是查询的结果

    [
      {
        "txid": "4f5b2b5ac6f7402f2edd8d4dafbdcca71758076d13f9b58dccd02ad76ac0f2ce",
        "vout": 0,
        "address": "1C6UYrmbtvdi8dHZNnZD3YoVwit2mccSgw",
        "account": "",
        "scriptPubKey": "76a91479b275dd5f136c241f3c28549b4c338177d5cb2188ac",
        "amount": 0.01411554,
        "confirmations": 105,
        "spendable": false,
        "solvable": false
      }
    ]


#### 1.3.确定要转出的USDT的数量

    ./omnicore-cli omni_createpayload_simplesend 31 5.0

上面代码中31表示USDT在Omni上的代币ID为31，和以太坊的ERC20代币Token类似，2.0代表的是要转出2个USDT。

执行结果如下：

    000000000000001f000000001dcd6500

#### 1.4.创建交易

这个和比特币是一样的，代码如下：

    ./omnicore-cli "createrawtransaction" '[{"txid":"4f5b2b5ac6f7402f2edd8d4dafbdcca71758076d13f9b58dccd02ad76ac0f2ce","vout":0}]' '{}'

执行结果如下：
    
    0100000001cef2c06ad72ad0cc8db5f9136d075817a7ccbdaf4d8ddd2e2f40f7c65a2b5b4f0000000000ffffffff0000000000
        

#### 1.5.在交易上绑定代币数据

    ./omnicore-cli omni_createrawtx_opreturn 0100000001cef2c06ad72ad0cc8db5f9136d075817a7ccbdaf4d8ddd2e2f40f7c65a2b5b4f0000000000ffffffff0000000000 000000000000001f000000001dcd6500
    
执行结果如下：
    0100000001cef2c06ad72ad0cc8db5f9136d075817a7ccbdaf4d8ddd2e2f40f7c65a2b5b4f0000000000ffffffff010000000000000000166a146f6d6e69000000000000001f000000001dcd650000000000

#### 1.6.添加接收币的地址

    ./omnicore-cli omni_createrawtx_reference 0100000001cef2c06ad72ad0cc8db5f9136d075817a7ccbdaf4d8ddd2e2f40f7c65a2b5b4f0000000000ffffffff010000000000000000166a146f6d6e69000000000000001f000000001dcd650000000000 1DefiYRCAD4wVS7rXwFkqhEn6R88EkSUnh
    
执行结果
            
    0100000001cef2c06ad72ad0cc8db5f9136d075817a7ccbdaf4d8ddd2e2f40f7c65a2b5b4f0000000000ffffffff020000000000000000166a146f6d6e69000000000000001f000000001dcd650022020000000000001976a9148ac137fb413490ec6f69792552c5f5ddf2ecd4cf88ac00000000
    
#### 1.6.设置找零和矿工费

指令：（事务HASH，交易信息，找零地址，手续费）

    ./omnicore-cli omni_createrawtx_change 0100000001cef2c06ad72ad0cc8db5f9136d075817a7ccbdaf4d8ddd2e2f40f7c65a2b5b4f0000000000ffffffff020000000000000000166a146f6d6e69000000000000001f000000001dcd650022020000000000001976a9148ac137fb413490ec6f69792552c5f5ddf2ecd4cf88ac00000000  '[{"txid":"4f5b2b5ac6f7402f2edd8d4dafbdcca71758076d13f9b58dccd02ad76ac0f2ce","vout":0,"scriptPubKey":"76a91479b275dd5f136c241f3c28549b4c338177d5cb2188ac","value":0.01411554}]' "1C6UYrmbtvdi8dHZNnZD3YoVwit2mccSgw" 0.0002
    
执行结果：   

    0100000001cef2c06ad72ad0cc8db5f9136d075817a7ccbdaf4d8ddd2e2f40f7c65a2b5b4f0000000000ffffffff03a0391500000000001976a91479b275dd5f136c241f3c28549b4c338177d5cb2188ac0000000000000000166a146f6d6e69000000000000001f000000001dcd650022020000000000001976a9148ac137fb413490ec6f69792552c5f5ddf2ecd4cf88ac00000000

#### 1.7.对交易进行签名

签名指令：

    ./omnicore-cli signrawtransaction 0100000001cef2c06ad72ad0cc8db5f9136d075817a7ccbdaf4d8ddd2e2f40f7c65a2b5b4f0000000000ffffffff03a0391500000000001976a91479b275dd5f136c241f3c28549b4c338177d5cb2188ac0000000000000000166a146f6d6e69000000000000001f000000001dcd650022020000000000001976a9148ac137fb413490ec6f69792552c5f5ddf2ecd4cf88ac00000000
    
执行结果：

    {
      "hex": "0100000001cef2c06ad72ad0cc8db5f9136d075817a7ccbdaf4d8ddd2e2f40f7c65a2b5b4f0000000000ffffffff03a0391500000000001976a91479b275dd5f136c241f3c28549b4c338177d5cb2188ac0000000000000000166a146f6d6e69000000000000001f000000001dcd650022020000000000001976a9148ac137fb413490ec6f69792552c5f5ddf2ecd4cf88ac00000000",
      "complete": false,
      "errors": [
        {
          "txid": "4f5b2b5ac6f7402f2edd8d4dafbdcca71758076d13f9b58dccd02ad76ac0f2ce",
          "vout": 0,
          "scriptSig": "",
          "sequence": 4294967295,
          "error": "Operation not valid with the current stack size"
        }
      ]
    }

出现上面这种错误的原因，是因为你的地址不是在节点上生成的，而是通过导入地址的方式导入的，这种情况下，你需要在签名的时候加上私钥。

如下：

    ./omnicore-cli signrawtransaction 0100000001cef2c06ad72ad0cc8db5f9136d075817a7ccbdaf4d8ddd2e2f40f7c65a2b5b4f0000000000ffffffff03a0391500000000001976a91479b275dd5f136c241f3c28549b4c338177d5cb2188ac0000000000000000166a146f6d6e69000000000000001f000000001dcd650022020000000000001976a9148ac137fb413490ec6f69792552c5f5ddf2ecd4cf88ac00000000 '[]' '["私钥"]'

执行结果如下：

    {
      "hex": "签名串",
      "complete": true
    }

#### 1.7.发送交易到区块链网络

指令如下：

    ./omnicore-cli sendrawtransaction 签名串
    
返回结果：

    2ae377b5d928132910ffa4419d52913c34d90baec3ed53fc621a07a9bcfb2bcf


### 2.NodeJS离线签名开发USDT钱包

签名代码：

    function addPreZero(num){
        var t = (num+'').length,
            s = '';
        for(var i=0; i<16-t; i++){
            s += '0';
        }
        return s+num;
    }

    function usdtSign(privateKey, utxo, feeValue, usdtValue, fromAddress, toAddress) {
        var txb = new bitcoin.TransactionBuilder();
        var set = bitcoin.ECPair.fromWIF(privateKey);
        const fundValue = 546;
        var usdtAmount = parseInt(usdtValue*1e8).toString(16);
        var totalUnspent = 0;
        for(var i = 0; i < utxo.length; i++){
            totalUnspent = totalUnspent + utxo[i].value;
        }
        const changeValue = totalUnspent - fundValue - (feeValue*1e8);
        if (totalUnspent < feeValue + fundValue) {
            console.log("Total less than fee");
            return constant.LessValue;
        }
        for(var i = 0; i< utxo.length; i++){
            txb.addInput(utxo[i].tx_hash_big_endian, utxo[i].tx_output_n, 0xfffffffe);
        }
        const usdtInfo = [
            "6f6d6e69",
            "0000",
            "00000000001f",
            addPreZero(usdtAmount)
        ].join('');
        const data = Buffer.from(usdtInfo, "hex");
        const omniOutput = bitcoin.script.compile([
            bitcoin.opcodes.OP_RETURN,
            data
        ]);
        txb.addOutput(toAddress, fundValue);
        txb.addOutput(omniOutput, 0);
        txb.addOutput(fromAddress, changeValue);
        for(var i = 0;i < utxo.length; i++){
            txb.sign(0, set);
        }
        return txb.buildIncomplete().toHex();
    };

上面代码中UXTO的获取方式和比特币的一样。

案列代码：

    var utxo ={
        "unspent_outputs":[
            {
                "tx_hash":"1bf1e457ac7572518cde36945e94728659dfae7fb2229411c1e13c085054c506",
                "tx_hash_big_endian":"06c55450083ce1c1119422b27faedf598672945e9436de8c517275ac57e4f11b",
                "tx_index":399167492,
                "tx_output_n": 2,
                "script":"76a91479b275dd5f136c241f3c28549b4c338177d5cb2188ac",
                "value": 1363462,
                "value_hex": "14ce06",
                "confirmations":0
            }
        ]
    }

    var privateKey = "私钥";
    var usdtValue = 2;
    var feeValue = 0.0002;
    var fromAddress = "1C6UYrmbtvdi8dHZNnZD3YoVwit2mccSgw";
    var toAddress = "1DefiYRCAD4wVS7rXwFkqhEn6R88EkSUnh";
    var sign = usdtSign(privateKey, utxo.unspent_outputs, feeValue, usdtValue, fromAddress, toAddress);
    console.log(sign);

签名成功后调用Omni区块链浏览器接口`https://api.omniexplorer.info/v1/transaction/pushtx`将其发到区块链网络即可





