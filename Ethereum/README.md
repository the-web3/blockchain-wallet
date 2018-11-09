
# 第五章：以太坊钱包开发

目前的公链项目，影响力最大的应该就数以太坊和比特币了，其他的多数公链，基本上都是借鉴了以太坊和比特币公链项目而设计开发的。了解区块链的人都知道，比特币和以太坊这两个公链项目的差距还是挺大的，故而他们的钱包开发也是特别不一样的。本章我们将详细讲解以太坊钱包原理和开发流程，涉及到的内容有以下这些：

* 依托钱包节点方式开发钱包，但这种方式的缺点就keystore生成存放到区块的节点上；

* 非确定性以太坊钱包开发，实现本地存储私钥，但每个账户对应一个私钥，私钥的管理比较难；

* 分层确定性以太坊钱包开发流程，实现本地存储，实现多链多账户和私钥关联性钱包。


## 一.以太坊简介

### 1.什么是以太坊

以太坊是一个开放的区块链平台，任何人都可以使用区块链技术构建和使用分散的应用程序。 像比特币一样，没有人控制或拥有以太坊，它是由世界各地的许多人建立的开源项目。 但与比特币协议不同，以太坊的设计具有适应性和灵活性。 在以太坊平台上创建新应用程序很容易，任何人都可以安全地使用这些应用程序。基于以太坊的一套生态也越来越强大，目前在以太坊上发Token的公司已经有将近800家了，质量比较高的数字资产占Token量的20%以上，基于以太的数字货币资产的发展直接导致了进入的资金越来越庞大。

### 2.以太坊产生的背景

区块链技术是比特币的技术基础，首先由其神秘作者中本聪（Satoshi Nakamoto）在2008年出版的白皮书“比特币：点对点电子现金系统”中描述。虽然区块链用于更广泛的用途已经是在原始论文中讨论过，直到几年后，区块链技术才成为一个通用术语。区块链是一种分布式计算架构，其中每个网络节点执行并记录相同的事务，这些事务被分组为块。一次只能添加一个块，并且每个块都包含一个数学证明，用于验证它是否依次跟随前一个块。通过这种方式，区块链的“分布式数据库”在整个网络中保持一致。个人用户与分类帐（交易）的交互由强密码术保护。维护和验证网络的节点受到编码到协议中的数学强制经济激励的激励。

在比特币的情况下，分布式数据库被设想为账户余额表，分类账，交易是比特币代币的转移，以促进个人之间的无信任融资。但随着比特币开始引起开发人员和技术人员的更多关注，新的项目开始将比特币网络用于除了价值代币转移之外的其他目的。其中许多采取“替代硬币”的形式 - 单独的区块链与他们自己的加密货币，改进了原有的比特币协议，以增加新的功能或功能。 2013年底，以太坊的发明者Vitalik Buterin提出，一个能够重新编程以执行任意复杂计算的区块链可以包含这些许多其他项目。

2014年，以太坊创始人Vitalik Buterin，Gavin Wood和Jeffrey Wilcke开始研究下一代区块链，该区块链有望实施一个完全无信任的通用智能合约平台。

### 3.以太坊虚拟机

以太坊是一个可编程的区块链。 Ethereum不是为用户提供一组预定义的操作（例如比特币交易），而是允许用户创建他们希望的任何复杂度的自己的操作。通过这种方式，它可以作为许多不同类型的分散式区块链应用程序的平台，包括但不限于加密货币。

狭义上的以太坊是指为分散应用程序定义平台的一套协议。其核心是以太坊虚拟机（“EVM”），它可以执行任意算法复杂度的代码。在计算机科学术语中，以太坊是“图灵完整”。开发人员可以使用以JavaScript和Python等现有语言为模型的友好编程语言创建在EVM上运行的应用程序。

与任何区块链一样，以太坊也包括点对点网络协议。以太坊区块链数据库由连接到网络的许多节点维护和更新。网络的每个节点都运行EVM并执行相同的指令。出于这个原因，以太坊有时被描述为“世界计算机”。

在整个以太坊网络中进行大规模的计算并行化并不是为了提高计算效率。实际上，这个过程使得以太坊上的计算比传统的“计算机”慢得多，也更昂贵。相反，每个以太坊节点都运行EVM，以便在区块链中保持一致。分散的共识为以太坊提供了极端的容错能力，确保零停机时间，并使存储在区块链上的数据永远不变且具有审查能力。

以太坊平台本身没有特色或与价值无关。与编程语言类似，由企业家和开发人员决定应该使用什么。但是，很明显某些应用程序类型比以太坊的功能更有益。具体而言，以太坊适用于自动化同伴之间直接交互或促进跨网络协调的群组行动的应用程序。例如，用于协调点对点市场的应用程序，或复杂金融合同的自动化。比特币允许个人交换现金而不涉及金融机构，银行或政府等任何中间人。以太坊的影响可能更为深远。从理论上讲，任何复杂性的金融交互或交换都可以使用在以太坊上运行的代码自动可靠地执行。除了金融应用程序之外，任何信任，安全性和持久性都很重要的环境（例如，资产注册，投票，治理和物联网）都可能受到以太坊平台的严重影响。

