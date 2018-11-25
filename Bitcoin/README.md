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

## 三.比特开发者API

Blockchain提供免费使用的API来帮助您开始构建比特币应用程序。在使用blockchain的API时，你需要申请一个秘钥，申请秘钥的地址如下

    https://api.blockchain.info/customer/signup
    

### 1.支付过程API（接收付款API V2）

一种非常简单的网站接收比特币支付的方法。 这项服务完全免费且安全。 适合商务或个人使用。用户提供了扩展公钥（xPub），blockchain会为用户的客户生成一个唯一的，未使用的相应地址，以便向其发送付款。 blockchain会立即使用你选择的回拨网址通知您该地址的付款。

Blockchain接收付款API V2是开始接受自动比特币支付的最快捷，最简单的方式。 只需一个简单的HTTP GET请求，您就可以在几分钟内启动并运行。

接收比特币支付所涉及的困难之一是需要为每个新用户或发票生成唯一地址。 需要安全地监视和存储这些地址。 区块链接收付款API负责地址的生成和监控。 每当收到付款时，blockchain都会使用简单的回电通知您的服务器。

#### 1.1.请求API密钥访问Blockchain.info API

要使用Receive Payments API V2，请通过https://api.blockchain.info/v2/apikey/request/申请API密钥。 此API密钥仅适用于我们的Receive Payments API。 您不能将标准区块链钱包API密钥用于Receive Payments V2，反之亦然。

#### 1.2.获取扩展公钥（xPub）[xPub可以使用我们新的区块链钱包创建]

此API要求您拥有BIP 32帐户xPub才能接收付款。 开始接收付款的最简单方法是在https://blockchain.info/wallet/#/signup上打开区块链钱包。 您应该在钱包内创建一个新帐户，专门用于此API促成的交易。 进行API调用时，请使用此帐户的xPub（位于“设置” - >“地址” - >“管理” - >“更多选项” - >“显示xPub”）。

#### 1.3.生成接收地址[GET]为您的客户提供唯一的, [未使用的比特币地址]

此方法创建一个应呈现给客户的唯一地址。对于发送到此地址的任何付款，您将收到HTTP通知。请注意，每次调用服务器都会增加`index`参数。这样做是为了不向两个不同的客户显示相同的地址。但是，所有资金仍将显示在同一帐户中。

    https://api.blockchain.info/v2/receive?xpub=$xpub&callback=$callback_url&key=$key

如BIP 44中所定义，钱包软件不会扫描超过20个未使用的地址。如果来自此API的足够多的请求没有匹配的付款，您可以生成超出此范围的地址，这将使支付这些地址的资金变得非常困难。因此，此API将返回错误并拒绝生成新地址，如果它检测到它将创建超过20个未使用地址的间隙。如果您遇到此错误，您将需要切换到新的xPub（在同一个钱包内是可以的），或者收到前20个创建的地址之一的付款

您可以通过选择将“gap_limit”作为额外的URL参数传递来控制此行为。请注意，这不会增加我们的服务器将监控的地址数量。传递`gap_limit`参数会在API停止生成新地址之前更改允许的最大间隙。使用此功能需要您了解差距限制以及如何处理它（仅限高级用户）：

    https://api.blockchain.info/v2/receive?xpub=$xpub&callback=$callback_url&key=$key&gap_limit=$gap_limit

* xpub:您的xPub（您希望付款的位置）
* callback_url:收到付款时要通知的回调URL。记住URL在调用create方法时对回调URL进行编码。
* key:您的blockchain.info接收付款v2 api密钥。请求API密钥。
* gap_limit:可选。在出错之前允许多少个未使用的地址。

使用xPub导出未使用的地址：

    curl "https://api.blockchain.info/v2/receive?xpub=xpub6CWiJoiwxPQni3DFbrQNHWq8kwrL2J1HuBN7zm4xKPCZRmEshc7Dojz4zMah7E4o2GEEbD6HgfG7sQid186Fw9x9akMNKw2mu1PjqacTJB2&callback=https%3A%2F%2Fmystore.com%3Finvoice_id%3D058921123&key=[yourkeyhere]"

