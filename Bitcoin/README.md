# 第六章：比特币钱包开发

本章主要内容有：比特币地址和比特币地址生成、比特私钥生成、比特币交易签名，发送比特币交易到区块链网络。

## 一.比特币的地址

### 1.比特币地址前缀

基于区块链的货币使用编码字符串，这些字符串采用Base58Check编码，但Bech32编码除外。 编码包括前缀（传统上是单个版本字节），其影响编码结果中的前导符号。 下面是比特币代码库中使用的一些前缀列表。

|   十进制前缀   | 十六进制	 |      案列使用               |	   前面标识   |	                        例子                        | 
|---------------|-----------|-----------------------------|---------------|-----------------------------------------------------|
|       0	      |     00	  |    公钥Hash (P2PKH地址)	     |      1	       |    17VZNX1SN5NtKa8UQFxwQbFeFc3iqRYhem               |
|       5	      |     05    |	   脚本Hash(P2SH address)   |      3	       |     3EktnHQD7RiAE6uzMj2ZifT9YgRrkSgzQX              |
|     128	      |     80	  |  私钥 (WIF, 未压缩公钥)	    |      5	       |5Hwgr3u458GLafKBgxtssHSPqJnYoGrSzgQsPwLFhLNYskDPyyA |
|     128	      |     80	  |    私钥 (WIF, 压缩公钥)	     |    K or L	    |L1aW4aubDFB7yfras2S1mN3bqg9nwySY8nkoLmJebSLD5BWv3ENZ|
|4 136 178 30   | 	0488B21E|	          BIP32公钥	        |      xpub	     | xpub661MyMwAqRbcEYS8w7XLSVeEsBXy79zSzH1J8vCdxAZningWLdN3zgtU6LBpB85b3D2yc8sfvZU521AAwdZafEz7mnzBBsz4wKY5e4cp9LB|
| 4 136 173 228 |	0488ADE4 |	      BIP32私钥             |	      xprv	   | xprv9s21ZrQH143K24Mfq5zL5MhWK9hUhhGbd45hLXo2Pq2oqzMMo63oStZzF93Y5wvzdUayhgkkFoicQZcP3y52uPPxFnfoLZB21Teqt1VvEHx |
|      111      |   	6F	 |       测试公钥hash           |	m or n         |	    mipcBbFg9gMiCh81Kj8tqqdgoZub1ZJRfn                 |
|      196	    |     C4	 |        测试脚本hash          |	    2	          |    2MzQwSSnBHWHqSAqtTVQ6v47XtaisrJa1Vc                |
|      239	    |     EF	 | 测试网私钥 (WIF, 未压缩公钥)  |	9	             | 92Pg46rUhgTT7romnV7iGW6W1gbGdeezqdbJCzShkCsYNzyyNcc   |
|      239	    |     EF	 | 测试网私钥 (WIF, 压缩公钥)    |  c              |	cNJFgo1driFnPcBdBX8BrJrpxchBWXwXCvNH5SoSkdcF6JXXwHMm  |
| 4 53 135 207  |	043587CF |     测试网BIP32公钥	         | tpub	           | tpubD6NzVbkrYhZ4WLczPJWReQycCJdd6YVWXubbVUFnJ5KgU5MDQrD998ZJLNGbhd2pq7ZtDiPYTfJ7iBenLVQpYgSQqPjUsQeJXH8VQ8xA67D|
| 4 53 131 148  |	04358394 |      测试网BIP32私钥         |	tprv	         |  tprv8ZgxMBicQKsPcsbCVeqqF1KVdH7gwDJbxbzpCxDUsoXHdb6SnTPYxdwSAKDC6KKJzv7khnNWRAJQsRA8BBQyiSfYnRt6zuu4vZQGKjeW4YF|
|               |          |  Bech32公钥hash和脚本Hash	   | bc1	           | bc1qw508d6qejxtdg4y5r3zarvary0c5xw7kv8f3t4 |
|               |          |Bech32测试网公钥hash和脚本Hash| tb1	             |tb1qw508d6qejxtdg4y5r3zarvary0c5xw7kxpjzsx|

请注意，压缩和未压缩比特币公钥的私钥使用相同的版本字节。 压缩表单以不同字符开头的原因是因为在base58编码之前将私有键附加了0x01字节。

### 2.比特币地址生成原理

下图是椭圆曲线公钥到比特币地址的转换

