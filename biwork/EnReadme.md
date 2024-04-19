# Chapter 11: Blockchain-wallet-sdk usage and code details

blockchain-wallet-sdk is an open source library that integrates mnemonic phrase generation, digital currency address private key generation, private key management, transaction signatures, transfer initiation, transfer confirmation, etc. It is developed by the technical developers of biwork enterprise-level wallets. A set of blockchain wallet NodeJs libraries. Currently in the code development stage.

## 1. Blockchain-wallet-sdk usage

### 1. Mnemonic phrase

Currently, it supports the generation of mnemonics in 12, 15, 18, 21, and 24 different languages. The supported languages ​​are Chinese Simplified, Traditional Chinese, English, French, Japanese, Italian, Korean and Spanish. The library also provides mnemonic encoding and decoding and random number seed generation.

#### 1.1. Mnemonic phrase generation

* You need to import the library before using it

       var testWord = require("../sdk/mnemonic/generateWord");

Let’s briefly write a few cases

##### 1.1.1. Generate 12 simplified Chinese mnemonics

     var chinese_simplified = testWord.createHelpWord(12, 'chinese_simplified');
    
     Side Zhong Bi Lan Zhu Lao Tan Xun Dao Jin Cai Xiao
    
##### 1.1.2. Generate 15 traditional Chinese mnemonics

     var words = testWord.createHelpWord(15, 'chinese_traditional');
    
     In the late Qian Dynasty, he broke the nature and popularized the wood, fanned the spear, and solemnly revealed the holy words.
    
##### 1.1.3. Generate 18 Japanese mnemonics

     var words = testWord.createHelpWord(18, 'japanese');

     It's the sameんそう　てくび　さつまいも　ちつじょ　だいじょうぶ　ひっこし　てんらんかい　むかい
    
*Note: Just pass in the number of mnemonics you want to generate (currently supported: `12,15,18,21,24`); pass in the corresponding language for the latter one, and the languages ​​that can be passed in currently are : `chinese_simplified` (Chinese simplified); `chinese_traditional` (Chinese traditional); `english` (English); `french` (French); `italian` (Italian); `japanese` (Japanese); `korean` ( Korean); `spanish` (Spanish).

#### 1.2. Mnemonic codec

     // Generate mnemonic words
     var words = testWord.createHelpWord(18, 'japanese');
     console.log(words)
    
     // Particle encoding
     var wordsEntropy = testWord.wordsToEntropy(words, 'japanese');
     console.log(wordsEntropy);
    
     // Mnemonic word decoding
     var wd = testWord.entropyToWords(wordsEntropy, 'japanese');
     console.log(wd);


#### 1.3. Verify mnemonic phrase

     var bool = testWord.validateMnemonic(words, 'japanese');
     console.log(bool) // Return true to indicate successful verification
    
### 2. Generate address

Currently the library supports Ethereum, ERC20 and Bitcoin address and private key generation

#### 2.1. Generate BTC address

The following code is the code to generate the bit address

       var mnemonicS = require("../sdk/mnemonic/generateWord");
       var address = require("../sdk/address/generateAddress");

       var mnemonic= mnemonicS.createHelpWord(12, 'english');
       var seed = mnemonicS.mnemonicToSeed(mnemonic);
       var addressParmas = {
           "seed":seed,
           "coinType":"BTC",
           "number":"12",
           "bipNumber":"0",
           "receiveOrChange":"1",
           "coinMark":"BTC"
       }

       var addr = address.blockchainAddress(addressParmas);
       console.log(addr);

The following is the generated effect:

       {
         coinMark: 'BTC',
         privateKey: 'L3LK2uQQZCj7x7SzGSyRbiP8RwrYpGFPw7qbjuMKNfz83edmMJG3',
         address: '1AtMVGqbVSZDc4p5H414Q8zKqXMoYv7zxs'
       }