让您的客户将比特币发送到响应中包含的地址：
回复：200 OK，application / json

    {"address":"19jJyiC6DnKyKvPg38eBE8R6yCSXLLEjqw","index":23,"callback":"https://mystore.com?invoice_id=058921123"}

PHP请求示例

    $secret = 'ZzsMLGKe162CfA5EcG6j';
    $my_xpub = '{YOUR XPUB ADDRESS}';
    $my_api_key = '{YOUR API KEY}';
    $my_callback_url = 'https://mystore.com?invoice_id=058921123&secret='.$secret;
    $root_url = 'https://api.blockchain.info/v2/receive';
    $parameters = 'xpub=' .$my_xpub. '&callback=' .urlencode($my_callback_url). '&key=' .$my_api_key;
    $response = file_get_contents($root_url . '?' . $parameters);
    $object = json_decode($response);
    echo 'Send Payment To : ' . $object->address;

github上的代码地址：https://github.com/blockchain/receive-payments-demos

#### 1.4.余额更新

此方法监控您选择的已接收和/或预付款的地址。您将在进行交易时立即向您发送HTTP通知，并在随后达到请求中指定的确认数时发送HTTP通知。

您需要指定请求的通知行为。将行为设置为“DELETE”将在将第一个相关通知发送到您的回调地址后删除该请求。将行为设置为“KEEP”将在每次将具有指定确认和操作类型的事务发送到请求中的地址或从请求中的地址发送时发送其他通知。

操作类型是一个可选参数，指示是否将监视接收或已用事务的地址，或两者。默认情况下，监视两种操作类型。

您还可以选择在发送通知之前指定事务达到的确认数。请注意，您将在0次确认时（即交易时立即收到）收到通知，并在达到请求中指定的确认次数时（默认情况下为3次确认）再次收到通知。


    https://api.blockchain.info/v2/receive/balance_update

* 地址：要监控的地址
* 回调：收到付款时要通知的回调网址。
* 密钥：你的blockchain.info接收付款v2 api密钥。 请求API密钥。
* onNotification：请求通知行为（'KEEP'|'DELETE）。
* Confs：可选（默认3）。 在发送通知之前交易需要的确认数。
* Op：可选（默认为“ALL”）。 您希望接收通知的操作类型（'SPEND'|'RECEIVE'|'ALL'）。

通过5次确认监控每个收到付款的地址：

    curl -H "Content-Type: text/plain" --data '{"key":"[your-key-here]","addr":"183qrMGHzMstARRh2rVoRepAd919sGgMHb","callback":"https://mystore.com?invoice_id=123","onNotification":"KEEP", "op":"RECEIVE", "confs": 5}' https://api.blockchain.info/v2/receive/balance_update

回复：200 OK，application/json

    {
      "id" : 70,
      "addr" : "183qrMGHzMstARRh2rVoRepAd919sGgMHb",
      "op" : "RECEIVE",
      "confs" : 5,
      "callback" : "https://mystore.com?invoice_id=123",
      "onNotification" : "KEEP"
    }

响应中的id可用于删除请求：

    curl -X DELETE "https://api.blockchain.info/v2/receive/balance_update/70?key=[your-key-here]"

回复：200 OK，application/json

    { "deleted": true }

#### 1.5.阻止通知[POST]

此方法允许您在将指定高度和确认编号的新块添加到区块链时请求回调。

与余额更新请求一样，您需要将请求的通知行为指定为“保持”或“删除”。

高度是一个可选参数，指示您希望在哪个高度接收块通知 - 如果未指定，则这将是下一个块到达的高度。

Confs是另一个可选参数，指示在发送通知时块应具有多少确认。

    https://api.blockchain.info/v2/receive/block_notification

* 回调：添加与查询匹配的块时要通知的回调URL。
* 密钥：您的blockchain.info接收付款v2 api密钥。 请求API密钥。
* onNotification：请求通知行为（'KEEP'|'DELETE）。
* Confs：可选（默认值1）。 在发送通知之前块应该具有的确认数。
* 高度：可选（默认当前链高+ 1）。 应发送通知的高度。

当比特币区块链达到500,000个区块时请求单个通知：

    curl -H "Content-Type: text/plain" --data '{"key":"[your-key-here]","height":500000,"callback":"https://mysite.com/block?request_id=1234","onNotification":"DELETE"}' https://api.blockchain.info/v2/receive/block_notification

