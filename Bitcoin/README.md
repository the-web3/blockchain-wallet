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
](https://github.com/guoshijiang/blockchain-wallet/blob/master/img/PubKeyToAddr.png)

### 3.比特币地址生成代码实战

关于比特币的地址生成部分，咱们主要生成主网地址和测试网地址，使用nodeJs和java两种语言实现。

#### 3.1.主网比特币地址生成（NodeJs版）


#### 3.2.测试网比特币地址生成（NodeJs版）


#### 3.3.主网比特币地址生成（Java版）


#### 3.4.测试网比特币地址生成（Java版）