The parameter `seed` in the above code is a random number seed. What needs to be passed here is a `Buffer` stream; `coinType` is the type of currency to generate an address. Currently, three parameter passing methods are supported: `BTC`, `ETH `and `ERC20`; `number` is the address to be generated, `0` is the first one by default; `bipNumber` is the standard number of the coin in the `BIP44` protocol for which the address is to be generated; `receiveOrChange` is Special parameters for Bitcoin address generation, `0` represents the ordinary address, `1` represents the change address; `coinMark` is the identifier of the currency, for example, the identifier of `bitcoin` is `BTC`, and the identifier of `ethereum` is `ETH` .

#### 2.2. Generate Ethereum address

The following is the code to generate the Ethereum address, the parameters are as above

       var mnemonicS = require("../sdk/mnemonic/generateWord");
       var address = require("../sdk/address/generateAddress");

       var mnemonic= mnemonicS.createHelpWord(12, 'english');
       var seed = mnemonicS.mnemonicToSeed(mnemonic);
       var addressParmas = {
           "seed":seed,
           "coinType":"ETH",
           "number":"0",
           "bipNumber":"60",
           "receiveOrChange":"1",
           "coinMark":"ETH"
       }

       var addr = address.blockchainAddress(addressParmas);
       console.log(addr);
      
Generate effects

       {
             coinMark: 'ETH',
             privateKey: '612c02325cbf84cd4ad3dac8ae107e2bf37f98834b23f6f6208547f1a179d852',
             address: '0x947ee95a84f9bdf00dbc600961a737cb92fca5f4'
       }

If you look carefully, it is not difficult to see that the above address is being generated mainly because the parameters have changed.

#### 2.3. Generate a single ERC20 address

The following is the code to generate the ERC20 address, the parameters are as above

       var mnemonicS = require("../sdk/mnemonic/generateWord");
       var address = require("../sdk/address/generateAddress");

       var mnemonic= mnemonicS.createHelpWord(12, 'english');
       var seed = mnemonicS.mnemonicToSeed(mnemonic);
       var addressParmas = {
           "seed":seed,
           "coinType":"ERC20",
           "number":"0",
           "bipNumber":"518",
           "receiveOrChange":"1",
           "coinMark":"LET"
       }

       var addr = address.blockchainAddress(addressParmas);
       console.log(addr);


Only the incoming parameters change

Generate effect:

       {
             coinMark: 'LET',
             privateKey: '0bdf145ae58a71c0da7fc3cf5510de5909d1314722f8d90c6b9ee543e431f1c7',
             address: '0x7d0c60a5ef87ed7f43fcad648911327c1f474623'
       }

#### 2.4. Generate ERC20 addresses in batches

       var mnemonicS = require("../sdk/mnemonic/generateWord");
       var address = require("../sdk/address/generateAddress");

       var mnemonic= mnemonicS.createHelpWord(12, 'english');
       var seed = mnemonicS.mnemonicToSeed(mnemonic);

       var ERC20AddressParam = {
           "seed":seed,
           "erc20":[
               {
                   "coinMark":"LET",
                   "bipNumber":"618",
                   "number":"0"
               },{
                   "coinMark":"SSP",
                   "bipNumber":"518",
                   "number":"0"
               },{
                   "coinMark":"Kcash",
                   "bipNumber":"128",
                   "number":"0"
               }
           ]
       }

       var addre= address.multiERC20AddressGenerate(ERC20AddressParam);console.log(addre);
      
The following is the generated effect

       [
             {
                   coinMark: 'LET',
                   privateKey: '164a6167e4c50e24ad6a74fd1cab6521e3a1472a70d67c88146a1952b7fb5092',
                   address: '0x3a960d90b219ea4b9d3f5827ea6fba5bf40d391f'
            },
            {
                   coinMark: 'SSP',
                   privateKey: 'f8f766b246289f325e32cb28462096ade51f89b720dc6b064168baef2e6c9ac4',
                   address: '0x8f5589faf64374788d1c8f3b1744bad1627ec565'
            },
            {
                   coinMark: 'Kcash',
                   privateKey: '9bf8d4b890b391736cec94220d5d41d0c8a2b9555a5385316eecbeb89ce37d61',
                   address: '0x910b0d94dd1f0f173f41e5ed0a2ddefeea6b4130'
           }
       ]

#### 2.5. Generate OMNI-USDT address

