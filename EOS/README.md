
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

### 1.EOS合约开发入门

在前面创建的合约目录中创建一个名为“hello”的新目录

    cd /root/eosio/contracts
    mkdir hello
    cd hello

创建一个`hello.cpp`的c++代码文件，然后用你最喜欢的编译器打开

下面分别包含两个头文件，eosio.hpp和print.hpp文件。 eosio.hpp文件包含编写合同所需的库。以下是这些必需库的说明：

* action.hpp：为查询和发送操作定义类型安全的C ++包装器。
* contract.hpp：EOSIO合约的基类。
* print.hpp：用于记录/打印文本消息的C++包装器。
* multi_index.hpp：EOSIO Multi-Index API为EOSIO数据库提供C++接口。
* dispatcher.hpp：定义C++函数以将操作分派给合同中的正确操作处理程序。

使用eosio命名空间可以减少代码中的混乱。 例如，通过设置使用命名空间eosio;，可以写eosio :: print（“foo”）print（“foo”）

eosiolib/eosio.hpp将EOSIO C和C++ API加载到合约范围内，这是您的新版本。

创建一个标准的C++11类。合同类需要扩展eosio :: contract。

    #include <eosiolib/eosio.hpp>

    using namespace eosio;

    class hello : public contract {};

空合约没有多大帮助。 添加公共访问说明符和using声明。 using声明将允许我们编写更简洁的代码。

    #include <eosiolib/eosio.hpp>

    using namespace eosio;

    class [[eosio::contract("hello")]] hello : public contract {
      public:
          using contract::contract;
    };

这份合同需要做点什么。 本着hello world的精神，编写一个接受“name”参数的动作，然后输出该参数。

动作实现合同的行为。 合同操作之间的通信有两种形式，内联操作（将在与调用操作相同的事务中执行）和延迟操作（将在稍后执行）。

    #include <eosiolib/eosio.hpp>

    using namespace eosio;

    class [[eosio::contract("hello")]] hello : public contract {
      public:
          using contract::contract;

          [[eosio::action]]
          void hi( name user ) {
             print( "Hello, ", name{user});
          }
    };

上面的操作接受一个名为user的参数，它是一个名称类型。 EOSIO附带了许多typedef，你会遇到的最常见的typedef之一是name。使用先前包含的eosio :: print库，连接字符串并打印用户参数。使用名称{user}的支撑初始化使用户参数可打印。

因此，eosio.cdt中的abi生成器将不知道没有属性的hi（）动作。在动作上方添加C++ 11样式属性，这样abi生成器可以产生更可靠的输出。

    #include <eosiolib/eosio.hpp>

    using namespace eosio;

    class [[eosio::contract("hello")]] hello : public contract {
      public:
          using contract::contract;

          [[eosio::action]]
          void hi( name user ) {
             print( "Hello, ", user);
          }
    };

    EOSIO_DISPATCH( hello, (hi))

最后，添加EOSIO_DISPATCH宏来处理调度hello契约的操作。

    EOSIO_DISPATCH( hello, (hi))

一切都在一起，这是完成的hello world合约

    #include <eosiolib/eosio.hpp>

    using namespace eosio;

    class [[eosio::contract("hello")]] hello : public contract {
      public:
          using contract::contract;

          [[eosio::action]]
          void hi( name user ) {
             print( "Hello, ", user);
          }
    };

    EOSIO_DISPATCH( hello, (hi))

您可以将代码编译为Web程序集（.wasm），如下所示：

    eosio-cpp -o hello.wasm hello.cpp --abigen

部署合约时，会将其部署到帐户，该帐户将成为合约的接口。 如前所述，这些教程对所有帐户使用相同的公钥，以保持简单。

    cleos wallet keys

使用cleos create account创建合同帐户，使用下面提供的命令。

    cleos create account eosio hello EOS6ia9oVJpCvUzMrgAyMdbhrYu5YBfbuohvfUJxwvqh8e4JaBtJH -p eosio@active

使用cleos set contract将已编译的wasm广播到区块链。

在前面的步骤中，您应该创建一个`contracts`目录并获取绝对路径，然后将其保存到cookie中。 将以下命令中的“CONTRACTS_DIR”替换为`contracts`目录的绝对路径。

    cleos set contract hello /root/eosio/contracts/hello -p hello@active

完美儿，现在合约已经设定，执行下面的命令

    cleos push action hello hi '["bob"]' -p bob@active

执行结果如下：

    executed transaction: 4c10c1426c16b1656e802f3302677594731b380b18a44851d38e8b5275072857  244 bytes  1000 cycles
    #    hello.code <= hello.code::hi               {"user":"bob"}
    >> Hello, bob

如上所述，合约将允许任何帐户向任何用户打招呼

    cleos push action hello hi '["bob"]' -p alice@active

执行结果：

    executed transaction: 28d92256c8ffd8b0255be324e4596b7c745f50f85722d0c4400471bc184b9a16  244 bytes  1000 cycles
    #    hello.code <= hello.code::hi               {"user":"bob"}
    >> Hello, bob

