
# 第七章：EOS钱包开发

## 一.什么是EOS

EOSIO是一个它开源的区块链项目，旨在实现分散式应用程序的垂直和水平扩展（“EOSIO软件”），并可用于启动私有和公共区块链网络。 这是通过类似操作系统的构造实现的，可以在其上构建应用程序。 该软件提供帐户，身份验证，数据库，异步通信以及跨多个CPU核心和/或群集的应用程序调度。 由此产生的技术是一种区块链架构，有可能每秒扩展到数百万个事务，注意，只是有可能扩展到数百万个事物。消除了用户费用，并允许快速轻松地部署分散式应用程序。

在安装钱包节点之前，咱们应该先了解一下EOS的架构，下面是EOS的一个模块架构。


.： 
    ![.： 
](https://github.com/guoshijiang/blockchain-wallet/blob/master/img/EOS1.png)

* nodeos（node + eos = nodeos）-可以使用插件配置以运行节点的核心EOSIO节点守护程序。 示例用法是块生产，专用API端点和本地开发。

* cleos（cli + eos = cleos）-与区块链交互并管理钱包的命令行界面

* keosd（key + eos = keosd）-将EOSIO密钥安全存储在钱包中的组件。

* eosio-cpp - eosio.cdt的一部分，它将C++代码编译为WASM并可以生成ABI


## 二.安装EOS钱包节点

下面咱们使用二进制文件来构建项目，从源码来构建项目的话，这种操作方式比较复杂，而且很浪费时间，这里不推荐这种方式。

### 1.各种平台上安装EOS

#### 1.1.在mac平台下安装EOS

    brew tap eosio/eosio
    brew install eosio

#### 1.2.Ubuntu18.04

    wget https://github.com/eosio/eos/releases/download/v1.5.0/eosio_1.5.0-1-ubuntu-18.04_amd64.deb
    sudo apt install ./eosio_1.5.0-1-ubuntu-18.04_amd64.deb

#### 1.3.Ubuntu16.04

    wget https://github.com/eosio/eos/releases/download/v1.5.0/eosio_1.5.0-1-ubuntu-16.04_amd64.deb
    sudo apt install ./eosio_1.5.0-1-ubuntu-16.04_amd64.deb

#### 1.4.CentOS

    wget https://github.com/eosio/eos/releases/download/v1.5.0/eosio-1.5.0-1.el7.x86_64.rpm
    sudo yum install ./eosio-1.5.0-1.el7.x86_64.rpm

#### 1.5.Fedora

    wget https://github.com/eosio/eos/releases/download/v1.5.0/eosio-1.5.0-1.fc27.x86_64.rpm
    sudo yum install ./eosio-1.5.0-1.fc27.x86_64.rpm


### 2.启动节点并对节点进行配置

#### 2.1.启动Keosd

    keosd &

执行上面这个命令之后，你应该可以看到类似下面这样的输出，按Enter键可以退出来。

    info  2018-11-26T06:54:24.789 thread-0  wallet_plugin.cpp:42          plugin_initialize    ] initializing wallet plugin
    info  2018-11-26T06:54:24.795 thread-0  http_plugin.cpp:554           add_handler          ] add api url: /v1/keosd/stop
    info  2018-11-26T06:54:24.796 thread-0  wallet_api_plugin.cpp:73      plugin_startup       ] starting wallet_api_plugin
    info  2018-11-26T06:54:24.796 thread-0  http_plugin.cpp:554           add_handler          ] add api url: /v1/wallet/create
    info  2018-11-26T06:54:24.796 thread-0  http_plugin.cpp:554           add_handler          ] add api url: /v1/wallet/create_key
    info  2018-11-26T06:54:24.796 thread-0  http_plugin.cpp:554           add_handler          ] add api url: /v1/wallet/get_public_keys


### 2.2.启动nodeos

    nodeos -e -p eosio \
    --plugin eosio::producer_plugin \
    --plugin eosio::chain_api_plugin \
    --plugin eosio::http_plugin \
    --plugin eosio::history_plugin \
    --plugin eosio::history_api_plugin \
    --access-control-allow-origin='*' \
    --contracts-console \
    --http-validate-host=false \
    --verbose-http-errors \
    --filter-on='*' >> nodeos.log 2>&1 &

这些设置可实现以下功能：