Since the underlying protocol of OMNI directly forks Bitcoin, the address generation method of OMNI-USDT is exactly the same as that of Bitcoin.

#### 2.6. EOS address generation method


    
### 3. Ethereum Keystore

#### 3.1. Generate Keystore

To generate the keystore, you only need to pass in the password, and the keystore will be automatically generated. In the following function, you only need to pass in one parameter, which is the password. "123456" is the password passed in.

     var testKeystore = require("../sdk/keystore/generateKeystore");
     var keystore = testKeystore.createKeystore("123456");
     console.log(keystore);
      {
           address: '531fe5d8be925ceba9bfce0c305d93fc6deb862b',
           crypto:
           {
                 cipher: 'aes-128-ctr',
                 ciphertext: '3cf8ba317042815338f7397d80a53cf84cc7861c9ce70ed26fd23b2fdab9efc',
                 cipherparams: { iv: '82c3b2d97f65ff974563bd14d5edaf4f' },
                 mac: '67b2960dd73cd4f35d375a4c8c0898fc6f6cd9b7190dafb1c14aa724179a1e06',
                 kdf: 'pbkdf2',
                 kdfparams:
                 {
                       c: 262144,
                       dklen: 32,
                       prf: 'hmac-sha256',
                       salt: 'cb7a82cba06330ffe0ad2fea7124034d53f5db559e30e7752f9562dab8caa39c
                  }
            },
           id: 'a101bdc9-54d6-44a9-b787-587c92fbc680',
           version: 3
     }
  
#### 3.2. Export keystore

exportKeystore is a function that exports Keystore. The parameters that need to be passed in are password and path, and the value of keystore is returned.

     var testKeystore = require("../sdk/keystore/generateKeystore");
     var keystore = testKeystore.createKeystore("123456");
     console.log(keystore);
     testKeystore.exportKeystore(keystore, "./");
  
The corresponding keystore file will be generated in the directory you specify.

      UTC--2018-11-29T07-30-06.805Z--2fecb0dd3718453b51d7f42db6e9c60b2c82cfa9
    
### 3. Import keystore


     var keystore = testKeystore.importKeystore("531fe5d8be925ceba9bfce0c305d93fc6deb862b", "./");
     console.log(keystore);
      {
           address: '531fe5d8be925ceba9bfce0c305d93fc6deb862b',
           crypto:
           {
                 cipher: 'aes-128-ctr',
                 ciphertext: '3cf8ba317042815338f7397d80a53cf84cc7861c9ce70ed26fd23b2fdab9efc',
                 cipherparams: { iv: '82c3b2d97f65ff974563bd14d5edaf4f' },
                 mac: '67b2960dd73cd4f35d375a4c8c0898fc6f6cd9b7190dafb1c14aa724179a1e06',
                 kdf: 'pbkdf2',
                 kdfparams:
                 {
                       c: 262144,
                       dklen: 32,
                       prf: 'hmac-sha256',
                       salt: 'cb7a82cba06330ffe0ad2fea7124034d53f5db559e30e7752f9562dab8caa39c
                  }
            },
           id: 'a101bdc9-54d6-44a9-b787-587c92fbc680',
           version: 3
     }
  
### 4. Export private key

      var privateKey = testKeystore.exportPrivateKey(keystore, "123456")
  
### 5. Import private key

       var keystore = testKeystore.importPrivateKey("qeeqeqqeqwweq", "123456");
      console.log(keystore)
       { address: '805a06621171443bcf7d2bae9ac4c91539aef65f',
       crypto:
        { cipher: 'aes-128-ctr',
          ciphertext: 'f7c0ce4b3c83b9927ce75cbceb',
          cipherparams: { iv: '173ecf2cf4e34879175190a5a7b328cd' },
          mac: 'd8b829e98383cbc8eb69f090e765c085e0882006489c74837aa4bff69b7b62bc',
          kdf: 'pbkdf2',
          kdfparams:
           { c: 262144,
             dklen: 32,
             prf: 'hmac-sha256',
             salt: '85766bde5af062f631d62d16842d079e8a0a52a0555bfd94ce3f1b3c28fa839b'
      } },
       id: '70860efe-84f0-43c9-9d68-3f8f0262203b',
       version: 3 }
      