### 4.以太坊工作机制

以太坊融合了比特币用户所熟悉的许多功能和技术，同时还引入了许多自己的修改和创新。

虽然比特币区块链纯粹是一个交易清单，但以太坊的基本单位是账户。以太坊区块链跟踪每个账户的状态，以太坊区块链上的所有状态转换都是账户之间价值和信息的转移。有两种类型的帐户：

* 外部拥有帐户（EOA），由私钥控制
* 合约账户，由合约代码控制，只能由EOA“激活”

对于大多数用户而言，这些用户之间的基本区别在于人类用户控制EOA，因为他们可以控制控制EOA的私钥。另一方面，合同账户由其内部代码管理。如果它们被人类用户“控制”，那是因为它们被编程为由具有特定地址的EOA控制，该地址又由持有控制该EOA的私钥的任何人控制。流行的术语“智能合约”是指合约账户中的代码 - 在将交易发送到该账户时执行的程序。用户可以通过将代码部署到区块链来创建新合同。

合同账户仅在EOA指示时执行操作。因此，合同帐户不可能执行本机操作（如随机数生成或API调用），只有在EOA提示时才能执行这些操作。这是因为以太坊要求节点能够就计算结果达成一致，这需要保证严格确定性的执行。

与比特币一样，用户必须向网络支付小额交易费用。这可以保护以太坊区块链免受无聊或恶意的计算任务，如DDoS攻击或无限循环。事务的发送者必须为他们激活的“程序”的每一步付费，包括计算和内存存储。这些费用以以太坊的本地价值标记，以太币的金额支付。

这些交易费用由验证网络的节点收集。这些“矿工”是以太坊网络中接收，传播，验证和执行交易的节点。然后矿工将交易分组，其中包括对以太坊区块链中账户“状态”的许多更新，进入所谓的“区块”，然后矿工互相竞争，使他们的区块成为下一个要添加的区块。区块链。对于他们开采的每个成功区块，矿工都会获得以太奖励。这为人们将以太网和电力专用于以太坊网络提供了经济激励。

正如在比特币网络中一样，矿工的任务是解决复杂的数学问题，以便成功“挖掘”一个区块。这被称为“工作证明”。任何计算问题都需要数量级更多的资源才能在算法上解决，而不是验证解决方案，这是一个很好的候选工作证明。为了阻止由于使用专用硬件（例如ASIC）而导致的集中化，如比特币网络中所发生的那样，以太坊选择了存储器硬计算问题。如果问题需要内存和CPU，理想的硬件实际上是通用计算机。这使得以太坊的工作证明具有ASIC抗性，允许比采用比特币等专用硬件占主导地位的区块链更安全地分散安全性。

## 二.以太坊钱包涉及到的术语

### 1.gas,Gas Limit和Gas Price

在以太坊上，发送代币或调用智能合约，在区块链上执行写入操作，需要支付矿工计算费用，计费是按照Gas计算的，Gas使用ETH来支付。无论您的调用的方法是成功还是失败，都需要支付计算费用。即使失败，矿工也验证并执行您的交易（计算），因此必须和成功交易一样支付矿工费。

简单的说Gas Limit相当于汽车需要加多少汽油，Gas Price相当于每升汽油的价格。

Gas Limit之所以称为限额，因为它是您愿意在一笔交易中花费Gas的最大数量。交易所需的Gas是通过调用智能合约执行多少代码来定义。 如果您不想花太多的Gas，通过降低Gas Limit将不会有太大的帮助。 因为您必须包括足够的Gas来支付的计算资源，否则由于Gas不够导致交易失败，您所设置的所有Gas limit也将消耗光。建议您在有充足ETH情况下，将Gas Limit尽量设高，所有未使用的Gas将在转账结束时退还给您。 

通过降低Gas Price可以节省矿工费用，但是也会减慢矿工打包的速度。矿工会优先打包 Gas Price设置高的交易，如果您想加快转账，您可以把Gas Price设置得更高，这样您就可以排队靠前。如果您不急，您只需要设置一个安全的 Gas Price，矿工也会打包您的交易。

查看矿工可以接受的最低Gas Price：https://etherscan.io/gastracker