回复：200 OK，application / json

    {
      "id" : 64,
      "height" : 500000,
      "callback" : "https://mysite.com/block?request_id=1234",
      "confs" : 1,
      "onNotification" : "DELETE"
    }
    
响应中的id可用于删除请求：

    curl -X DELETE "https://api.blockchain.info/v2/receive/block_notifcation/64?key=[your-key-here]"
    
回复：200 OK，application/json

    { "deleted": true }

#### 1.6.实现回调处理（从blockchain.info发送的回调）

##### 1.6.1.接收和平衡更新回调

请注意，回调网址的长度限制为255个字符。

当生成的地址或余额更新请求监控的地址收到付款时，blockchain.info将通知您指定的回调URL。 对于余额更新回调，一旦交易达到指定的确认数量，将发送附加通知。

transaction_hash：付款交易哈希。
address：目标比特币地址（xPub帐户的一部分）。
确认：此交易的确认数量。
价值：收到的付款的价值（在satoshi，因此除以100,000,000以获得BTC的价值）。
{custom parameter}：回调URL中包含的任何参数都将传递回通知中的回调URL。 您可以使用此功能在回调网址中包含参数，例如invoice_id或customer_id，以跟踪哪些付款与您的哪些客户相关联。

##### 1.6.2.阻止通知回调

每次将新块添加到区块链时，都会发送块通知，并匹配通知请求中设置的确认的高度和数量。

hash:块哈希。
确认:该块的确认数量。
height:块高。
timestamp:指示添加块的时间的unix时间戳。
size:块大小（以字节为单位）。
{custom parameter}:回调URL中包含的任何参数都将传递回通知中的回调URL。

##### 1.6.3.PHP代码示例

    $real_secret = 'ZzsMLGKe162CfA5EcG6j';
    $invoice_id = $_GET['invoice_id']; //invoice_id is passed back to the callback URL
    $transaction_hash = $_GET['transaction_hash'];
    $value_in_satoshi = $_GET['value'];
    $value_in_btc = $value_in_satoshi / 100000000;

    //Commented out to test, uncomment when live
    if ($_GET['test'] == true) {
        return;
    }

    try {
      //create or open the database
      $database = new SQLiteDatabase('db.sqlite', 0666, $error);
    } catch(Exception $e) {
      die($error);
    }

    //Add the invoice to the database
    $stmt = $db->prepare("replace INTO invoice_payments (invoice_id, transaction_hash, value) values(?, ?, ?)");
    $stmt->bind_param("isd", $invoice_id, $transaction_hash, $value_in_btc);

    if($stmt->execute()) {
       echo "*ok*";
    }

##### 1.6.4.预期的回调响应

为了确认回调的成功处理，您的服务器应该以纯文本，无HTML来回复文本“*ok*”（无引号）。 如果服务器以其他任何方式响应，或者什么也不响应，则每个新块（大约每10分钟）将再次重新发送回调，最多1000次（1周）。 可能会阻止服务中阻止看似已死或永不返回“*ok*”响应的回调域。

#### 1.7.检查xPub地址间隙[GET]

检查支付的最后一个地址与使用checkgap端点生成的最后一个地址之间的索引间隔。 使用您要检查的xpub和API密钥，如下所示：

    curl "https://api.blockchain.info/v2/receive/checkgap?xpub=[yourxpubhere]]&key=[yourkeyhere]"
响应

    {"gap":2}

#### 1.8.回调日志[GET](调试未付款)

使用callback_logs端点查看与回调尝试相关的日志。 使用相关的确切回调和您的API密钥，如下所示：

    curl "https://api.blockchain.info/v2/receive/callback_log?callback=https%3A%2F%2Fmystore.com%3Finvoice_id%3D05892112%26secret%3DZzsMLGKe162CfA5EcG6j&key=[yourkeyhere]"

响应

    [
       {
           "callback": "https://mystore.com?invoice_id=058921123&secret=ZzsMLGKe162CfA5EcG6j&key=[yourkeyhere]",
           "called_at": "2015-10-21T22:43:47Z",
           "raw_response": "*bad*",
           "response_code": 200
       },
       {
           "callback": "http://mystore.com?invoice_id=058921123&secret=ZzsMLGKe162CfA5EcG6j&key=[yourkeyhere]",
           "called_at": "2015-10-21T22:43:55Z",
           "raw_response": "*bad*",
           "response_code": 200
       }
      ]