### 4.Digital currency signature

  ### 1. Bitcoin single transfer signature
 
      const testBtcSign = require('../sdk/sign/bitcoinSign');
      var privateKey = "L2CzLwNmNxVtV4RpgBMMRKPWhZmDeMofqxEqUjeRi8nQaVae5F51";
     var amount = "100000";
     var utxo = {
         "unspent_outputs":[
             {
                 "tx_hash":"8ee886ba0c66ba2df2c0e3da3beee526996d9a5e6bbbdfea43e1a78340cb0128",
                 "tx_hash_big_endian":"2801cb4083a7e143eadfbb6b5e9a6d9926e5ee3bdae3c0f22dba660cba86e88e",
                 "tx_index":382253932,
                 "tx_output_n": 0,
                 "script":"76a914ca45c6eceea7aed14b6aea7e0ed466c6134f14bc88ac",
                 "value": 1899000,
                 "value_hex": "1cf9f8",
                 "confirmations":2
             }
         ]
     };
     var sendFee = "100000";
     var toAddress = "12zEJohMNqSZLXH1Msxpw41ykkk3rxgx1s";
     var changeAddress = "12zEJohMNqSZLXH1Msxpw41ykkk3rxgx1s";
      var signValue = testBtcSign.btcSingleSign(privateKey, amount, utxo.unspent_outputs, sendFee, toAddress, changeAddress);
     console.log(signValue);
    
The result is as follows:

      01000000018ee886ba0c66ba2df2c0e3da3beee526996d9a5e6bbbdfea43e1a78340cb0128000000
     006a473044022002d03fbb44d012733de62bd34047c22ef988bd3c79134be63565a1a0e031bfbe02
     2067bddc7d9e5b70ff78abd2db2db499b3bd8ecb23b8bf9f7ecc670cc55b4d5818012103881a9743
     d7bbb3e3f1ec534960b433cff5f883eec1f1c29dfdaf3aff43c1d0feffffffff02a0860100000000
     001976a91415caf1976fc8334d4f2f071548d9cbace1a4517988acb8ec1900000000001976a91415
     caf1976fc8334d4f2f071548d9cbace1a4517988ac00000000
    
#### 3.2. Bitcoin multiple transaction transfer signatures


      const testBtcSign = require('../sdk/sign/bitcoinSign');
      var sendInfo = {
         "privateKey":"KwHEU8DTrY2ekGuqE6EqMMrcFj6Kdb6gWF4k8SpUeV7vDfc9c5Fn",
         "changeAddress":"1KSX5wmrVax3LYaB4uKUxXzCRcv5SiLDq3",
         "sendFee":1000,
         "addressAmount":[
             {
                 "toAddress":"12zEJohMNqSZLXH1Msxpw41ykkk3rxgx1s",
                 "amount":10
             },{
                 "toAddress":"1KSX5wmrVax3LYaB4uKUxXzCRcv5SiLDq3",
                 "amount":10
             },{
                 "toAddress":"12zEJohMNqSZLXH1Msxpw41ykkk3rxgx1s",
                 "amount":10
             }
         ]
     }
      var bitUtxo = {
         "unspent_outputs":[
             {
                 "tx_hash":"8ee886ba0c66ba2df2c0e3da3beee526996d9a5e6bbbdfea43e1a78340cb0128",
                 "tx_hash_big_endian":"2801cb4083a7e143eadfbb6b5e9a6d9926e5ee3bdae3c0f22dba660cba86e88e",
                 "tx_index":382253932,
                 "tx_output_n": 0,
                 "script":"76a914ca45c6eceea7aed14b6aea7e0ed466c6134f14bc88ac",
                 "value": 1899000,
                 "value_hex": "1cf9f8",
                 "confirmations":2
             }
         ]
     };
      var utxo = bitUtxo.unspent_outputs;
     var signValue = testBtcSign.btcMultiSign(sendInfo, utxo);
     console.log(signValue);
    
