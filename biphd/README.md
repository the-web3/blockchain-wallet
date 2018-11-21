
# 第四章：BIP钱包分层协议

## 一.为什么需要分层协议

### 1.非确定钱包生成以太坊地址

    var keythereum = require("keythereum");
    var params = { keyBytes: 32, ivBytes: 16 };

    keythereum.create(params, function (dk) {
        var options = {
          kdf: "pbkdf2",
          cipher: "aes-128-ctr",
          kdfparams: {
            c: 262144,
            dklen: 32,
            prf: "hmac-sha256"
          }
        };
        var password = "wheethereum";
        var kdf = "pbkdf2"; 
        keythereum.dump(password, dk.privateKey, dk.salt, dk.iv, options, function (keyObject) {
          console.log(keyObject.address)
          keythereum.exportToFile(keyObject);
      });
    });
    
想要运行上面这段段代码，你需要nodejs的环境并且安装keythereum库。上面这段代码是生成以太坊keystore的代码。keystore里面包含了地址、加密的私钥和MAC地址等信息，dk.privateKey是明文的私钥。上面这段代码中并没有助记词的概念。

### 2.确定性分层钱包以太坊地址生成

    var bip39 = require('bip39')
    var hdkey = require('ethereumjs-wallet/hdkey')
    var util = require('ethereumjs-util')

    var mnemonic = bip39.generateMnemonic()
    var seed = bip39.mnemonicToSeed(mnemonic)
    var hdWallet = hdkey.fromMasterSeed(seed)

    var key1 = hdWallet.derivePath("m/44'/60'/0'/0/0")
    console.log(key1._hdkey._privateKey.toString('hex'))
    var address1 = util.pubToAddress(key1._hdkey._publicKey, true).toString('hex')
    
以上代码是通过严格的分层钱包机制生成的以太坊地址和私钥，此处的私钥是明文的，如果你需要密文的私钥，可以自己写一个编码私钥的代码对私钥进行编码，下面是一段编码私钥的代码：

        var encrypt = function (str, pwd) {
          if(pwd == null || pwd.length <= 0) {
            console.log("Please enter a password with which to encrypt the message");
            return null;
          }
          var prand = "";
          for(var i=0; i<pwd.length; i++) {
            prand += pwd.charCodeAt(i).toString();
          }
          var sPos = Math.floor(prand.length / 5);
          var mult = parseInt(prand.charAt(sPos) + prand.charAt(sPos*2) + prand.charAt(sPos*3) + prand.charAt(sPos*4) + prand.charAt(sPos*5));
          var incr = Math.ceil(pwd.length / 2);
          var modu = Math.pow(2, 31) - 1;
          if(mult < 2) {
            console.log("Algorithm cannot find a suitable hash. Please choose a different password. \nPossible considerations are to choose a more complex or longer password.");
            return null;
          }
          var salt = Math.round(Math.random() * 1000000000) % 100000000;
          prand += salt;
          while(prand.length > 10) {
            prand = (parseInt(prand.substring(0, 10)) + parseInt(prand.substring(10, prand.length))).toString();
          }
          prand = (mult * prand + incr) % modu;
          var enc_chr = "";
          var enc_str = "";
          for(var i=0; i<str.length; i++) {
            enc_chr = parseInt(str.charCodeAt(i) ^ Math.floor((prand / modu) * 255));
            if(enc_chr < 16) {
              enc_str += "0" + enc_chr.toString(16);
            } else enc_str += enc_chr.toString(16);
            prand = (mult * prand + incr) % modu;
          }
          salt = salt.toString(16);
          while(salt.length < 8)salt = "0" + salt;
          enc_str += salt;
          return enc_str;
        }

        var decrypt = function (str, pwd) {
          if(str == null || str.length < 8) {
            console.log("A salt value could not be extracted from the encrypted message because it's length is too short. The message cannot be decrypted.");
            return null;
          }
          if(pwd == null || pwd.length <= 0) {
            console.log("Please enter a password with which to decrypt the message.");
            return null;
          }
          var prand = "";
          for(var i = 0; i < pwd.length; i++) {
            prand += pwd.charCodeAt(i).toString();
          }
          var sPos = Math.floor(prand.length / 5);
          var mult = parseInt(prand.charAt(sPos) + prand.charAt(sPos*2) + prand.charAt(sPos*3) + prand.charAt(sPos*4) + prand.charAt(sPos*5));
          var incr = Math.round(pwd. length / 2);
          var modu = Math.pow(2, 31) - 1;
          var salt = parseInt(str.substring(str.length - 8, str.length), 16);
          str = str.substring(0, str.length - 8);
          prand += salt;
          while(prand.length > 10) {
            prand = (parseInt(prand.substring(0, 10)) + parseInt(prand.substring(10, prand.length))).toString();
          }
          prand = (mult * prand + incr) % modu;
          var enc_chr = "";
          var enc_str = "";
          for(var i=0; i<str.length; i+=2) {
            enc_chr = parseInt(parseInt(str.substring(i, i+2), 16) ^ Math.floor((prand / modu) * 255));
            enc_str += String.fromCharCode(enc_chr);
            prand = (mult * prand + incr) % modu;
          }
          return enc_str;
        }

        export {encrypt, decrypt}