正如预期的那样，控制台输出是“Hello，bob”

在这种情况下，“alice”是授权它的人，用户只是一个参数。修改合约，以便授权用户在这种情况下“alice”必须与合约响应“hi”的用户相同。使用require_auth方法。此方法将名称作为参数，并将检查执行操作的用户是否与提供的参数匹配并具有适当的授权。

    void hi( name user ) {
       require_auth( user );
       print( "Hello, ", name{user} );
    }

重新编译合约代码

    eosio-cpp -o hello.wasm hello.cpp --abigen

然后更新一下它

    cleos set contract hello /root/eosio/contracts/hello -p hello@active

尝试再次执行操作，但这次授权不匹配。

    cleos push action hello hi '["bob"]' -p alice@active

正如预期的那样，require_auth暂停了事务并抛出了错误。

    Error 3090004: Missing required authority
    Ensure that you have the related authority inside your transaction!;
    If you are currently using 'cleos push action' command, try to add the [relevant](**http://google.com**) authority using -p option.

现在，通过我们的更改，合同将验证提供的名称用户是否与授权用户相同。 再试一次，但这一次，拥有“alice”帐户的权限。

    cleos push action hello hi '["alice"]' -p alice@active

执行结果

    executed transaction: 235bd766c2097f4a698cfb948eb2e709532df8d18458b92c9c6aae74ed8e4518  244 bytes  1000 cycles
    #    hello <= hello::hi               {"user":"alice"}
    >> Hello, alice

### 2.部署，发布和token转账

EOS官方开源了一份转账的合约代码，我们只需要把这份合约代码搞明白了，就可以基于这份合约代码根据自己的需求进行合约代码的修改就可以了。

#### 2.1.拉取合约代码

进入前面已经创建好的合约目录

    cd /root/eosio/contracts

拉取源码

    git clone https://github.com/EOSIO/eosio.contracts --branch v1.4.0 --single-branch

这个存储库包含几个契约，但它现在是重要的eosio.token契约。 进入到该目录。

    cd eosio.contracts/eosio.token

#### 2.2.为合约创建一个用户

在我们部署token合约之前，我们必须创建一个帐户来部署它，我们将使用该帐户的eosio开发密钥。当然，在使用前你应该先解锁钱包

    cleos create account eosio eosio.token EOS6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV

#### 2.3.编译合约代码

    eosio-cpp -I include -o eosio.token.wasm src/eosio.token.cpp --abigen

#### 2.4.部署token合约

    cleos set contract eosio.token /root/eosio/contracts/eosio.contracts/eosio.token --abi eosio.token.abi -p eosio.token@active

下面是执行结果

    Reading WASM from ...
    Publishing contract...
    executed transaction: 69c68b1bd5d61a0cc146b11e89e11f02527f24e4b240731c4003ad1dc0c87c2c  9696 bytes  6290 us
    #         eosio <= eosio::setcode               {"account":"eosio.token","vmtype":0,"vmversion":0,"code":"0061736d0100000001aa011c60037f7e7f0060047f...
    #         eosio <= eosio::setabi                {"account":"eosio.token","abi":"0e656f73696f3a3a6162692f312e30000605636c6f73650002056f776e6572046e61...
    warning: transaction executed locally, but may not be confirmed by the network yet         ]

#### 2.5.创建一个token

要创建新Token，调用create（...）操作。 此操作接受1个参数，它是一个symbol_name类型，由两个数据组成，最大供应float和仅带大写字母字符的symbol_name，例如“1.0000 SYM”。 发行人将有权发出问题或执行其他操作，例如冻结，召回和列入所有者白名单。

下面是使用位置参数调用此方法的简明方法：

    cleos push action eosio.token create '[ "eosio", "1000000000.0000 SYS"]' -p eosio.token@active

执行结果

    executed transaction: 0e49a421f6e75f4c5e09dd738a02d3f51bd18a0cf31894f68d335cd70d9c0e12  120 bytes  1000 cycles
    #   eosio.token <= eosio.token::create          {"issuer":"eosio","maximum_supply":"1000000000.0000 SYS"}

另一种方法使用命名参数：

    cleos push action eosio.token create '{"issuer":"eosio", "maximum_supply":"1000000000.0000 SYS"}' -p eosio.token@active

执行结果：

    executed transaction: 0e49a421f6e75f4c5e09dd738a02d3f51bd18a0cf31894f68d335cd70d9c0e12  120 bytes  1000 cycles
    #   eosio.token <= eosio.token::create          {"issuer":"eosio","maximum_supply":"1000000000.0000 SYS"}

此命令创建了一个新的令牌SYS，其精度为4位小数，最大供应量为1000000000.0000 SYS。 要创建此令牌，需要获得eosio.token合约的许可。 因此，传递了-p eosio.token@active以授权该请求。

#### 2.6.发布token