result:

     01000000018ee886ba0c66ba2df2c0e3da3beee526996d9a5e6bbbdfea43e1a78340cb0128000000
     006a47304402207bf66084ebbb91abc4fd4132b21a2d4d3b9c9d6535da1ff599ec0d323595ff2402
     200965c6afd1d0b673f71761a0d53f6d6dcb6b194b1064226ef440c3d460b327ee01210202184157
     40e8ae879fc6b6837e4ab6e8c44d452c62f7e0f7bceb37ae1b13e685ffffffff040a000000000000
     001976a91415caf1976fc8334d4f2f071548d9cbace1a4517988ac0a000000000000001976a914ca
     45c6eceea7aed14b6aea7e0ed466c6134f14bc88ac0a000000000000001976a91415caf1976fc833
     4d4f2f071548d9cbace1a4517988acf2f51c00000000001976a914ca45c6eceea7aed14b6aea7e0e
     d466c6134f14bc88ac00000000
    
  ### 3. Ethereum single transfer signature
 
      const testEthSign = require('../sdk/sign/ethereumSign');
      var privateKey = "a2506976294fc506f6969e8f914ae9371804b104163f07e8d0e96794d5b43189";
     var nonce = 78;
     var toAddress = "0xe558be4e90b2ac96ae5cad47dc39cd08316f2e57";
     var gasPrice = 9000000000;
     var gasLimit = 120000;
     var sendToBalance = 10;
      var signValue = testEthSign.ethereumSign(privateKey, nonce, toAddress, sendToBalance, gasPrice, gasLimit);
     console.log(signValue);
    
result:
     
      0xf86d4e850218711a008301d4c094e558be4e90b2ac96ae5cad47dc39cd08316f2e57888ac72304
     89e80000801ca03dcd39cc88ce022914de6b8ec66915d14fe3273f342b67692bea5e338ce3c2dfa0
     16ecf3d329d4497baf538a36d7874ecc8aee7c537dca45e36fe0d7fc420da330

#### 3.4. Ethereum batch transfer signature

      const testEthSign = require('../sdk/sign/ethereumSign');
      var sendData =
         {
             "signMark":"ETH",
             "privateKey":"a2506976294fc506f6969e8f914ae9371804b104163f07e8d0e96794d5b43189",
             "gasPrice":12000000000,
             "gasLimit":30000,
             "nonce":63,
             "signDta":[
                 {
                     "toAddress":"0xe558be4e90b2ac96ae5cad47dc39cd08316f2e57",
                     "totalAmount":0.0001,
                 },{
                     "toAddress":"0x69204ab30fa18fb6b5b9677e639cdaf8a7e9b587",
                     "totalAmount":0.0001,
                 },{
                     "toAddress":"0x79faca516a4eae381ab0baa50f99629ee59ead89",
                     "totalAmount":0.0001,
                 }
             ]
         }
      var signValue = testEthSign.ethereumMultiSign(sendData);
     console.log(signValue);
    
result:

      {
           signCoin: 'ETH',
           signDataArr:
           [
               '0xf86a3f8502cb41780082753094e558be4e90b2ac96ae5cad47dc39cd08316f2e 57865af3107a4000801ca0520360d673c0988ad6771c702ebf360449a05f84cf97bb 1feb60342e6dc50b81a078c4c3ab0026e49fa0ab3555888d26ca01813763a1c57e7f5ee2074790e4dee5',
          '0xf86a408502cb4178008275309469204ab30fa18fb6b5b9677e639cdaf8a7e9b5 87865af3107a4000801ba05ccc441eac845e2bcdc2889ddcae025e4040159aea441 a795bb4cf359c1a4e73a03afdeda4978b3b032ead801de3ac4e114ea5940f761230075dc5765bba146f13',
           '0xf86a418502cb4178008275309479faca516a4eae381ab0baa50f99629ee59ead8 9865af3107a4000801ca04e586da8a2e8c8d606886572b82cefdf51402ad819f22aef 52634e188b9355d6a0078214480d95f1a3a2cf62be2f0030c4c67cc9c598550ee0f174a1e22c244de9'
         ]
   }
  