通过上面的确定性钱包生成以太坊地址生成过程我们可以看出，生成分层钱包的地址的流程是，生成助记词->生成随机数种子->导出主私钥->根据分层规范导出子私钥->导出地址。单单看上面的这两个案列，我们可能看不出不确定性钱包和确定钱包的优势和逆势。但是如果我们的钱包中有很多个账户，这时用不确定性钱包私钥就很难管理，如果用分层确定性钱包，我们只需要记住助记词就行。下面问将分析为什么确定性钱包只需要记住助记词就行，而分层钱包需要记住和管理很多私钥。

## 二. SLIPS项目

项目需要一种技术来记录其技术决策和功能的方法，SatoshiLabs项目提供了这么一种机制来管理比特币和加密货币。SLIP就是其项目的实现，SLIP存储库是比特币改进提案（BIP）流程的扩展，包含不适合提交给BIP存储库的文档。

每个SLIP应提供该功能的简明技术规范和该功能的基本原理。

 |     标号      |              标题                             |    类型      |          状态       |  
 |---------------|----------------------------------------------|--------------|-------------------- |
 |   SLIP-0000  |                   SLIP模板                     |   信息化      |        公认         | 
 |   SLIP-0010  |               从主私钥派生通用私钥               |   标准       |        草案         | 
 |   SLIP-0011  |         使用确定性层次结构对键值对的对称加密      |    标准       |        草案         |  
 |   SLIP-0012  |                使用确定性等级的公钥加密         |     标准       |        草案         |   
 |   SLIP-0013  |                  使用确定性等级认证              |    标准       |        草案         |   
 |   SLIP-0014  |                   压力测试确定性钱包             |    信息化     |        草案         | 
 |   SLIP-0015  |         比特币元数据的格式及其在高清钱包中的加密  |     标准       |        草案         | 
 |   SLIP-0016  |                  密码存储格式及其加密           |     标准       |        草案         |  
 |   SLIP-0017  |   确定性层次结构Elliptic Curve Diffie-Hellman   |     标准       |        草案         |   
 |   SLIP-0018  |                     保留（CoSi）               |     标准       |        草案         |   
 |   SLIP-0032  |              BIP-32钱包的扩展序列化格式         |     标准       |        草案         |   
 |   SLIP-0039  |                  沙米尔的助记码秘密共享         |     标准        |        草案         |   
 |   SLIP-0044  |                BIP-0044注册的币的类别          |     标准       |        草案         |  
 |   SLIP-0048  |         基于石墨烯的网络的确定性密钥层次结构     |     标准        |        草案         |   
 |   SLIP-0132  |            BIP-0032的已注册HD版本字节	         |     标准       |        草案         |   
 |   SLIP-0173  |            BIP-0173的已注册人类可读部件         |     标准        |        草案         |   

### 三.各个组件介绍

强制使用公共派生密钥对于支持私钥导出的任何软件都是不安全的，并且会导致（导致）资金损失。 缺少用于版本控制的钩子对于将来的可扩展性来说是很差的（尽管这可以在更高的层次上处理）。 通常建议不要执行此操作，硬件钱包除外。 

第二节中介绍到的这些组件中，运用最广泛的是SLIP-0044，严格的分层确定性钱包都会用到SLIP-0044分层协议的规定。当然，实际上的钱包开发中，由于ERC20代币比较混乱，大多数币都是空气币，很多项目方都不知道有这个协议的存在，故而在实际的钱包开发中，ERC20代币的地址生成协议我们都会选择使用以太坊的BIP44规定的序号。下面我们首先介绍一下这个组件。

