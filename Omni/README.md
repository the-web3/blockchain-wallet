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


## 二.钱包开发