#### 3.5.ERC20 single transfer signature

     const testERC20Sign = require('../sdk/sign/erc20Sign');
     var privateKey = "a2506976294fc506f6969e8f914ae9371804b104163f07e8d0e96794d5b43189";
     var nonce = 78;
     var currentAccount = "0xc6328b3a137b3be3f01c35ecda4ecda375be7fdf";
     var contractAddress = "0xfa3118b34522580c35ae27f6cf52da1dbb756288";
     var toAddress = "0xe558be4e90b2ac96ae5cad47dc39cd08316f2e57";
     var gasPrice = 9000000000;
     var gasLimit = 120000;
     var totalAmount = 10;
     var decimal = 6;
      var signValue = testERC20Sign.ethereumErc20CoinSign(privateKey, nonce, currentAccount, contractAddress, toAddress, gasPrice, gasLimit, totalAmount, decimal);
     console.log(signValue);
    
result:

      0xf8aa4e850218711a008301d4c094fa3118b34522580c35ae27f6cf52da1dbb75628880b844a905
     9cbb000000000000000000000000e558be4e90b2ac96ae5cad47dc39cd08316f2e57000000000000
     0000000000000000000000000000000000000000000009896801ba07d9cd29d2af124c00714d604
     747c41dad42731d5c890dfade8217c6abba13899a064da50dce8079678a8eeaffb976957f1592332
     682f888e8d106db93d362bee93
    
### 3.6. ERC20 batch transfer signature

      const testERC20Sign = require('../sdk/sign/erc20Sign');
      var sendErc20Data =
         {
             "signMark":"ERC20",
             "privateKey":"a2506976294fc506f6969e8f914ae9371804b104163f07e8d0e96794d5b43189",
             "contractAddress":"0xfa3118b34522580c35ae27f6cf52da1dbb756288",
             "currentAccount":"0xc6328b3a137b3be3f01c35ecda4ecda375be7fdf",
             "gasPrice":9000000000,
             "gasLimit":90000,
             "nonce":81,
             "decimal":6,
             "signDta":[
                 {
                     "toAddress":"0xe558be4e90b2ac96ae5cad47dc39cd08316f2e57",
                     "totalAmount":10
                 },{
                     "toAddress":"0x69204ab30fa18fb6b5b9677e639cdaf8a7e9b587",
                     "totalAmount":10
                 }
             ]
         }
      var signValue = testERC20Sign.MultiEthereumErc20CoinSign(sendErc20Data);
     console.log(signValue);
    
result:

      { signCoin: 'ERC20',
       signDataArr:
        [ '0xf8aa51850218711a008301d4c094fa3118b34522580c35ae27f6cf52da1dbb75628880b8
     44a9059cbb000000000000000000000000e558be4e90b2ac96ae5cad47dc39cd08316f2e57000000
     000000000000000000000000000000000000000000000000009896801ca0401192d543095f6b46
     08cfb64c682edb8598bdc10b8108c4229f774272ef1cdfa02f8ad180dcd8c5e99b45155444c72081
     fb2d101c42e73aa5ac4dc38778f671b2',
          '0xf8aa52850218711a008301d4c094fa3118b34522580c35ae27f6cf52da1dbb75628880b8
     44a9059cbb00000000000000000000000069204ab30fa18fb6b5b9677e639cdaf8a7e9b587000000
     000000000000000000000000000000000000000000000000009896801ca0cf346f3a99a56adb66
     985be0d6fe5c08012542d96d96cf72eb89f7cf711667e5a06dd14c9c988b81eb7af5027514e20163
     67b89fd5bed9f619257ff3e816b9b62a' ] }
  
  
  
#### 3.6. Signature of OMNI-USDT

#### 3.7. EOS signature


## 2. Detailed explanation of blockchain-wallet-sdk code

### 1. Detailed explanation of mnemonic code part

### 2. Detailed explanation of some codes of Ethereum KeyStore

### 3. Detailed explanation of some codes of Ethereum and ERC20

### 4. Detailed explanation of some Bitcoin codes

### 5. OMNI—Detailed explanation of some USDT codes

### 6. Detailed explanation of some EOS codes
