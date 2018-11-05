
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

### 2.确定性钱包生成以太坊地址

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

通过上面的确定性钱包生成以太坊地址生成过程我们可以看出，生成分层钱包的地址的流程是，生成助记词->生成随机数种子->导出主私钥->根据分层规范导出子私钥->导出地址。单单看上面的这两个案列，我们可能看不出不确定性钱包和确定钱包的优势和逆势。但是如果我们的钱包中有很多个账户，这时用不确定性钱包私钥就很难管理，如果用确定性钱包，我们只需要记住助记词就行。下面问将分析为什么确定性钱包只需要记住助记词就行，而分层钱包需要记住和管理很多私钥。

## 二. SLIPS项目

项目需要一种技术来记录其技术决策和功能的方法，SatoshiLabs项目提供了这么一种机制来管理比特币和加密货币。SLIP就是其项目的实现，SLIP存储库是比特币改进提案（BIP）流程的扩展，包含不适合提交给BIP存储库的文档。

每个SLIP应提供该功能的简明技术规范和该功能的基本原理。

 |     标号      |              标题                             |    类型      |          状态       |  
 |--------------|---------------------------------------------- |--------------|-------------------- |
 |   SLIP-0000  |                   SLIP模板                     |             |        必输         | 
 |   SLIP-0010  |               从主私钥派生通用私钥               |     32       |        必输         | 
 |   SLIP-0011  |         使用确定性层次结构对键值对的对称加密      |     32       |        必输         |  
 |   SLIP-0012  |   int         |     10       |        必输         |   
 |   SLIP-0013  |   tinyint     |     3        |        必输         |   
 |   SLIP-0014  |   int         |     11       |        必输         | 
 |   SLIP-0015  |   varchar(32) |     32       |        必输         | 
 |   SLIP-0016  |   varchar()   |     32       |        必输         |  
 |   SLIP-0017  |   int         |     10       |        必输         |   
 |   SLIP-0018  |   tinyint     |     3        |        必输         |   
 |   SLIP-0032  |   int         |     10       |        必输         |   
 |   SLIP-0039  |   tinyint     |     3        |        必输         |   
 |   SLIP-0044  |   int         |     10       |        必输         |   
 |   SLIP-0048  |   tinyint     |     3        |        必输         |   
 |   SLIP-0132  |   int         |     10       |        必输         |   
 |   SLIP-0173  |   tinyint     |     3        |        必输         |   

