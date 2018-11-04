
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
