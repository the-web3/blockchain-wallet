# 第八章：ERC20代币钱包开发

## 一.什么是ERC20

ERC-20是一门使用智能合约在代太坊区块链实现Tokens的技术，20是分配给此请求的号码。 在以太坊区块链上发行的绝大部分代币均符合ERC-20标准。 根据Etherscan.io 显示，截至目前为止，在以太坊主网络上共发现了147762个ERC-20代币Token。

ERC-20为更大的以太坊生态系统中的以太坊Token定义了一个通用的规则列表，允许开发人员准确预测令牌之间的交互。 这些规则包括如何在地址之间传输令牌以及如何访问每个令牌中的数据。

目前，Ether不符合ERC-20标准。 需要ERC-20合规交易的协议已经创建了包裹的以太币Token作为ETH的占位符。 这些“WETH”代币存放在一个单独的智能合约中，并以1：1的方式与以太网挂钩。

### 1.ERC-20的历史由来

ERC-20于2015年11月19日由Fabian Vogelsteller提出。 它定义了以太坊Token必须实现的常见规则列表，使开发人员能够编程新Token在以太坊生态系统中的运作方式。 ERC-20令牌标准因为部署简单，以及与其他以太坊Token标准互操作的潜力，开始受到从事数字货币产品（ICO）众筹公司的欢迎，

### 2.应用

截至目前，有超过147762份ERC-20Token合约在以太坊区块链网络上运行。最成功的ERC20Token销售包括EOS，Filecoin，Bancor，Qash和Bankex，已经筹集到大量的资金。

### 3.函数

ERC-20令牌具有以下与方法相关的功能：

获取Token代币总量

    totalSupply() public view returns (uint256 totalSupply)
     
获取具有相应合约地址上的的帐户的帐户余额

    balanceOf(address _owner) public view returns (uint256 balance) 
    
将一定数量的Token代币发送到地址给其他接收转账地址

    transfer(address _to, uint256 _value) public returns (bool success) 

从地址_from到地址_to发送_value数量的Token代币

    transferFrom(address _from, address _to, uint256 _value) public returns (bool success)

允许_spender多次退出您的帐户，最多为_value金额。 如果再次调用此函数，它将使用_value覆盖当前允许值

    approve(address _spender, uint256 _value) public returns (bool success) 

返回仍然允许_spender从_owner中退出的数量

    allowance(address _owner, address _spender) public view returns (uint256 remaining) 

#### 事件格式

转移Token时触发

    Transfer(address indexed _from, address indexed _to, uint256 _value)
    
每当批准(地址_spender,uint256 _value)被调用时触发

    Approval(address indexed _owner, address indexed _spender, uint256 _value)

### 二.ERC20代币账户地址生成

由于现在很多代币都没有在BIP44上注册相应的序号，因此现在的大多数钱包开发ERC20代币生成地址时，都使用以太坊的账户地址方式生成ERC20代币的地址。我们这里和正式钱包开发不一样，咱们将用严格的地址生成方式生成ERC20代币的地址。有关BIP44协议部分内容请看面的章节，这里不再做重复的介绍。

下面是封装好的一个生成ERC20地址的函数

    function erc20Address(seed, bipNumber, number, coinMark) {
        if(!seed || !number) {
            console.log("input param seed, coinNumber and number is null")
            return paramsErr;
        }
        var rootMasterKey = hdkey.fromMasterSeed(seed);
        var childKey = rootMasterKey.derivePath("m/44'/" + bipNumber + "'/0'/0/" +  number + "");
        var address = util.pubToAddress(childKey._hdkey._publicKey, true).toString('hex');
        var privateKey = childKey._hdkey._privateKey.toString('hex');
        var erc20Data = {coinMark:coinMark, privateKey:privateKey, address:"0x" + address}
        return erc20Data;
    }

函数说明：`seed`是随机种子的Buffer流，`bipNumber`对BIP44协议上规定的代号，`number`对应第几个账户，`0`表示第一个服务器，`coinMark`币的标识，例如`LET`。

