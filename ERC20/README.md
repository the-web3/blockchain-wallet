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
    
每当批准`（地址_spender，uint256 _value）`被调用时触发

    Approval(address indexed _owner, address indexed _spender, uint256 _value)