发行者可以向之前创建的“alice”帐户发放新令牌。

    cleos push action eosio.token issue '[ "alice", "100.0000 SYS", "memo" ]' -p eosio@active

执行结果如下：

    executed transaction: 822a607a9196112831ecc2dc14ffb1722634f1749f3ac18b73ffacd41160b019  268 bytes  1000 cycles
    #   eosio.token <= eosio.token::issue           {"to":"user","quantity":"100.0000 SYS","memo":"memo"}
    >> issue
    #   eosio.token <= eosio.token::transfer        {"from":"eosio","to":"user","quantity":"100.0000 SYS","memo":"memo"}
    >> transfer
    #         eosio <= eosio.token::transfer        {"from":"eosio","to":"user","quantity":"100.0000 SYS","memo":"memo"}
    #          user <= eosio.token::transfer        {"from":"eosio","to":"user","quantity":"100.0000 SYS","memo":"memo"}


这次输出包含几个不同的操作：一个问题和三个传输。虽然签署的唯一操作是问题，但问题操作执行“内联传输”，“内联传输”通知发件人和收件人帐户。输出指示调用的所有操作处理程序，调用它们的顺序以及操作是否生成任何输出。

从技术上讲，eosio.token合同可能会跳过内联转移，并选择直接修改余额。但是，在这种情况下，eosio.token合同遵循我们的Token约定，该约定要求所有帐户余额可以通过引用它们的传输操作的总和来推导。它还要求通知资金的发送方和接收方，以便它们可以自动处理存款和取款。

要检查事务，请尝试使用-d -j选项，它们表示“不要广播”和“将事务返回为json”，在开发过程中您可能会发现这些选项很有用。

    cleos push action eosio.token issue '["alice", "100.0000 SYS", "memo"]' -p eosio@active -d -j

#### 2.7.转移token

现在该帐户已经发出了令牌，将其中一些转移到帐户bob。 之前曾表示alice使用参数-p alice @ active授权此操作。

    cleos push action eosio.token transfer '[ "alice", "bob", "25.0000 SYS", "m" ]' -p alice@active

执行结果如下：

    executed transaction: 06d0a99652c11637230d08a207520bf38066b8817ef7cafaab2f0344aafd7018  268 bytes  1000 cycles
    #   eosio.token <= eosio.token::transfer        {"from":"alice","to":"bob","quantity":"25.0000 SYS","memo":"Here you go bob!"}
    >> transfer
    #          user <= eosio.token::transfer        {"from":"alice","to":"bob","quantity":"25.0000 SYS","memo":"Here you go bob!"}
    #        tester <= eosio.token::transfer        {"from":"alice","to":"bob","quantity":"25.0000 SYS","memo":"Here you go bob!"}

现在检查“bob”是否使用cleos获得Token以获得货币余额

    cleos get currency balance eosio.token bob SYS

结果如下：

    25.00 SYS
    
检查“alice”的余额，注意从帐户中扣除了Token

    cleos get currency balance eosio.token alice SYS

结果如下：

    75.00 SYS

## 五.钱包相关的API

### 1.根据给出的名字创建一个钱包的接口

#### 1.1.curl方式调用

    curl --request POST \
      --url http://127.0.0.1:8888/v1/wallet/create \
      --header 'content-type: application/x-www-form-urlencoded; charset=UTF-8'
    
#### 1.2.NodeJs方式调用

    var request = require("request");

    var options = { method: 'POST',
      url: 'http://127.0.0.1:8888/v1/wallet/create',
      headers: { 'content-type': 'application/x-www-form-urlencoded; charset=UTF-8' } };

    request(options, function (error, response, body) {
      if (error) throw new Error(error);

      console.log(body);
    });


#### 1.3.Ruby方式调用

    require 'uri'
    require 'net/http'

    url = URI("http://127.0.0.1:8888/v1/wallet/create")

    http = Net::HTTP.new(url.host, url.port)

    request = Net::HTTP::Post.new(url)
    request["content-type"] = 'application/x-www-form-urlencoded; charset=UTF-8'

    response = http.request(request)
    puts response.read_body


#### 1.4.javaScript方式调用

    var data = null;

    var xhr = new XMLHttpRequest();

    xhr.addEventListener("readystatechange", function () {
      if (this.readyState === this.DONE) {
        console.log(this.responseText);
      }
    });

    xhr.open("POST", "http://127.0.0.1:8888/v1/wallet/create");
    xhr.setRequestHeader("content-type", "application/x-www-form-urlencoded; charset=UTF-8");

    xhr.send(data);

#### 1.5.python方式调用

    import requests

    url = "http://127.0.0.1:8888/v1/wallet/create"

    headers = {'content-type': 'application/x-www-form-urlencoded; charset=UTF-8'}

    response = requests.request("POST", url, headers=headers)

    print(response.text)

返回结果都一样，返回结果如下

    "PW5KdHAJNyHGvVQfZMebawZrCxUrXoG8wrz55R5EHcfmvjSASuDay"