#### 1.9.安全

自定义密钥参数应包含在回调URL中。 当回调被触发时，秘密将被传递回回调脚本，并且应该由您的代码检查是否有效。 这可以防止有人试图打电话给你的服务器并错误地将发票标记为“付费”。

#### 1.10.货币转换

使用Exchange Rates API将本地货币的值转换为BTC。下面的内容中将会介绍

#### 1.11.双倍花费和退款

当恶意用户花费两次相同的BTC时会发生双重花费。 初始看似成功的付款可以在以后撤销。 通过等待交易被包含在区块链中并达到一些确认来抵消这种情况。 对于高价值交易，通常认为6个确认是安全的。

通过检查`$_GET['confirmations']`参数来验证回调脚本中的事务确认。 建议您在零确认时确认交易，但仅在确认后才信任该交易。 例如，如果购买产品，我们会在零确认时显示订单成功（第一次回调，但尚未回复“*ok*”），但仅在达到4次或更多次确认时才发货。 有关示例，请参阅PHP演示callback.php。

    if ($_GET['confirmations'] >= 6) {
        //Insert into confirmed payments
        echo '*ok*';
    } else {
        //Insert into pending payments
        //Don't print *ok* so the notification resent again on next confirmation
    }

#### 1.12.地址到期

接收地址永不过期，并将继续受到监视，直到在回调响应中收到“*ok*”或blockchain.info已通知回调1000次。

#### 1.13.安全使用

可以生成的接收地址数量没有限制（只要满足20地址间隙限制），该服务旨在监视数百万个地址。

可能会阻止服务中阻止看似已死或永不返回“* ok *”响应的回调域。

### 2.比特币区块链钱包API（Blockchain Wallet用户发送和接收比特币的简单API）

区块链钱包API提供了一个简单的界面，商家可以用它以编程方式与钱包进行交互。

#### 2.1.安装

要使用此API，您需要运行负责管理区块链钱包的小型本地服务。 您的应用程序通过HTTP API调用在本地与此服务进行交互。下面说安装说明

nodejs和npm是安装和使用此API服务所必需的。 安装：

    npm install -g blockchain-wallet-service
    
为获得最佳稳定性和性能，请确保始终使用最新版本。

要检查您的版本：

    blockchain-wallet-service -V

要更新到最新版本：

    npm update -g blockchain-wallet-service
    
要求

* node >= 6.0.0
* npm >= 3.0.0

#### 2.2.运行

使用下面的命令启动区块链钱包服务：

    blockchain-wallet-service start --port 3000

下面是你安装运行过程中可能遇到的问题

* 如果您收到EACCESS或与权限相关的错误，则可能需要使用sudo命令以root身份运行安装。
* 如果您收到有关node-gyp或python的错误，请使用npm install --no-optional安装
* 如果使用/usr/bin/env：node启动失败：没有这样的文件或目录，可能没有安装节点，或者安装了不同的名称（例如，Ubuntu将节点安装为nodejs）。 如果使用其他名称安装node，请为节点二进制文件创建符号链接：sudo ln -s/usr/bin/nodejs/usr/bin/node，或通过Node Version Manager安装节点。
* 如果您看到TypeError声称对象没有方法'compare'，那是因为在比较方法添加到Buffers之前，您的节点版本早于0.12。 尝试升级到至少节点版本0.12。
* 如果您获得钱包解密错误，尽管有正确的凭据，那么您可能没有安装Java，这是my-wallet-v3模块的依赖项所必需的。 在npm安装过程中没有安装Java会导致无法解密钱包。 从这里为Mac下载JDK或在基于debian的linux系统上运行apt-get install default-jdk。
* 如果您收到超时响应，可能需要您的区块链钱包的额外授权。 使用无法识别的浏览器或IP地址时可能会发生这种情况。 授权API访问尝试的电子邮件将发送给注册用户，该用户需要采取措施以授权将来的请求。

#### 2.3.创建钱包API

create_wallet方法可用于创建新的blockchain.info比特币钱包。