.： 
    ![.： 
](https://github.com/guoshijiang/blockchain-wallet/blob/master/img/gas.png)


### 2.以太币单位

以太坊最小的单位是wei,最常用的单位是ether

* Kwei（Babbage）= 10的3次方Wei
* Mwei（Lovelace）= 10的6次方Wei
* Gwei（Shannon）= 10的9次方Wei
* MicroEther（Szabo）= 10的12次方Wei
* MilliEther（Finney）= 10的15次方Wei
* Ether = 10的18次方Wei

### 3.keystore

keystore是以太坊账户的一种表现形式，里面包含了以太坊账户的地址，账户密文的私钥和MAC地址等等一系列的信息，下面是一个keystore的完整信息。

    {
      "address":"2ffe6e3816b6a84f509ea3be1b8e8bb024c894d8",
      "crypto":
      {
        "cipher":"aes-128-ctr",
        "ciphertext":"c302e468ad640ad6c43d51754caa60964ece820ad98ad0d5aa72a785a93d7a59",
        "cipherparams": {"iv":"f4609a583b28e48a6bda7b6bf1229a26"},
        "mac":"ef8872a3ad0a92a410b15b0e2e662d5cbfc98360d72b190a7b3189bd4151ebcf",
        "kdf":"pbkdf2",
        "kdfparams":
        {
          "c":262144,
          "dklen":32,
          "prf":"hmac-sha256",
          "salt":"5a757ae33c08a46c8a50f34a2a514503fd66481ea56aeab31e25da45ae3f1c39"
        }
     },
     "id":"49a53e88-4f8a-4858-80a4-02ad230da1d3",
     "version":3
    }

### 4.矿工费

转账或调用智能合约，在区块链上执行写入操作，需要支付矿工计算费用，计费是按照Gas计算的，Gas使用ETH来支付，详细内容请看上面 1 部分

## 三.以太坊钱包涉及到的开源库

### 1.keythereum

Keythereum是一个用于生成，导入和导出以太坊密钥的JavaScript工具。 这提供了一种在本地和Web钱包中使用同一帐户的简单方法。 它可用于可验证和存储钱包。

Keythereum使用相同的密钥派生函数（PBKDF2-SHA256或scrypt），对称密码（AES-128-CTR或AES-128-CBC）和消息验证代码作为geth。 您可以将生成的密钥导出到文件，将其复制到数据目录的密钥库，然后立即开始在您的本地以太坊客户端中使用它。

从版本0.5.0开始，keythereum的加密和解密函数都返回Buffers而不是字符串。 对于直接使用这些功能的人来说，这是一个重大改变。

### 1.1.使用keythereum生产keystore

在生产keystore之前，你必须有一个nodeJs的环境，并且安装keythereum

    npm install keythereum
    
或者使用压缩的浏览器文件dist/keythereum.min.js，以便在浏览器中使用。 使用一下代码引入

    <script src="dist/keythereum.min.js" type="text/javascript"></script>

生成一个新的随机私钥（256位），以及密钥派生函数使用的salt（256位），用于AES-128-CTR的初始化向量（128位）对密钥进行加密。 如果传递回调函数，则create是异步的，否则是同步的。

下面是生成keystore的代码：

    var keythereum = require("keythereum");

    var params = { keyBytes: 32, ivBytes: 16 };
    var dk = keythereum.create(params);
    keythereum.create(params, function (dk) {
        var password = "wheethereum";
        var kdf = "pbkdf2";
        var options = {
            kdf: "pbkdf2",
            cipher: "aes-128-ctr",
            kdfparams: {
                c: 262144,
                dklen: 32,
                prf: "hmac-sha256"
            }
        };
        keythereum.dump(password, dk.privateKey, dk.salt, dk.iv, options, function (keyObject) {
            console.log(keyObject);
        });
    });

下图是运行结果：

.： 
    ![.： 
](https://github.com/guoshijiang/blockchain-wallet/blob/master/img/keystoreone.png)

### 1.2.将keystore到文件中存储

dump创建一个对象而不是JSON字符串。 在Node中，exportToFile方法提供了一种将此格式化的密钥对象导出到文件的简便方法。 它在keystore子目录中创建一个JSON文件，并使用geth的当前文件命名约定（ISO时间戳与密钥派生的以太坊地址连接）。

代码如下：

    var keythereum = require("keythereum");

    var params = { keyBytes: 32, ivBytes: 16 };
    var dk = keythereum.create(params);
    keythereum.create(params, function (dk) {
        var password = "wheethereum";
        var kdf = "pbkdf2";
        var options = {
            kdf: "pbkdf2",
            cipher: "aes-128-ctr",
            kdfparams: {
                c: 262144,
                dklen: 32,
                prf: "hmac-sha256"
            }
        };
        keythereum.dump(password, dk.privateKey, dk.salt, dk.iv, options, function (keyObject) {
            keythereum.exportToFile(keyObject);
        });
    });

成功之后在你的keystor目录下将看到下面这些信息，如果出现错误，最可能的原因就是你的目录下没有keystore这个目录，当然以上代码中你也可以指定keystore的存储目录

.： 
    ![.： 
](https://github.com/guoshijiang/blockchain-wallet/blob/master/img/1541492232(1).png)


### 1.3.keystore的导入

从geth的密钥库导入密钥只能在Node上完成。 将JSON文件解析为与上面的keyObject具有相同结构的对象。

    var datadir = "/home/jack/.ethereum-test";
    var keyObject = keythereum.importFromFile(address, datadir);
    console.log(keyObject)
    keythereum.importFromFile(address, datadir, function (keyObject) {
       console.log(keyObject)
    });

### 1.4.从keystore中恢复私钥

这里恢复出来的私钥是buffer格式的,password是你设置的密码，keyObject就是keystore

    var privateKey = keythereum.recover(password, keyObject);
    console.log(privateKey)
    keythereum.recover(password, keyObject, function (privateKey) {
      console.log(privateKey)
    });

### 2.ethereumjs-tx

ethereumjs-tx是用来对以太坊或者ERC20代币的交易进行交易签名的javascript库.

### 2.1.安装ethereumjs-tx

    npm install ethereumjs-tx
   
### 2.2.使用ethereumjs-tx库对交易签名

#### 2.2.1.以太坊交易签名

    const Web3 =require ('web3')
    const transaction = require('ethereumjs-tx');
    if (typeof web3 !== 'undefined')
    {
        var web3 = new Web3(web3.currentProvider);
    } else {
        var web3 = new Web3(new Web3.providers.HttpProvider("http://localhost:8545"));
    }

    function ethereumSign(privateKey, nonce, toAddress, sendToBalance, sendFee) {
        if(!privateKey || !nonce || !toAddress || !sendToBalance || !sendFee) {
            console.log("one of fromAddress, toAddress, sendToBalance, sendFee is null, please give a valid param");
        } else {
            console.log("param is valid, start sign transaction");
            var transactionNonce = parseInt(nonce).toString(16);
            console.log("transaction nonce is " + transactionNonce);
            var numBalance = parseFloat(sendToBalance);
            var balancetoWei = web3.toWei(numBalance, "ether");
            console.log("balancetoWei is " + balancetoWei);
            var oxNumBalance = parseInt(balancetoWei).toString(16);
            console.log("16 oxNumBalance is " + oxNumBalance);
            var gasFee = parseFloat(sendFee);
            var gasFeeToWei = web3.toWei(gasFee, "ether");
            console.log("gas fee to wei is " + gasFeeToWei);
            var gas = gasFeeToWei / 21000;
            console.log("param gas is " + gas);
            var oxgasFeeToWei = parseInt(gas).toString(16);
            console.log("16 oxgasFeeToWei is " + oxgasFeeToWei);
            var privateKeyBuffer =  Buffer.from(privateKey, 'hex');
            var rawTx = {
                nonce:'0x' + transactionNonce,
                gasPrice: '0x5208',
                gas:'0x4a817c800', //+ oxgasFeeToWei,
                to: '0x' + toAddress,
                value:'0x' + oxNumBalance,
                data: '',
                chainId: 1
            };
            var tx = new transaction(rawTx);
            tx.sign(privateKeyBuffer);
            var serializedTx = tx.serialize();
            if(serializedTx == null) {
                console.log("Serialized transaction fail")
            } else {
                console.log("Serialized transaction success and the result is " + serializedTx.toString('hex'));
                console.log("The sender address is " + tx.getSenderAddress().toString('hex'));
                if (tx.verifySignature()) {
                    console.log('Signature Checks out!')
                } else {
                    console.log("Signature checks fail")
                }
            }
        }
        return '0x' + serializedTx.toString('hex')
    }

    var privateKey = "5204627f8e58b3c75be408423c121988a1cb942e73470fb19186ef8986cd50b3";
    var nonce = 10;
    var toAddress = "c6328b3a137b3be3f01c35ecda4ecda375be7fdf";
    var sendToBalance = "0.003";
    var sendFee = "0.001";

    var sign = ethereumSign(privateKey, nonce, toAddress, sendToBalance, sendFee);
    console.log(sign);
    
签名结果如下：

.： 
    ![.： 
](https://github.com/guoshijiang/blockchain-wallet/blob/master/img/ethSign.png)

#### 2.2.2.ERC20代币交易签名

    const Web3 =require ('web3')
    const transaction = require('ethereumjs-tx');

    function addPreZero(num){
        var t = (num+'').length,
            s = '';
        for(var i=0; i<64-t; i++){
            s += '0';
        }
        return s+num;
    }

    function ethereumErc20CoinSign(privateKey/*私钥*/, nonce/*标识交易*/, currentAccount/*当前账户*/,  contractAddress/*合约地址*/, toAddress/*转入地址*/,  gasPrice/*gasPrice*/,  gasLimit/*gasLimit*/, totalAmount/*代币总量*/ , decimal /*代币的单位换算*/) {
        if(!privateKey || !nonce || !currentAccount || !contractAddress || !toAddress  || !gasPrice || !gasLimit || !totalAmount || !decimal) {
            console.log("one of param is null, please give a valid param");
            return ;
        }
        var transactionNonce = parseInt(nonce).toString(16);
        console.log("transaction nonce is " + transactionNonce);
        var gasLimit = parseInt(gasLimit).toString(16);
        console.log("send transaction gasLimit is " + gasLimit);
        var gasPrice = parseFloat(gasPrice).toString(16);
        console.log("send transaction gasPrice is " + gasPrice)
        var totx = parseFloat(totalAmount*(10**decimal)).toString(16); //
        var txData = {
            nonce: '0x'+ transactionNonce,
            gasLimit: '0x' + gasLimit,
            gasPrice: '0x' +gasPrice,
            to: contractAddress,
            from: currentAccount,
            value: '0x00',
            data: '0x' + 'a9059cbb' + addPreZero(toAddress) + addPreZero(totx)
        }
        var tx = new transaction(txData);
        const privateKey1 = new Buffer(privateKey, 'hex');
        tx.sign(privateKey1);
        var serializedTx = tx.serialize().toString('hex');
        // console.log("transaction sign result is " + serializedTx);
        return serializedTx; /*签名串*/
    }

    var privateKey = "5204627f8e58b3c75be408423c121988a1cb942e73470fb19186ef8986cd50b3";
    var nonce = "9";
    var currentAccount = "0xe558be4e90b2ac96ae5cad47dc39cd08316f2e57";
    var contractAddress = "0xfa3118b34522580c35ae27f6cf52da1dbb756288";
    var toAddress = "c6328b3a137b3be3f01c35ecda4ecda375be7fdf";
    var gasPrice = 400000000;
    var gasLimit = 200000;
    var totalAmount = 8;
    var decimal = 6;
    var sign = ethereumErc20CoinSign(privateKey, nonce, currentAccount, contractAddress, toAddress, gasPrice, gasLimit, totalAmount, decimal);

    console.log(sign);

签名结果如下：

.： 
    ![.： 
](https://github.com/guoshijiang/blockchain-wallet/blob/master/img/ERC20Sign.png)


### 2.web3库

web3是以太坊开发的重要库，目前有web3.js、web3j(web3java)、web3.py(web3python)、hs-web3(haskell)、web3j-scala、purescript-web3、web3.php、ethereum-php；咱们此处主要介绍web3.js，其他的后面将用一个章节来进行介绍。web3.js是与以太坊兼容的JavaScript API，它实现了通用JSON RPC规范。 它在npm上可用作节点模块，Bower和组件可用作可嵌入脚本，也可用作meteor.js包。

#### 2.1.web3.js库的安装

##### 2.1.1.nodejs安装

    npm install web3
    
##### 2.1.2.yarn安装

    yarn add web3
    
##### 2.1.3.Meteor.js安装

    meteor add ethereum:web3
 
##### 2.1.4.做为浏览器模块

* CDN

    <script src="https://cdn.jsdelivr.net/gh/ethereum/web3.js/dist/web3.min.js"></script>

* Bower

    bower install web3
    
* Component

    component install ethereum/web3.js
    
#### 2.2.web3.js的使用

##### 2.2.1.使用web3和以太坊交互

web3.js初始化设置Provider

    var Web3 = require("web3");
    
    if (typeof web3 !== 'undefined')
    {
        web3 = new Web3(web3.currentProvider);
    }
    else
    {
        web3 = new Web3(new Web3.providers.HttpProvider("http://10.23.1.209:8545"));
    }

##### 2.2.2.获取账户余额

    web3.eth.getBalance("0x68db18a9cd87403c39e84467b332195b43fc33b5", function(err, result)
    {
        if (err == null)
        {
            console.log('~balance Ether:' +web3.fromWei(result, "ether"));
        }
        else
        {
            console.log('~balance Ether:' + web3.fromWei(result, "ether"));
        }
    });

##### 2.2.3.获取交易的nonce

    web3.eth.getTransactionCount("0x68db18a9cd87403c39e84467b332195b43fc33b5", function (err, result)
    {
        if (err == null)
        {
            console.log('nonce:' + result);
        }
        else
        {
            console.log('nonce:' + result);
        }
    });

##### 2.2.4.发起转账

转账需要使用到ethereumjs-tx库，对交易进行签名，关于ethereumjs-tx库，请参阅上面的内容

    var Tx = require('ethereumjs-tx');
    var privateKey = new Buffer.from('0f8e7b1b99f49d1d94ac42084216a95fe5967caec6bba35c62c911f6c4eafa95', 'hex')

    var rawTx = {
        subId:'0x0000000000000000000000000000000000',
        nonce: '0x1',
        gasPrice: '0x1a13b8600',
        gas: '0xf4240',
        to: '0x82aeb528664bb153d2114ae7ca4ef118ef1e7a98',
        value: '0x8ac7230489e80000',
        data:'0xd46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f072445675',
        //chainId:"10"
    };

    var tx = new Tx(rawTx);
    tx.sign(privateKey);

    var serializedTx = tx.serialize();

    if (tx.verifySignature())
    {
        console.log('Signature Checks out!')
    }

    web3.eth.sendRawTransaction('0x' + serializedTx.toString('hex'), function(err, hash) {
        if (!err)
        {
            console.log("hash:" + hash); // "0x7f9fade1c0d57a7af66ab4ead79fade1c0d57a7af66ab4ead7c2c2eb7b11a91385"
        }
        else
        {
            console.log(err);
        }
    });

以上是Web3js的使用，web3是一个强大SDK，他的功能远远不止这些。后期的文章中还会用到web3.js，用到的时候我们再对相应的内容进行讲解。

## 四.以太坊浏览器API介绍（钱包开发中需要用到其中一些API）

Etherscan的API地址：https://etherscan.io/apis

### 1.简介

Etherscan的以太坊开发者API是社区毫无保留的提供的服务，你可以在上面使用你需要的接口。 这些接口支持的请求方式是GET/POST请求，并且每秒只能发送5次请求。要使用API服务，请在ClientPortal-> MyApiKey区域内创建一个免费的Api-Key令牌，然后您可以将其用于所有api请求。

### 2.Etherscan账户体系API

* 获取单个地址的账户余额

        https://api.etherscan.io/api?module=account&action=balance&address=0xddbd2b932c763ba5b1b7ae3b362eac3e8d40121a&tag=latest&apikey=YourApiKeyToken

* 一次调用获取多个地址的账户余额

        https://api.etherscan.io/api?module=account&action=balancemulti&address=0xddbd2b932c763ba5b1b7ae3b362eac3e8d40121a,0x63a9975ba31b0b9626b34300f7f627147df1f526,0x198ef1ec325a96cc354c7266a038be8b5c558f67&tag=latest&apikey=YourApiKeyToken
    
用逗号分隔地址，一次最多可包含20个帐户
    
* 根据地址获取账户交易的列表

参数：startblock：起始区块，根据起始区块去检索结果；endblock：结束区块，根据结束的区块去检索结果

        http://api.etherscan.io/api?module=account&action=txlist&address=0xddbd2b932c763ba5b1b7ae3b362eac3e8d40121a&startblock=0&endblock=99999999&sort=asc&apikey=YourApiKeyToken

（[BETA]返回'isError'值：0 =无错误，1 =得到错误）（最多返回最后10000个交易）

或者

        https://api.etherscan.io/api?module=account&action=txlist&address=0xddbd2b932c763ba5b1b7ae3b362eac3e8d40121a&startblock=0&endblock=99999999&page=1&offset=10&sort=asc&apikey=YourApiKeyToken

（要获取分页结果，请使用page =<页码>和offset =<返回最大记录数>）

* 根据地址获取合约交易列表

参数：startblock：起始区块，根据起始区块去检索结果；endblock：结束区块，根据结束的区块去检索结果

        http://api.etherscan.io/api?module=account&action=txlistinternal&address=0x2c1ba59d6f58433fb1eaee7d20b26ed83bda51a3&startblock=0&endblock=2702578&sort=asc&apikey=YourApiKeyToken
        
（[BETA]返回'isError'值：0 =无错误，1 =得到错误）（最多返回最后10000个交易）

或者

        https://api.etherscan.io/api?module=account&action=txlistinternal&address=0x2c1ba59d6f58433fb1eaee7d20b26ed83bda51a3&startblock=0&endblock=2702578&page=1&offset=10&sort=asc&apikey=YourApiKeyToken

（要获取分页结果，请使用page =<页码>和offset =<返回最大记录数>）

* 根据交易Hash获取内部交易

        https://api.etherscan.io/api?module=account&action=txlistinternal&txhash=0x40eb908387324f2b575b4879cd9d7188f69c8fc9d87c901b9e2daaea4b442170&apikey=YourApiKeyToken
        
返回'isError'值：0=OK，1 =拒绝/取消
仅返回最后10000个交易

* 通过地址获取“ERC20-Token转移事件”列表

可选择参数：startblock: 根据起始区块号检索结果, endblock:根据结束区块检索结果

        http://api.etherscan.io/api?module=account&action=tokentx&address=0x4e83362442b8d1bec281594cea3050c8eb01311c&startblock=0&endblock=999999999&sort=asc&apikey=YourApiKeyToken
        
仅返回最后10000个交易

或者

        https://api.etherscan.io/api?module=account&action=tokentx&contractaddress=0x9f8f72aa9304c8b593d555f12ef6589cc3a579a2&page=1&offset=100&sort=asc&apikey=YourApiKeyToken

要获得分页结果，请使用page=<page number>和offset=<返回最大记录>

或者

        https://api.etherscan.io/api?module=account&action=tokentx&contractaddress=0x9f8f72aa9304c8b593d555f12ef6589cc3a579a2&address=0x4e83362442b8d1bec281594cea3050c8eb01311c&page=1&offset=100&sort=asc&apikey=YourApiKeyToken
        
要获取特定Token合约的转移事件，请包括合约地址参数


* Get list of Blocks Mined by Address
* 根据按地址获取挖掘的块列表

        https://api.etherscan.io/api?module=account&action=getminedblocks&address=0x9dd134d14d1e65f84b706d6f205cd5b1cd03a46b&blocktype=blocks&apikey=YourApiKeyToken

或者

        https://api.etherscan.io/api?module=account&action=getminedblocks&address=0x9dd134d14d1e65f84b706d6f205cd5b1cd03a46b&blocktype=blocks&page=1&offset=10&apikey=YourApiKeyToken
        
要获取分页结果，请使用page =<页码>和offset =<返回最大记录数>

### 3.合约API

新验证的合约将在5分钟或更短的时间内同步到API服务器

此处的代码将会涉及到web3.js调用智能合约的代码，大家感兴趣的话可以仔细看看

* 获取已验证合约源代码的合约ABI

        https://api.etherscan.io/api?module=contract&action=getabi&address=0xBB9bc244D798123fDe783fCc1C72d3Bb8C189413&apikey=YourApiKeyToken
        
一个简单的示例，用于使用Web3.js和Jquery检索contractABI以与合同进行交互

        var Web3 = require('web3');
        var web3 = new Web3(new Web3.providers.HttpProvider());
        var version = web3.version.api;

        $.getJSON('http://api.etherscan.io/api?module=contract&action=getabi&address=0xfb6916095ca1df60bb79ce92ce3ea74c37c5d359', function (data) {
            var contractABI = "";
            contractABI = JSON.parse(data.result);
            if (contractABI != ''){
                var MyContract = web3.eth.contract(contractABI);
                var myContractInstance = MyContract.at("0xfb6916095ca1df60bb79ce92ce3ea74c37c5d359");
                var result = myContractInstance.memberId("0xfe8ad7dd2f564a877cc23feea6c0a9cc2e783715");
                console.log("result1 : " + result);            
                var result = myContractInstance.members(1);
                console.log("result2 : " + result);
            } else {
                console.log("Error" );
            }            
        });
        
* 获取已验证合约源代码的合约源代码

        https://api.etherscan.io/api?module=contract&action=getsourcecode&address=0xBB9bc244D798123fDe783fCc1C72d3Bb8C189413&apikey=YourApiKeyToken
        
[BETA]验证源代码

1.需要有效的Etherscan API密钥，否则将拒绝
2.每位用户每天提交的每日限额为100（可能会有变化）
3.由于http get的最大传输大小限制，仅支持HTTP post
4.最多支持10个不同的库对
5.使用“导入”的合同将需要将代码连接到一个文件中，因为我们不支持单独文件中的“导入”。 您可以尝试使用Blockcat solidity-flattener或SolidityFlattery
6.支持的solc版本列表，仅支持solc版本v0.4.11及更高版本。防爆。v0.4.25+ commit.59dbf8f1
7.成功提交后，您将收到GUID（50个字符）作为收据。
8.您可以使用此GUID来跟踪提交的状态
9.已验证的源代码将显示在contractsRerified中


源代码提交Gist（成功时返回guid作为结果的一部分）：

        $.ajax({
            type: "POST",                       //Only POST supported  
            url: "//api.etherscan.io/api", //Set to the  correct API url for Other Networks
            data: {
                apikey: $('#apikey').val(),                     //A valid API-Key is required        
                module: 'contract',                             //Do not change
                action: 'verifysourcecode',                     //Do not change
                contractaddress: $('#contractaddress').val(),   //Contract Address starts with 0x...     
                sourceCode: $('#sourceCode').val(),             //Contract Source Code (Flattened if necessary)
                contractname: $('#contractname').val(),         //ContractName
                compilerversion: $('#compilerversion').val(),   // see http://etherscan.io/solcversions for list of support versions
                optimizationUsed: $('#optimizationUsed').val(), //0 = Optimization used, 1 = No Optimization
                runs: 200,                                      //set to 200 as default unless otherwise         
                constructorArguements: $('#constructorArguements').val(),   //if applicable
                libraryname1: $('#libraryname1').val(),         //if applicable, a matching pair with libraryaddress1 required
                libraryaddress1: $('#libraryaddress1').val(),   //if applicable, a matching pair with libraryname1 required
                libraryname2: $('#libraryname2').val(),         //if applicable, matching pair required
                libraryaddress2: $('#libraryaddress2').val(),   //if applicable, matching pair required
                libraryname3: $('#libraryname3').val(),         //if applicable, matching pair required
                libraryaddress3: $('#libraryaddress3').val(),   //if applicable, matching pair required
                libraryname4: $('#libraryname4').val(),         //if applicable, matching pair required
                libraryaddress4: $('#libraryaddress4').val(),   //if applicable, matching pair required
                libraryname5: $('#libraryname5').val(),         //if applicable, matching pair required
                libraryaddress5: $('#libraryaddress5').val(),   //if applicable, matching pair required
                libraryname6: $('#libraryname6').val(),         //if applicable, matching pair required
                libraryaddress6: $('#libraryaddress6').val(),   //if applicable, matching pair required
                libraryname7: $('#libraryname7').val(),         //if applicable, matching pair required
                libraryaddress7: $('#libraryaddress7').val(),   //if applicable, matching pair required
                libraryname8: $('#libraryname8').val(),         //if applicable, matching pair required
                libraryaddress8: $('#libraryaddress8').val(),   //if applicable, matching pair required
                libraryname9: $('#libraryname9').val(),         //if applicable, matching pair required
                libraryaddress9: $('#libraryaddress9').val(),   //if applicable, matching pair required
                libraryname10: $('#libraryname10').val(),       //if applicable, matching pair required
                libraryaddress10: $('#libraryaddress10').val()  //if applicable, matching pair required
            },
            success: function (result) {
                console.log(result);
                if (result.status == "1") {
                    //1 = submission success, use the guid returned (result.result) to check the status of your submission.
                    // Average time of processing is 30-60 seconds
                    document.getElementById("postresult").innerHTML = result.status + ";" + result.message + ";" + result.result;
                    // result.result is the GUID receipt for the submission, you can use this guid for checking the verification status
                } else {
                    //0 = error
                    document.getElementById("postresult").innerHTML = result.status + ";" + result.message + ";" + result.result;
                }
                console.log("status : " + result.status);
                console.log("result : " + result.result);
            },
            error: function (result) {
                console.log("error!");
                document.getElementById("postresult").innerHTML = "Unexpected Error"
            }
        });

检查源代码验证提交状态：

        $.ajax({
            type: "GET",
            url: "//api.etherscan.io/api",
            data: {
                guid: 'ezq878u486pzijkvvmerl6a9mzwhv6sefgvqi5tkwceejc7tvn', //Replace with your Source Code GUID receipt above
                module: "contract",
                action: "checkverifystatus"
            },
            success: function (result) {
                console.log("status : " + result.status);   //0=Error, 1=Pass 
                console.log("message : " + result.message); //OK, NOTOK
                console.log("result : " + result.result);   //result explanation
                $('#guidstatus').html(">> " + result.result);
            },
            error: function (result) {
                alert('error');
            }
        });

### 4.交易API

* [BETA]检查合约执行状态（如果合约执行期间出错），注意：isError“：”0“= Pass，isError”：“1”=合约执行期间出错

        https://api.etherscan.io/api?module=transaction&action=getstatus&txhash=0x15f8e5ea1079d9a0bb04a4c58ae5fe7654b5b2b4463375ff7ffb490aa0032f3a&apikey=YourApiKeyToken
        
[BETA]检查交易背书状态（仅适用于Post Byzantium fork交易），注意：状态：0=失败，1=通过。 将为pre-byzantium fork返回null/empty值

        https://api.etherscan.io/api?module=transaction&action=gettxreceiptstatus&txhash=0x513c1ba0bebf66436b5fed86ab668452b7805593c05073eb2d51d3a52f480a76&apikey=YourApiKeyToken

### 5.区块API

* [BETA] 通过区块号获取区块和叔区块

        https://api.etherscan.io/api?module=block&action=getblockreward&blockno=2165403&apikey=YourApiKeyToken

### 6.事件日志



### 7.Geth/Parity Proxy



### 8.代币相关的API



### 9.统计相关API

* 获得以太的总供应量,结果以Wei方式返回，以获得以太坊数结果的值除以1000000000000000000的到最终的结果

        https://api.etherscan.io/api?module=stats&action=ethsupply&apikey=YourApiKeyToken

* 获得ETHER LastPrice价格

        https://api.etherscan.io/api?module=stats&action=ethprice&apikey=YourApiKeyToken

### 10.杂项、工具和实用程序

下面这些是由社区创建的第三方工具和实用程序

* py-etherscan-api模块（corpetty），由Corey Petty编写的第三方EtherScan.io API python绑定模块

        https://github.com/corpetty/py-etherscan-api

* 节点API（SebastianSchürmann），Etherscan的第三方Node API

        https:/github.com/sebs/etherscan-api

## 五.以太坊JSON-RPC接口介绍（钱包开发过程中会用到其中的一些接口）


## 六.web3j(web3java)介绍


## 七.依托钱包节点方式开发钱包


## 八.非确定性以太坊钱包开发


## 九.分层确定性以太坊钱包开发
