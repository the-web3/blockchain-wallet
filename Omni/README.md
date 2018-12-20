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


## 二.钱包开发

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
        "txid": "974e148acb093a654309bd976fd989e3a565835147b21b8de8cbda97b457995a",
        "vout": 0,
        "address": "1C6UYrmbtvdi8dHZNnZD3YoVwit2mccSgw",
        "account": "",
        "scriptPubKey": "76a91479b275dd5f136c241f3c28549b4c338177d5cb2188ac",
        "amount": 0.01300000,
        "confirmations": 2677,
        "spendable": false,
        "solvable": false
      },
      {
        "txid": "25a91d219fe3bd736f60c56f6886a4e96bb3863f181e727657bbc7e1bd407b71",
        "vout": 0,
        "address": "1C6UYrmbtvdi8dHZNnZD3YoVwit2mccSgw",
        "account": "",
        "scriptPubKey": "76a91479b275dd5f136c241f3c28549b4c338177d5cb2188ac",
        "amount": 0.00022008,
        "confirmations": 2676,
        "spendable": false,
        "solvable": false
      },
      {
        "txid": "570d3afd9814de0b44b479ad2df597f76c8e798d1e983d199312e0da092fefc2",
        "vout": 0,
        "address": "1C6UYrmbtvdi8dHZNnZD3YoVwit2mccSgw",
        "account": "",
        "scriptPubKey": "76a91479b275dd5f136c241f3c28549b4c338177d5cb2188ac",
        "amount": 0.00000546,
        "confirmations": 1709,
        "spendable": false,
        "solvable": false
      },
      {
        "txid": "b43dbcb4d7ead95ab59a3193b97bc30e97c0ea292d415c048076b46e0cd5c1cb",
        "vout": 0,
        "address": "1C6UYrmbtvdi8dHZNnZD3YoVwit2mccSgw",
        "account": "",
        "scriptPubKey": "76a91479b275dd5f136c241f3c28549b4c338177d5cb2188ac",
        "amount": 0.00149000,
        "confirmations": 2677,
        "spendable": false,
        "solvable": false
      }
    ]

#### 1.3.确定要转出的USDT的数量

    ./omnicore-cli omni_createpayload_simplesend 31 2.0

上面代码中31表示USDT在Omni上的代币ID为31，和以太坊的ERC20代币Token类似，2.0代表的是要转出2个USDT。

执行结果如下：

    000000000000001f000000000bebc200

#### 1.4.创建交易

这个和比特币是一样的，代码如下：