接口路径：http://localhost:3000/api/v2/create
请求方式：POST或者GET
参数
* password:新钱包的密码。 长度必须至少为10个字符。
* api_code:具有create wallets权限的API代码。
* priv:添加到钱包的私钥（首选钱包导入格式）。 （可选的）
* label:为钱包中的第一个地址设置的标签。 仅限字母数字。 （可选的）
* email:与新钱包关联的电子邮件，即代表您创建此钱包的用户的电子邮件地址。 （可选的）

请在此处创建API代码，包括“创建钱包”的权限。
回复：200 OK，application / json

    {
        "guid": "4b8cd8e9-9480-44cc-b7f2-527e98ee3287",
        "address": "12AaMuRnzw6vW6s2KPRAGeX53meTf8JbZS",
        "label": "Main address"
    }

#### 2.4.付款

将比特币从您的钱包发送到另一个比特币地址。 所有交易均包括0.0001BTC矿工费。

所有比特币值均为Satoshi，即除以100000000以获得BTC中的金额。 所有请求的基本URL：https://blockchain.info/merchant/$guid/  guid应替换为您的区块链钱包标识符（在登录页面上找到）。

    http://localhost:3000/merchant/$guid/payment?password=$main_password&second_password=$second_password&to=$address&amount=$amount&from=$from&fee=$fee

* main_password：主要区块链钱包密码
* second_password：如果启用了双重加密，则为您的第二个区块链钱包密码。
* to：背书比特币地址。
* amount：用satoshi发送的金额。
* from 特定比特币地址发送（可选）
* fee satoshi的交易费用价值（必须大于违约费）（可选）
回复：200 OK，application/json

    { "message" : "Response Message" , "tx_hash": "Transaction Hash", "notice" : "Additional Message" }

    { "message" : "Sent 0.1 BTC to 1A8JiWcwvpY7tAopUkSnGuEYHmzGYfZPiq" , "tx_hash" : "f322d01ad784e5deeb25464a5781c3b20971c1863679ca506e702e3e33c18e9c" , "notice" : "Some funds are pending confirmation and cannot be spent yet (Value 0.001 BTC)" }

#### 2.5.发送很多交易

将事务发送到同一事务中的多个收件人。

    http://localhost:3000/merchant/$guid/sendmany?password=$main_password&second_password=$second_password&recipients=$recipients&fee=$fee

* main_password：主区块链钱包密码
* second_password：如果启用了双重加密，则为您的第二个区块链钱包密码。
* recipients是一个JSON对象，使用比特币地址作为键，以及作为值发送的金额（见下文）。
* from特定比特币地址发送（可选）
* fee satoshi的交易费用价值（必须大于违约费）（可选）
回复：200 OK，application/json

    {
        "1JzSZFs2DQke2B3S4pBxaNaMzzVZaG4Cqh": 100000000,
        "12Cf6nCcRtKERh9cQm3Z29c9MWvQuFSxvT": 1500000000,
        "1dice6YgEVBf88erBFra9BHf6ZMoyvG88": 200000000
    }

以上示例将在同一交易中将1BTC发送到1JzSZFs2DQke2B3S4pBxaNaMzzVZaG4Cqh，15BTC到12Cf6nCcRtKERh9cQm3Z29c9MWvQuFSxvT和2BTC到1dice6YgEVBf88erBFra9BHf6ZMoyvG88。

回复：200 OK，application/json

    { "message" : "Response Message" , "tx_hash": "Transaction Hash" }

    { "message" : "Sent To Multiple Recipients" , "tx_hash" : "f322d01ad784e5deeb25464a5781c3b20971c1863679ca506e702e3e33c18e9c" }

PHP示例代码

    <?

    $guid="GUID_HERE";
    $firstpassword="PASSWORD_HERE";
    $secondpassword="PASSWORD_HERE";
    $amounta = "10000000";
    $amountb = "400000";
    $addressa = "1A8JiWcwvpY7tAopUkSnGuEYHmzGYfZPiq";
    $addressb = "1ExD2je6UNxL5oSu6iPUhn9Ta7UrN8bjBy";
    $recipients = urlencode('{
                      "'.$addressa.'": '.$amounta.',
                      "'.$addressb.'": '.$amountb.'
                   }');

    $json_url = "http://localhost:3000/merchant/$guid/sendmany?password=$firstpassword&second_password=$secondpassword&recipients=$recipients";

    $json_data = file_get_contents($json_url);

    $json_feed = json_decode($json_data);

    $message = $json_feed->message;
    $txid = $json_feed->tx_hash;

    ?>