* 在开发目录下的eosio目录中使用工作目录进行区块链数据和配置。这里我们分别使用eosio/data和eosio/config

* 运行Nodeos。此命令加载所有基本插件，设置服务器地址，启用CORS并添加一些合同调试和日志记录。

* 无限制地启用CORS（*）

在上面的配置中，CORS仅用于开发目的*，您永远不应在可公开访问的节点上启用CORS for *！

### 3.检查节点的和钱包的情况

#### 3.1.检查节点的情况

运行下面的命令

    tail -f nodeos.log

你可以看到终端下面有这些输入

    1929001ms thread-0   producer_plugin.cpp:585       block_production_loo ] Produced block 0000366974ce4e2a... #13929 @ 2018-05-23T16:32:09.000 signed by eosio [trxs: 0, lib: 13928, confirmed: 0]
    1929502ms thread-0   producer_plugin.cpp:585       block_production_loo ] Produced block 0000366aea085023... #13930 @ 2018-05-23T16:32:09.500 signed by eosio [trxs: 0, lib: 13929, confirmed: 0]
    1930002ms thread-0   producer_plugin.cpp:585       block_production_loo ] Produced block 0000366b7f074fdd... #13931 @ 2018-05-23T16:32:10.000 signed by eosio [trxs: 0, lib: 13930, confirmed: 0]
    1930501ms thread-0   producer_plugin.cpp:585       block_production_loo ] Produced block 0000366cd8222adb... #13932 @ 2018-05-23T16:32:10.500 signed by eosio [trxs: 0, lib: 13931, confirmed: 0]
    1931002ms thread-0   producer_plugin.cpp:585       block_production_loo ] Produced block 0000366d5c1ec38d... #13933 @ 2018-05-23T16:32:11.000 signed by eosio [trxs: 0, lib: 13932, confirmed: 0]
    1931501ms thread-0   producer_plugin.cpp:585       block_production_loo ] Produced block 0000366e45c1f235... #13934 @ 2018-05-23T16:32:11.500 signed by eosio [trxs: 0, lib: 13933, confirmed: 0]
    1932001ms thread-0   producer_plugin.cpp:585       block_production_loo ] Produced block 0000366f98adb324... #13935 @ 2018-05-23T16:32:12.000 signed by eosio [trxs: 0, lib: 13934, confirmed: 0]
    1932501ms thread-0   producer_plugin.cpp:585       block_production_loo ] Produced block 00003670a0f01daa... #13936 @ 2018-05-23T16:32:12.500 signed by eosio [trxs: 0, lib: 13935, confirmed: 0]
    1933001ms thread-0   producer_plugin.cpp:585       block_production_loo ] Produced block 00003671e8b36e1e... #13937 @ 2018-05-23T16:32:13.000 signed by eosio [trxs: 0, lib: 13936, confirmed: 0]
    1933501ms thread-0   producer_plugin.cpp:585       block_production_loo ] Produced block 0000367257fe1623... #13938 @ 2018-05-23T16:32:13.500 signed by eosio [trxs: 0, lib: 13937, confirmed: 0]

按CNTL + C键退出日志。

#### 3.2.检查钱包的情况

打开shell并运行cleos命令列出可用的钱包

    cleos wallet list

你应该看到下面这样的输出

    Wallets:
    []

#### 3.3.检查Nodeos末端节点

这将检查RPC API是否正常工作，选择一个。

检查浏览器中chain_api_plugin提供的get_info端点：

    http：// localhost：8888 / v1 / chain / get_info
    
检查相同的事情，但在主机的控制台中

    curl http://localhost:8888/v1/chain/get_info


## 三.使用命令行创建一个开发钱包

钱包是公钥-私钥对的存储库。 需要私钥来签署区块链上执行的操作。 钱包可以使用cleos访问。

### 1.创建钱包

第一步是创建一个钱包。 使用cleos wallet create创建一个新的“默认”钱包，使用选项--to-console以简化。 如果在生产中使用cleo，最好使用--to-file，这样你的钱包密码就不在你的bash历史中了。 出于开发目的，因为这些是开发而非生产密钥 - 控制台不会构成安全威胁。

    cleos wallet create --to-console
    