.： 
    ![.： 
](https://github.com/guoshijiang/blockchain-wallet/blob/master/img/PubKeyToAddr.jpg)


### 3.比特币地址生成代码实战

关于比特币的地址生成部分，咱们主要生成主网地址和测试网地址，使用nodeJs和java两种语言实现。

#### 3.1.主网比特币地址生成（NodeJs版）

rng ()函数产生随机数种子，下面的代码使用了bitcoinjs库

    const bitcoin = require('bitcoinjs-lib')
    const baddress = require('bitcoinjs-lib/src/address')
    const bcrypto = require('bitcoinjs-lib/src/crypto')
    const NETWORKS = require('bitcoinjs-lib/src/networks')

    // deterministic RNG for testing only
    function rng () { return Buffer.from('sssszzddzzzzzzzzzzzzzzzzzzzzzzzz') }

    const testnet = bitcoin.networks.testnet
    const keyPair = bitcoin.ECPair.makeRandom({ network: testnet, rng: rng })
    const wif = keyPair.toWIF()
    console.log("wif = " + wif)

    const { address } = bitcoin.payments.p2pkh({ pubkey: keyPair.publicKey, network: testnet })
    console.log("address = " + address)
    
#### 3.2.测试网比特币地址生成（NodeJs版）

下面代码生成了找零地址和非找零地址

    var bip39 = require('bip39')
    const bip32 = require('bip32')
    const bitcoin = require('bitcoinjs-lib')
    const baddress = require('bitcoinjs-lib/src/address')
    const bcrypto = require('bitcoinjs-lib/src/crypto')
    const NETWORKS = require('bitcoinjs-lib/src/networks')

    function getAddress (node) {
        console.log("PrivateKey = " + node.toWIF().toString('hex'));
        console.log("PublicKey =" + node.publicKey.toString('hex'))
        return baddress.toBase58Check(bcrypto.hash160(node.publicKey), NETWORKS.bitcoin.pubKeyHash)
    }
    var mnemonic = bip39.generateMnemonic()
    var seed = bip39.mnemonicToSeed(mnemonic)
    const rootMasterKey = bip32.fromSeed(seed)

    var key1 = rootMasterKey.derivePath("m/44'/0'/0'/0/0")
    var key2 = rootMasterKey.derivePath("m/44'/0'/0'/0/1")
    var key3 = rootMasterKey.derivePath("m/44'/0'/0'/1/0")
    var key4 =rootMasterKey.derivePath("m/44'/0'/0'/1/1")

    //普通地址
    console.log(getAddress(key1))
    console.log(getAddress(key2))

    //找零地址
    console.log(getAddress(key3))
    console.log(getAddress(key4))

#### 3.3.主网比特币地址生成（Java版）



#### 3.4.测试网比特币地址生成（Java版）


## 二.比特币手续建议

查看比特币建议手续费的网站`https://bitcoinfees.earn.com/`

### 1.应该使用那一个手续费

在上面这个网站上可以看到当前的手续费是多少聪每比特，一般来说，一个中等的交易是225比特，手续费的结果是8550聪。但是许多钱包使用每千字节satoshis或每千字节比特币，因此您可能需要转换单位。

### 2.显示的费用是多少

此处显示的费用为每字节交易数据的Satoshis（0.00000001 BTC）。 矿工通常首先包括费用/字节最高的交易。钱包应根据用户需要确认的速度，根据此数字进行费用计算。

### 3.交易延迟意味着什么

此处显示的延迟是交易将要确认的预测块数。 如果交易预计会延迟1-3个区块，则有90％的可能性会在该范围内（约10至30分钟）确认。

具有较高费用的交易通常会有0延迟，这意味着它们可能会在下一个区块（通常大约5-15分钟）确认。

### 4.如何预测延迟

预测基于过去3小时的区块链数据，以及当前未经证实的交易池（mempool）。

首先，使用蒙特卡罗模拟预测可能的未来mempool和矿工行为。 从模拟中可以看出，具有不同费用的交易有多快可能包含在即将到来的区块中。

选择此处所示的预测延迟以表示90％置信区间。

### 5.比特币费用开发API

获取当前用于钱包或其他服务的JSON格式的比特币交易费预测。

#### 5.1.建议交易手续费接口

1.接口名字：

    https://bitcoinfees.earn.com/api/v1/fees/recommended
    
2.请求方式

    HTTP GET方式

3.接口返回数据：

    { "fastestFee": 40, "halfHourFee": 20, "hourFee": 10 }

4.参数解释

* astestFee：最低费用（每个字节的satoshis），目前将导致最快的事务确认（通常为0到1块延迟）。
* halfHourFee：最低费用（每个字节的satoshis）将在半小时内确认交易（概率为90％）。
* hourFee：最低费用（每个字节的satoshis）将在一小时内确认交易（概率为90％）。

#### 5.2.交易费用摘要

1.接口名字：

    https://bitcoinfees.earn.com/api/v1/fees/list
    
2.请求方式

    HTTP GET方式

3.接口返回数据：

    { "fees": [ 
      {"minFee":0,"maxFee":0,"dayCount":545,"memCount":87,
      "minDelay":4,"maxDelay":32,"minMinutes":20,"maxMinutes":420},
    ...
     ] }

4.返回

返回Fee对象列表，其中包含有关在satoshis / byte中从minFee到maxFee的给定范围内的费用的预测。
Fee对象具有以下属性（除了它们引用的minFee-maxFee范围）：

5.参数解释

* dayCount：过去24小时内收取此费用的已确认交易数。
* memCount：此费用未经证实的交易数量。
* minDelay：确认交易之前的估计最小延迟（以块为单位）（90％置信区间）。
* maxDelay：确认事务之前的估计最大延迟（以块为单位）（90％置信区间）。
* minMinutes：确认交易确认之前的最短时间（以分钟为单位）（90％置信区间）。
* maxMinutes：确认交易之前的估计最长时间（以分钟为单位）（90％置信区间）。

6.错误代码

* 状态503：服务不可用（请在生成预测时等待）
* 状态429：请求太多（已达到API速率限制）

## 三.比特币区块链浏览器API

## 四.比特币JSON-RPC接口

## 五.在线发起比特交易

## 六.比特币交易离线签名

## 七.发送交易比特币主网























