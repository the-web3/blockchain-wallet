
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

