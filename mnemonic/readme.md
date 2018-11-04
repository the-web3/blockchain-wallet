
# 第三章：助记词

现在区块链市场上的大部分钱包，都是通过助记词来备份钱包的。当然也有不少的钱包是通过私钥来备份钱包的。不管是通过助记词备份钱包，还是痛私钥备份钱包，其实在原理上都是大同小异。一般的钱包都是通过助记词生成随机数种子，然后再通过随机数种子生成根私钥，再通过BIP分层协议生成各个币种账户的私钥。因而助记词是一个钱包的起始段，也是一个钱包的重要技术组成部分。本章咱们将详细地讲解助记词相关的知识。

## 一.助记词原理

助记词并不是一个新概念，早在很多行业，就出现了助记词的概念。发音为“ne-manik”的最纯粹形式的助记符是一种字母，单词或关联模式，可让您轻松记住信息，并已被人类使用了数千年。换句话说，它可以是一个非常有用的工具，帮助我们记住我们需要记住的重要信息。

在比特币中，助记符采用相同的基本原则，即使用一组单词生成一个独特的钱包短语，为您提供一个人类可读的单词格式，以备份您的钱包进行恢复。用于生成这些短语的助记符代码在2013年通过比特币改进提案或BIP引入比特币。BIP描述了助记符代码或助记句的实现,一组容易记忆的单词,用于生成确定性钱包。它由两部分组成：生成助记符，并将其转换为二进制种子。 该种子可以稍后用于使用BIP-0032或类似方法生成确定性钱包。

一般来说私钥都有256位，以64个字母数字构成的16进制字符串表示。备份私钥的时候直接抄录64个字符显然是很容易产生错误的，而且易读性也很差，用户体验度显然就会降低，而助记词的出现刚好解决了这个尴尬的局面，助记词是明文私钥的另一种表现形式。助记词出现的目的是为了帮助用户记忆复杂的私钥 (64位的哈希值)。助记词一般由12、15、18、21个单词构成, 这些单词都取自一个固定词库, 其生成顺序也是按照一定算法而来, 所以用户没必要担心随便输入12个单词就会生成一个地址。虽然助记词和 Keystore 都可以作为私钥的另一种表现形式, 但与 Keystore 不同的是, 助记词是未经加密的私钥, 没有任何安全性可言, 任何人得到了你的助记词, 可以不费吹灰之力的夺走你的资产。

目前钱包分为两种：非确定性钱包和确定性钱包，非确定钱包就是随机生成多个私钥，钱包管理这些私钥。这种钱包私钥不易于管理。但如果设计钱包的人水平过高的话，也可以设计得很好。确定性钱包是通过种子来生成不同账户的私钥，我们只要记住助记词就行，用助记词生成种子，再去生成钱包相关的地址和私钥。

下面我们看看种子生成的流程