#### 2.6.获取钱包余额

获取钱包的余额。 这应仅用作估算值，包括未经证实的交易和可能的双倍花费。

    http://localhost:3000/merchant/$guid/balance?password=$main_password

回复：200 OK，application/json

    { "balance": Wallet Balance in Satoshi }
    { "balance": 1000}

#### 2.7.列出地址

列出钱包中的所有活动地址。 还包括0确认余额，该余额仅用作估算，包括未经证实的交易和可能的双倍花费。

    http://localhost:3000/merchant/$guid/list?password=$main_password

回复：200 OK，application/json

    {
        "addresses": [
            {
                "balance": 1400938800,
                "address": "1Q1AtvCyKhtveGm3187mgNRh5YcukUWjQC",
                "label": "SMS Deposits",
                "total_received": 5954572400
            },
            {
                "balance": 79434360,
                "address": "1A8JiWcwvpY7tAopUkSnGuEYHmzGYfZPiq",
                "label": "My Wallet",
                "total_received": 453300048335
            },
            {
                "balance": 0,
                "address": "17p49XUC2fw4Fn53WjZqYAm4APKqhNPEkY",
                "total_received": 0
            }
        ]
    }


#### 2.8.获得地址的余额

检索比特币地址的余额。 按标签查询地址余额是折旧的。

    http://localhost:3000/merchant/$guid/address_balance?password=$main_password&address=$address

* main_password：主区块链钱包密码
* address：要查找的比特币地址

回复：200 OK，application/json

    {"balance" : Balance in Satoshi ,"address": "Bitcoin Address", "total_received" : Total Satoshi Received}
    {"balance" : 50000000, "address" : "19r7jAbPDtfTKQ9VJpvDzFFxCjUJFKesVZ", "total_received" : 100000000}


#### 2.9.创建一个新地址

    http://localhost:3000/merchant/$guid/new_address?password=$main_password&second_password=$second_password&label=$label

* main_password：主区块链钱包密码
* second_password：如果启用了双重加密，则为您的第二个区块链钱包密码。
* label附加到此地址的可选标签。 建议这是一个人类可读的字符串，例如 “订单号：1234”。 您可以使用此作为参考来检查订单的余额（稍后记录）

回复：200 OK，application / json

    { "address" : "The Bitcoin Address Generated" , "label" : "The Address Label"}
    { "address" : "18fyqiZzndTxdVo7g9ouRogB4uFj86JJiy" , "label":  "Order No : 1234" }

#### 2.10.地址管理

##### 2.10.1.存档地址

为了改善钱包性能，最近未使用的地址应该移动到存档状态。 它们仍将保留在钱包中，但不再包含在“列表”或“列表 - 交易”调用中。

例如，如果在支付发票后为用户生成发票，则应归档该地址。

或者，如果为每个用户生成唯一的比特币地址，则应该归档最近（~30天）未登录其地址的用户。

    http://localhost:3000/merchant/$guid/archive_address?password=$main_password&second_password=$second_password&address=$address

* main_password：主区块链钱包密码
* address：要存档的比特币地址

回复：200 OK，application / json

    {"archived" : "18fyqiZzndTxdVo7g9ouRogB4uFj86JJiy"}


##### 2.10.2.取消归档地址

    http://localhost:3000/merchant/$guid/unarchive_address?password=$main_password&second_password=$second_password&address=$address

* main_password主区块链钱包密码
* address要取消归档的比特币地址

回复：200 OK，application / json

    {"active" : "18fyqiZzndTxdVo7g9ouRogB4uFj86JJiy"}



## 四.比特币JSON-RPC接口



## 五.在线创建并发起比特交易



## 六.比特币交易离线签名