cleos将返回密码，需要将密码存在其他地方，以供后面需要的时候使用

    Creating wallet: default
    Save password to use in the future to unlock this wallet.
    Without password imported keys will not be retrievable.
    "PW5Kewn9L76X8Fpd....................t42S9XCw2"

### 2.打开钱包

默认情况下，在启动keosd服务钱包是关闭，开启钱包请运行下面的命令

    cleos wallet open

获取钱包的列表请执行下面的命令

    cleos wallet list
    
获取钱包列表返回的数据结构

    Wallets:
    [
      "default"
    ]

### 3.解锁钱包

keosd钱包已经打开，但仍然被锁定。 你创建钱包的时候，您已经获得了密码，现在就需要密码了。，执行下面的命令解锁钱包

    cleos wallet unlock

上面的命令会提示您输入密码，你直接把上面生成的密码复制过来粘贴上去就可以了。

### 4.查看钱包是否解锁

执行`cleos wallet list`命令之后返回的钱包后面带有星号（`*`）, 这意味着钱包目前已解锁,如下：

    Wallets:
    [
      "default *"   
    ]

### 5.给钱包导入密钥

生成一个私钥，cleos有一个函数生成私钥的函数，只需运行以下命令即可。

    cleos wallet create_key

执行上面的命令将会返回下面的结果

    Created new private key with a public key of: "EOS8PEJ5FM42xLpHK...X6PymQu97KrGDJQY5Y"


### 6.引入开发的密钥

每个新的EOSIO链都有一个名为“eosio”的默认“系统”用户。 此帐户用于通过加载系统合同来设置链，该合约规定了EOSIO链的治理和共识。 每个新的EOSIO链都带有一个开发密钥，这个密钥是相同的。 加载此密钥以代表系统用户（eosio）对事务进行签名。

    cleos wallet import

系统将提示您输入私钥，输入下面提供的eosio开发密钥

    5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3

*注意：上面的密钥是我生成的密钥，此处你应该填写的是自己生成的密钥。切勿将开发密钥用于生产帐户！ 这样做肯定会导致您无法访问您的帐户，此私钥是公开的。

### 7.创建一个测试账户

#### 7.1.什么是EOS的账户

帐户是一组授权，存储在区块链中，用于标识发件人/收件人。它具有灵活的授权结构，使其可以由个人或一组个人拥有，具体取决于如何配置权限。需要一个帐户才能向区块链发送或接收有效的交易。

#### 7.2.创建一个测试账户

使用了bob和alice做为账户，使用cleos create account创建两个账户。

    cleos create account eosio bob EOS6ia9oVJpCvUzMrgAyMdbhrYu5YBfbuohvfUJxwvqh8e4JaBtJH 
    cleos create account eosio alice EOS6ia9oVJpCvUzMrgAyMdbhrYu5YBfbuohvfUJxwvqh8e4JaBtJH

然后，您应该看到确认消息已被广播的每个命令的类似于以下内容的确认消息。

    executed transaction: 40c605006de...  200 bytes  153 us
    #         eosio <= eosio::newaccount            {"creator":"eosio","name":"alice","owner":{"threshold":1,"keys":[{"key":"EOS5rti4LTL53xptjgQBXv9HxyU...
    warning: transaction executed locally, but may not be confirmed by the network yet    ]

#### 7.3.公钥

请注意，在cleos命令中，公钥与帐户alice相关联。每个EOSIO帐户都与公钥相关联。

请注意，帐户名称是所有权的唯一标识符。您可以更改公钥，但不会更改您的EOSIO帐户的所有权。

使用cleos get帐户检查哪个公钥与alice相关联

    cleos get account alice

你将看到下面的这种返回

    permissions:
         owner     1:    1 EOS6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV
            active     1:    1 EOS6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV
    memory:
         quota:       unlimited  used:      2.66 KiB

    net bandwidth:
         used:               unlimited
         available:          unlimited
         limit:              unlimited

    cpu bandwidth:
         used:               unlimited
         available:          unlimited
         limit:              unlimited


*请注意，实际上alice同时拥有所有者和活动公钥。 EOSIO具有独特的授权结构，可为您的帐户增加安全性。在使用与您的活动权限相关联的密钥时，您可以通过保持所有者密钥冷却来最小化帐户的风险。这样，如果您的有效密钥遭到入侵，您可以使用所有者密钥重新控制您的帐户。


## 四.使用合约方式开发转账的过程







