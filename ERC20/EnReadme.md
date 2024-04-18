# Chapter 8: ERC20 Token Wallet Development

## 1. What is ERC20

ERC-20 is a technology that uses smart contracts to implement Tokens on the Ethereum blockchain. 20 is the number assigned to this request. The vast majority of tokens issued on the Ethereum blockchain comply with the ERC-20 standard. According to Etherscan.io, a total of 147,762 ERC-20 tokens have been discovered on the Ethereum main network so far.

ERC-20 defines a common list of rules for Ethereum tokens within the larger Ethereum ecosystem, allowing developers to accurately predict interactions between tokens. These rules include how tokens are transferred between addresses and how the data within each token is accessed.

Currently, Ether is not ERC-20 compliant. Protocols that require ERC-20 compliant transactions have created wrapped Ethereum tokens as placeholders for ETH. These "WETH" tokens are held in a separate smart contract and are pegged to Ethereum on a 1:1 basis.

### 1. The historical origin of ERC-20

ERC-20 was proposed by Fabian Vogelsteller on November 19, 2015. It defines a list of common rules that Ethereum tokens must implement, allowing developers to program how new tokens operate within the Ethereum ecosystem. The ERC-20 token standard has become popular among companies engaged in digital currency product (ICO) crowdfunding due to its simplicity of deployment and the potential to interoperate with other Ethereum token standards.

### 2.Application

As of now, there are more than 147,762 ERC-20Token contracts running on the Ethereum blockchain network. The most successful ERC20 Token sales include EOS, Filecoin, Bancor, Qash and Bankex, which have raised significant amounts of funds.

### 3. Function

ERC-20 tokens have the following method-related features:

Get the total amount of Token tokens

     totalSupply() public view returns (uint256 totalSupply)
     
Get the account balance of the account with the corresponding contract address

     balanceOf(address _owner) public view returns (uint256 balance)
    
Send a certain amount of Token tokens to the address to other receiving transfer addresses

     transfer(address _to, uint256 _value) public returns (bool success)

Send _value amount of Token tokens from address_from to address_to

     transferFrom(address _from, address _to, uint256 _value) public returns (bool success)

Allow _spender to exit your account multiple times, up to _value amount. If this function is called again it will overwrite the currently allowed value with _value

     approve(address _spender, uint256 _value) public returns (bool success)

Returns the number of times _spender is still allowed to exit from _owner

     allowance(address _owner, address _spender) public view returns (uint256 remaining)

#### Event format

Triggered when transferring Token

     Transfer(address indexed _from, address indexed _to, uint256 _value)
    
Triggered whenever approve(address_spender, uint256 _value) is called

     Approval(address indexed _owner, address indexed _spender, uint256 _value)

### 2. ERC20 token account address generation

Since many tokens do not have corresponding serial numbers registered on BIP44, most current wallets use the Ethereum account address method to generate addresses for ERC20 tokens when developing ERC20 tokens. Our development here is different from the formal wallet development. We will use a strict address generation method to generate the address of the ERC20 token. Please refer to the previous chapters for part of the BIP44 protocol, and I will not repeat the introduction here.

The following is an encapsulated function that generates an ERC20 address

     function erc20Address(seed, bipNumber, number, coinMark) {
         if(!seed || !number) {
             console.log("input param seed, coinNumber and number is null")
             return paramsErr;
         }
         var rootMasterKey = hdkey.fromMasterSeed(seed);
         var childKey = rootMasterKey.derivePath("m/44'/" + bipNumber + "'/0'/0/" + number + "");
         var address = util.pubToAddress(childKey._hdkey._publicKey, true).toString('hex');
         var privateKey = childKey._hdkey._privateKey.toString('hex');
         var erc20Data = {coinMark:coinMark, privateKey:privateKey, address:"0x" + address}
         return erc20Data;
     }

Function description: `seed` is the Buffer stream of random seeds, `bipNumber` is the code specified in the BIP44 protocol, `number` corresponds to the account number, `0` represents the first server, `coinMark` is the identifier of the coin, for example `LET`.

### 3. ERC20 token transfer signature

The following is a packaged ERC20 transaction signature. The above code contains a single ERC20 transaction signature and a batch ERC20 transaction signature. The open source library ethereumjs-tx is used in the code.


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

This function is a single ERC20 token transaction signature function. `privateKey` is the private key; `nonce` transaction nonce identifies each transaction; `currentAccount` is the current account address to be transferred; `contractAddress` is the contract address of the token; `toAddress `Digital currency transfer address; the multiplication of gasPrice and gasLimit represents the handling fee; `totalAmount` transfer amount, `decimal` token unit conversion.

     function ethereumErc20CoinSign(privateKey, nonce, currentAccount, contractAddress, toAddress, gasPrice, gasLimit, totalAmount, decimal) {
         if(!privateKey || !nonce || !currentAccount || !contractAddress || !toAddress || !gasPrice || !gasLimit || !totalAmount || !decimal) {
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

The following function is a batch ERC20 transaction signature. The following function converts the parameters into JSON. The meaning of the parameters is the same as the above function.

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
             var gasPrice = parseFloat(erc20SignData.gasPrice).toString(16);var totx = parseFloat((erc20SignData.signDta[i].totalAmount)*(10**(erc20SignData.decimal))).toString(16);
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


### 4. Send transactions to the blockchain network

The transaction sent here is consistent with the blockchain network and Ethereum, and I won’t go into too much introduction here.