### 1.单个转账签名

    const bitcoin = require('bitcoinjs-lib');

    function bitcoinSign(privateKey, amount, utxo, sendFee, toAddress, changeAddress) {
        if(!privateKey || !amount || !utxo || !sendFee || !toAddress || !changeAddress ) {
            console.log("one of privateKey, amount, utxo, sendFee, toAddress and changeAddress is null, please give a valid param");
        } else {
            console.log("param is valid, start sign transaction");
            set = bitcoin.ECPair.fromWIF(privateKey);
            txb = new bitcoin.TransactionBuilder();
            var sendAmount = parseFloat(amount);
            var fee = parseFloat(sendFee);
            sendAmount += fee;
            console.log("Send Transaction total amount is: " + sendAmount)
            txb.setVersion(1);
            var totalMoney = 0;
            for(var i=0; i<utxo.length; i++){
                txb.addInput(utxo[i].tx_hash_big_endian, utxo[i].tx_output_n);
                totalMoney += utxo[i].value;
            }
            console.log("this address total money is: " + totalMoney)
            txb.addOutput(toAddress, sendAmount - fee);
            for(var i=0;i<utxo.length;i++){
                txb.sign(0, set);
            }
        }
        return txb.buildIncomplete().toHex();
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
    }
    var privateKey = "KwHEU8DTrY2ekGuqE6EqMMrcFj6Kdb6gWF4k8SpUeV7vDfc9c5Fn";
    var amount = 1898000;
    var utxo = bitUtxo.unspent_outputs;
    var sendFee = 1000;
    var toAddress = "12zEJohMNqSZLXH1Msxpw41ykkk3rxgx1s";
    var changeAddress = "1KSX5wmrVax3LYaB4uKUxXzCRcv5SiLDq3";
    var sign = bitcoinSign(privateKey, amount, bitUtxo.unspent_outputs, sendFee, toAddress, changeAddress);
    console.log(sign);


### 2.批量转账签名


    const bitcoin = require('bitcoinjs-lib');

    function bitcoinMultiSign(sendInfo, utxo) {
        if( !utxo || !sendInfo ) {
            console.log("one of sendInfo or utxo, is null, please give a valid param");
        } else {
            console.log("param is valid, start sign transaction");
            set = bitcoin.ECPair.fromWIF(sendInfo.privateKey);
            txb = new bitcoin.TransactionBuilder();
            var sendAmount = 0;
            console.log("Send Transaction total amount is: " + sendAmount)
            txb.setVersion(1);
            var totalMoney = 0;
            for(var i=0; i<utxo.length; i++){
                txb.addInput(utxo[i].tx_hash_big_endian, utxo[i].tx_output_n);
                totalMoney += utxo[i].value;
            }
            console.log("this address total money is: " + totalMoney)
            for(var j = 0; j < sendInfo.addressAmount.length; j++)
            {
                txb.addOutput(sendInfo.addressAmount[j].toAddress,  parseFloat(sendInfo.addressAmount[j].amount));
                sendAmount = sendAmount + sendInfo.addressAmount[j].amount;
            }
            console.log("sendAount = " + sendAmount)
            txb.addOutput(sendInfo.changeAddress, totalMoney - (sendAmount + parseFloat(sendInfo.sendFee)));
            for(var i=0;i<utxo.length;i++){
                txb.sign(0, set);
            }
        }
        return txb.buildIncomplete().toHex();
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
    }

    var sendInfo = {
        "privateKey":"KwHEU8DTrY2ekGuqE6EqMMrcFj6Kdb6gWF4k8SpUeV7vDfc9c5Fn",
        "changeAddress":"1KSX5wmrVax3LYaB4uKUxXzCRcv5SiLDq3",
        "sendFee":1000,
        "addressAmount":[
            {
                "toAddress":"12zEJohMNqSZLXH1Msxpw41ykkk3rxgx1s",
                "amount":0.00003
            },{
                "toAddress":"1KSX5wmrVax3LYaB4uKUxXzCRcv5SiLDq3",
                "amount":0.00003
            },{
                "toAddress":"12zEJohMNqSZLXH1Msxpw41ykkk3rxgx1s",
                "amount":0.001
            }
        ]
    }


    var utxo = bitUtxo.unspent_outputs;
    var sign = bitcoinMultiSign(sendInfo, bitUtxo.unspent_outputs);
    console.log(sign);


## 七.发送交易比特币主网