要说BIP44,先得从BIP32说起，BIP32是HD钱包的核心提案，通过种子来生成主私钥，然后派生海量的子私钥和地址，但是种子是一串很长的随机数，不利于记录，所以我们用算法将种子转化为一串助记词，方便保存记录，这就是BIP39，它扩展了HD钱包种子的生成算法。BIP43对BIP32树结构增加了子索引标识purpose的扩展`m/purpose'/*`。 BIP44是在 BIP43和BIP32的基础上增加多币种，通过HD钱包派生多个地址，BIP44提出5层的路径建议，如下：
`m/purpse’/coin_type’/account’/change/address_index`
BIP44的规则使得HD钱包非常强大，用户只需要保存一个种子，就能控制所有币种，所有账户的钱包。

#### 1.BIP44

BIP44基于BIP-0032（从现在开始的BIP32）和BIP-0043（从现在开始的BIP43）中描述的目的方案中描述的算法定义确定性钱包的逻辑层级。BIP44是BIP43的特定应用。BIP44的层次结构非常全面。 它允许处理多个硬币，多个帐户，每个帐户的外部和内部链以及每个链数百万个地址。

##### 1.1.路径级别

在BIP32中定义了5种路径级别：

    m / purpose' / coin_type' / account' / change / address_index

路径中的撇号表示使用BIP32硬化衍生物。上面的每个路径的级别都有其特殊的意义，我们下面将做详细的说明。

##### 1.2.purpose

Purpose是在BIP43建议之后将常数设置为44'（或0x8000002C）。 它表示根据本规范使用该节点的子树。

在此级别使用强化派生。

##### 1.3.coin_type

一个主节点（种子）可用于无限数量的独立加密币，如比特币，Litecoin或Namecoin。 但是，为各种加密币共享相同的空间有一些缺点。

此级别为每个加密币创建一个单独的子树，避免重用加密链中的地址并改善隐私问题。

硬币类型是一个常量，为每个加密币设置。 Cryptocoin开发人员可能会要求为他们的项目注册未使用的号码。

已分配硬币类型的列表在下面的“已注册硬币类型”一章中。

在此级别使用强化派生。

##### 1.4. Account

此级别将密钥空间拆分为独立的用户身份，因此钱包永远不会将硬币混合在不同的帐户中。

用户可以使用这些账户以与银行账户相同的方式组织资金; 用于捐赠目的（所有地址都被视为公共地址），用于保存目的，共同费用等。

帐户按顺序递增的方式从索引0开始编号。 此数字在BIP32派生中用作子索引。

在此级别使用强化派生。

如果先前的帐户没有交易历史记录（意味着之前没有使用过任何地址），则软件应该阻止创建帐户。

从外部源导入种子后，软件需要发现所有使用过的帐户。 “帐户发现”一章中描述了这种算法。

##### 1.5.Change

常量0用于外部链，常量1用于内部链（也称为找零地址）。 外部链用于在钱包外部可见的地址（例如，用于接收付款）。 内部链用于地址，这些地址并不意味着在钱包外可见，并用于返回交易更改。

在这个级别使用公共派生。

##### 1.6.Index

地址按顺序递增的方式从索引0开始编号。 此数字在BIP32派生中用作子索引。

在这个级别使用公共派生。

#### 2.账户发现机制

从外部源导入主种子时，软件应开始以下列方式发现帐户：

* 派生第一个帐户的节点（索引= 0）
* 派生此帐户的外部链节点
* 扫描外链的地址; 遵循下面描述的差距限制
* 如果外部链上未找到任何交易，请停止发现
* 如果有某些交易，请增加帐户索引并转到步骤1

此算法是成功的，因为如果上一个帐户没有交易历史记录，程序应该禁止创建新帐户，如上面“帐户”一章所述。

请注意，该算法适用于交易历史记录，而不是帐户余额，因此您可以拥有一个总共0个硬币的帐户，算法仍将继续发现。

##### 2.1.地址差距限制

地址间隙限制当前设置为20.如果程序连续命中20个未使用的地址，则预计在此之后没有使用过的地址并停止搜索地址链。 我们只扫描外部链，因为内部链仅接收来自相关外部链的货币。

当用户试图通过生成新地址来超过外部链的间隙限制时，钱包程序应该发出警告。

#### 3.注册的币种

注册的币种是“数字货币类型”；描述的是BIP44第2级使用的默认注册硬币类型。所有这些常量都用作硬化推导。

| 索引  |   十六进制	 |        币      |
|------|--------------|----------------|
|   0  | 0x80000000	  |     比特币     |
|   1  | 0x80000001	  |  比特币测试网  |

此BIP不是注册货币类型的中央目录，请访问SatoshiLabs，其中包含完整列表：

要注册新的硬币类型，需要实现该标准的现有钱包，并且应创建对上述文件的拉取请求。

#### 例子

.： 
    ![.： 
](https://github.com/guoshijiang/blockchain-wallet/blob/master/img/coinExample.png)