.： 
    ![.： 
](https://github.com/guoshijiang/blockchain-wallet/blob/master/img/mnemonic.png)


### 1.从熵到助记词


随机生成一个128到258位的数字，叫做熵; 熵通过SHA256哈希得一个值，取前面的几位（熵长/32），记为y; 熵和y组成一个新的序列，将新序列以11位为一部分，已经预先定义2048个单词的字典做对应; 生成的有顺序的单词组就是助记词

如下图

.： 
    ![.： 
](https://github.com/guoshijiang/blockchain-wallet/blob/master/img/mnemonicone.png)

### 2.从助记词生成种子

助记词表示长度为128至256位的熵。 通过使用密钥延伸函数PBKDF2，熵被用于导出较长的（512位）种子。
PBKDF2的基本原理是通过一个伪随机函数（例如HMAC函数），把明文和一个盐值作为输入参数，然后重复进行运算，并最终产生密钥。如果重复的次数足够大，破解的成本就会变得很高。而盐值的添加也会增加“彩虹表”攻击的难度。
比特币钱包中，PBKDF2函数的第一个参数是助记词，第二个参数盐，由字符串常数“助记词”与可选的用户提供的密码字符串连接组成。使用HMAC-SHA512算法，使用2048次哈希来延伸助记符和盐参数，产生一个512位的值作为其最终输出。 这个512位的值就是种子。如图

.： 
    ![.： 
](https://github.com/guoshijiang/blockchain-wallet/blob/master/img/mnemonictwo.png)

### 3.从种子到母密钥

512位分成平均分成两部分，左边的256位为母私钥，右边的256位为链码。母私钥、链码和索引号，CKD（child key derivation)函数去从母密钥衍生出子密钥。如图

.： 
    ![.： 
](https://github.com/guoshijiang/blockchain-wallet/blob/master/img/mnemonicthree.png)


### 4.从母密钥到子密钥

母密钥、链码、索引合并在一起并且用HMAC-SHA512函数散列之后可以产生512位的散列。所得的散列可被拆分为两部分。散列右半部分的256位产出可以给子链当链码。左半部分256位散列以及索引码被加载在母私钥上来衍生子私钥。在图中，我们看到这个说明——索引集被设为0去生产母密钥的第0个子密钥（第一个通过索 引）。

.： 
    ![.： 
](https://github.com/guoshijiang/blockchain-wallet/blob/master/img/mnemonicfour.png)

### 5.扩展密钥

母密钥和链码结合叫做扩展密钥，拥有扩展私钥可以推导出子私钥，扩展公钥可以推导出子公钥。拥有扩展公钥就可以推导出子公钥，在服务器不需要母私钥也可以，这样就更安全更方便。但是还有一个问题，那就是扩展公钥包含有链码，如果子私钥被知道或者被泄漏的话，链码就可以被用来衍生所有的其他子私钥。简单地泄露的私钥以及一个母链码，可以暴露所有的子密钥。更糟糕的是，子私钥与母链码可以用来推断母私钥。
为此，HD钱包使用一种叫做硬化衍生(hardened derivation）的替代衍生函数。这就“打破”了母公钥以及子链码之间的关系。这个硬化衍生函数使用了母私钥去推导子链码，而不是母公钥。这就在母/子顺序中创造了一道“防火墙”——有链码但并不能够用来推算子链码或者姊妹私钥。强化衍生函数看起来几乎与一般的衍生的子私钥相同，不同的是母私钥被用来输入散列函数中而不是母公钥。如图

.： 
    ![.： 
](https://github.com/guoshijiang/blockchain-wallet/blob/master/img/mnemonicfive.png)


## 二.生成助记词

### 1.生成12个助记词

	var bip39 = require('bip39')
	var mnemonic = bip39.generateMnemonic()
	console.log(mnemonic);

	var bip39 = require('bip39')
	var mnemonic = bip39.generateMnemonic(128)
	console.log(mnemonic);	

### 2.生成15个助记词

	var bip39 = require('bip39')
	var mnemonic = bip39.generateMnemonic(160)
	console.log(mnemonic);	

### 3.生成18个助记词

	var bip39 = require('bip39')
	var mnemonic = bip39.generateMnemonic(192)
	console.log(mnemonic);	

### 4.生成21个助记词

	var bip39 = require('bip39')
	var mnemonic = bip39.generateMnemonic(224)
	console.log(mnemonic);	

### 5.生成24个助记词

	var bip39 = require('bip39')
	var mnemonic = bip39.generateMnemonic(256)
	console.log(mnemonic);	

### 6.生成中文助记词

	var mnemonicS = bip39.generateMnemonic(128, null, bip39.wordlists.chinese_simplified);
	console.log(mnemonicS);
以上根据传值的的大小限定助记词的个数

目前助记词库能够生成“简体中文”，“繁体中文”，“英文”，“法文”，“意大利文”，“日文”，“韩文”和西班牙文。

## 三.助记词编解码

### 1.编码助记词

	var bip39 = require('bip39')
	var mnemonic = bip39.generateMnemonic()
	var encrytMnemonic = bip39.mnemonicToEntropy(mnemonic)
	console.log(encrytMnemonic);

### 2.解码助记词

	var bip39 = require('bip39')
	var mnemonic = bip39.generateMnemonic()
	console.log(mnemonic);
	var encrytMnemonic = bip39.mnemonicToEntropy(mnemonic)
	var word = bip39.entropyToMnemonic(encrytMnemonic)
	console.log(word);

## 四.生成随机数种子

	var mnemonicE = bip39.generateMnemonic()
	console.log(mnemonicE);
	var Seed= bip39.mnemonicToSeed(mnemonicE)
	var seedHex= bip39.mnemonicToSeedHex(mnemonicE)
	console.log(Seed);
	console.log(seedHex);

## 五.验证助记词
	
	var mnemonic = bip39.generateMnemonic()
	console.log(mnemonic);
	var bool = bip39.validateMnemonic(mnemonic)

## 六.助记词开源库

    https://github.com/bitcoinjs/bip39
    
    