### 三.ERC20代币转账签名

以下是封装好的一个ERC20交易签名，上述代码中包含了单个ERC20交易签名和批量ERC20交易签名，代码中使用到了ethereumjs-tx这个开源库。


    const transaction = require( 'ethereumjs-tx');

    const paramsErr = {code:1000, message:"input params is null"};

    var libErc29Sign = {};

    function addPreZero(num){
        var t = (num+'').length,
            s = '';
        for(var i=0; i<64-t; i++){
            s += '0';
        }
        return s+num;
    }

这个函数是单个ERC20代币交易签名函数，`privateKey`是私钥；`nonce`交易nonce，标识每一交易；`currentAccount`当前要转账的账户地址；`contractAddress`代币的合约地址；`toAddress`数字货币转入地址；gasPrice和gasLimit俩个相乘代表手续费；`totalAmount`转账金额, `decimal`代币单位换算。

    function ethereumErc20CoinSign(privateKey, nonce, currentAccount,  contractAddress, toAddress,  gasPrice,  gasLimit, totalAmount , decimal) {
        if(!privateKey || !nonce || !currentAccount || !contractAddress || !toAddress  || !gasPrice || !gasLimit || !totalAmount || !decimal) {
            console.log("one of param is null, please give a valid param");
            return paramsErr;
        }
        var transactionNonce = parseInt(nonce).toString(16);
        var gasLimits = parseInt(90000).toString(16);
        var gasPrices = parseFloat(gasPrice).toString(16);
        var txboPrice = parseFloat(totalAmount*(10**decimal)).toString(16)
        var txData = {
            nonce: '0x'+ transactionNonce,
            gasLimit: '0x' + gasLimits,
            gasPrice: '0x' +gasPrices,
            to: contractAddress,
            from: currentAccount,
            value: '0x00',
            data: '0x' + 'a9059cbb' + addPreZero(toAddress.substr(2)) + addPreZero(txboPrice)
        }
        var tx = new transaction(txData);
        const privateKey1 = new Buffer(privateKey, 'hex');
        tx.sign(privateKey1);
        var serializedTx = tx.serialize().toString('hex');
        return '0x'+serializedTx;
    };

下面函数是批量ERC20交易签名，下面函数将参数JSON化，参数的意义和上面的函数一致。

    function MultiEthereumErc20CoinSign(erc20SignData) {
        var outErc20Data = [];
        if(erc20SignData === null) {
            console.log("erc30SignData param is null, please give a valid param");
            return paramsErr;
        }
        var calcNonce = Number(erc20SignData.nonce);
        for (var i = 0; i < erc20SignData.signDta.length; i++){
            var transactionNonce = parseInt(calcNonce).toString(16);
            var gasLimit = parseInt(120000).toString(16);
            var gasPrice = parseFloat(erc20SignData.gasPrice).toString(16);
            var totx = parseFloat((erc20SignData.signDta[i].totalAmount)*(10**(erc20SignData.decimal))).toString(16);
            var txData = {
                nonce: '0x'+ transactionNonce,
                gasLimit: '0x' + gasLimit,
                gasPrice: '0x' +gasPrice,
                to: erc20SignData.contractAddress,
                from: erc20SignData.currentAccount,
                value: '0x00',
                data: '0x' + 'a9059cbb' + addPreZero((erc20SignData.signDta[i].toAddress).substr(2)) + addPreZero(totx)
            }
            var tx = new transaction(txData);
            const privateKey1 = new Buffer(erc20SignData.privateKey, 'hex');
            tx.sign(privateKey1);
            var serializedTx = '0x' + tx.serialize().toString('hex');
            outErc20Data = outErc20Data.concat(serializedTx)
            calcNonce = calcNonce + 1;
        }
        return { signCoin:"ERC20", signDataArr:outErc20Data}
    };


### 四.发送交易到区块链网络

此处发送交易到区块链网络和以太坊的一致，这里不再做过多的介绍。
