# Chapter 5: Ethereum wallet development

Among the current public chain projects, the most influential are probably Ethereum and Bitcoin. Most other public chains are basically designed and developed based on the Ethereum and Bitcoin public chain projects. Anyone who understands blockchain knows that the gap between the two public chain projects of Bitcoin and Ethereum is quite large, so their wallet development is also very different. In this chapter, we will explain the principles and development process of Ethereum wallet in detail. The contents involved are as follows:

* Develop wallets based on the wallet node method, but the disadvantage of this method is that the keystore is generated and stored on the node of the block;

* Non-deterministic Ethereum wallet development to realize local storage of private keys, but each account corresponds to a private key, and the management of private keys is difficult;

* Hierarchical deterministic Ethereum wallet development process, realizing local storage, multi-chain multi-account and private key correlation wallet.


## 1. Introduction to Ethereum

### 1. What is Ethereum?

Ethereum is an open blockchain platform that allows anyone to build and use decentralized applications using blockchain technology. Like Bitcoin, no one controls or owns Ethereum, it is an open source project built by many people around the world. But unlike the Bitcoin protocol, Ethereum is designed to be adaptable and flexible. Creating new applications on the Ethereum platform is easy and anyone can use them safely. The ecosystem based on Ethereum is becoming more and more powerful. Currently, there are nearly 800 companies issuing tokens on Ethereum. Digital assets with relatively high quality account for more than 20% of the token volume. Ethereum-based digital currency assets Development has directly led to an increasing amount of capital entering.

### 2. The background of Ethereum

Blockchain technology is the technical foundation of Bitcoin, first described by its mysterious author Satoshi Nakamoto in the 2008 white paper "Bitcoin: A Peer-to-Peer Electronic Cash System." Although the use of blockchain for broader purposes was already discussed in the original paper, it was not until several years later that blockchain technology became a common term. Blockchain is a distributed computing architecture in which each network node performs and records the same transactions, which are grouped into blocks. Only one block can be added at a time, and each block contains a mathematical proof that verifies that it sequentially follows the previous block. In this way, the blockchain’s “distributed database” remains consistent across the entire network. Individual user interactions with the ledger (transactions) are protected by strong cryptography. The nodes that maintain and validate the network are motivated by mathematically enforced economic incentives encoded into the protocol.

In the case of Bitcoin, a distributed database is envisioned as a table of account balances, a ledger, and transactions are transfers of Bitcoin tokens to facilitate trustless financing between individuals. But as Bitcoin began to attract more attention from developers and technologists, new projects began to use the Bitcoin network for purposes other than the transfer of value tokens. Many of these take the form of "alternative coins" - separate blockchains with their own cryptocurrencies that improve upon the original Bitcoin protocol to add new features or functionality. In late 2013, Ethereum inventor Vitalik Buterin proposed that a blockchain capable of being reprogrammed to perform arbitrarily complex calculations could encompass these and many other projects.

In 2014, Ethereum founders Vitalik Buterin, Gavin Wood, and Jeffrey Wilcke began working on a next-generation blockchain that promised to implement a fully trustless universal smart contract platform.

### 3. Ethereum Virtual Machine

Ethereum is a programmable blockchain. Rather than providing users with a predefined set of operations (such as Bitcoin transactions), Ethereum allows users to create their own operations of any complexity they wish. In this way, it can serve as a platform for many different types of decentralized blockchain applications, including but not limited to cryptocurrencies.

Ethereum in a narrow sense refers to a set of protocols that define a platform for decentralized applications. At its core is the Ethereum Virtual Machine (“EVM”), which can execute code of arbitrary algorithmic complexity. In computer science terms, Ethereum is "Turing complete." Developers can create applications that run on the EVM using friendly programming languages ​​modeled after existing languages ​​such as JavaScript and Python.

Like any blockchain, Ethereum includes a peer-to-peer network protocol. The Ethereum blockchain database is maintained and updated by many nodes connected to the network. Each node of the network runs the EVM and executes the same instructions. For this reason, Ethereum is sometimes described as the "world computer."

Massive computation parallelization across the Ethereum network is not intended to improve computational efficiency. In effect, this process makes calculations on Ethereum much slower and more expensive than traditional "computers." Instead, every Ethereum node runs the EVM to maintain consistency across the blockchain. Decentralized consensus provides Ethereum with extreme fault tolerance, ensuring zero downtime and making data stored on the blockchain immutable and auditable.

The Ethereum platform itself is featureless or irrelevant to value. Similar to programming languages, it is up to entrepreneurs and developers to decide what should be used. However, it's clear that certain application types are more beneficial than Ethereum's capabilities. Specifically, Ethereum is suitable for applications that automate direct interactions between peers or facilitate coordinated group actions across the network. For example, applications for coordinating peer-to-peer markets, or the automation of complex financial contracts. Bitcoin allows individuals to exchange cash without involving any middlemen such as financial institutions, banks or governments. Ethereum’s impact may be even more profound. In theory, financial interactions or exchanges of any complexity can be performed automatically and reliably using code running on Ethereum. Beyond financial applications, any environment where trust, security, and durability are important (e.g., asset registries, voting, governance, and the Internet of Things) are likely to be severely impacted by the Ethereum platform.

### 4. Working mechanism of Ethereum

Ethereum incorporates many features and technologies familiar to Bitcoin users, while also introducing many of its own modifications and innovations.

While the Bitcoin blockchain is purely a list of transactions, the basic unit of Ethereum is an account. The Ethereum blockchain tracks the state of each account, and all state transitions on the Ethereum blockchain are transfers of value and information between accounts. There are two types of accounts:

* Externally owned account (EOA), controlled by private key
* Contract account, controlled by contract code, can only be "activated" by EOA

For most users, the basic difference between these users is that human users control the EOA because they control the private keys that control the EOA. Contract accounts, on the other hand, are managed by their internal code. If they are "controlled" by a human user, it is because they are programmed to be controlled by an EOA with a specific address, which in turn is controlled by whoever holds the private key that controls that EOA. The popular term "smart contract" refers to the code in a contract account - the program that is executed when a transaction is sent to that account. Users can create new contracts by deploying code to the blockchain.

Contract accounts only perform operations when instructed by EOA. Therefore, it is not possible for a contract account to perform native operations (such as random number generation or API calls), which can only be performed when prompted by an EOA. This is because Ethereum requires nodes to be able to agree on calculation results, which requires strict deterministic execution.

As with Bitcoin, users must pay small transaction fees to the network. This protects the Ethereum blockchain from boring or malicious computing tasks, such as DDoS attacks or infinite loops. The sender of a transaction must pay for every step of the "program" they activate, including computation and memory storage. These fees are paid in amounts of Ether tokenized in Ethereum's native value.

These transaction fees are collected by the nodes of the validating network. These "miners" are the nodes in the Ethereum network that receive, propagate, verify and execute transactions. Miners then group transactions, which include many updates to an account's "state" in the Ethereum blockchain, into what are called "blocks," and miners then compete with each other to make their block the next one to be added. Blockchain. For every successful block they mine, miners are rewarded with ether. This provides a financial incentive for people to dedicate ether and electricity to the Ethereum network.

Just like in the Bitcoin network, miners are tasked with solving complex mathematical problems in order to successfully "mine" a block. This is called "proof of work". Any computational problem that requires orders of magnitude more resources to solve algorithmically than to verify the solution is a good candidate for proof of work. In order to prevent centralization due to the use of specialized hardware (such as ASICs), as occurs in the Bitcoin network, Ethereum chose the memory hard computing problem. If the problem requires memory and a CPU, the ideal hardware is actually a general-purpose computer. This makes Ethereum’s proof-of-work ASIC resistant, allowing for more secure decentralized security than blockchains dominated by specialized hardware like Bitcoin.

## 2. Terminology related to Ethereum wallet

### 1.gas, Gas Limit and Gas Price

On Ethereum, sending tokens or calling smart contracts to perform write operations on the blockchain requires paying miner computing fees. The billing is calculated in Gas, and Gas is paid in ETH. Computational charges apply regardless of whether your called method succeeds or fails. Even if it fails, the miner verifies and executes your transaction (computation), so the miner fee must be paid the same as for a successful transaction.

Simply put, Gas Limit is equivalent to how much gasoline a car needs to fill, and Gas Price is equivalent to the price per liter of gasoline.

Gas Limit is called a limit because it is the maximum amount of Gas you are willing to spend on a transaction. The Gas required for a transaction is defined by how much code is executed by calling the smart contract. If you don't want to spend too much gas, lowering the Gas Limit won't help much. Because you must include enough Gas to pay for the computing resources, otherwise the transaction will fail due to insufficient Gas, and all the Gas limits you set will also be consumed. It is recommended that you set the Gas Limit as high as possible if you have sufficient ETH. All unused Gas will be returned to you at the end of the transfer.

Miner fees can be saved by lowering the Gas Price, but it will also slow down the miner's packaging speed. Miners will give priority to packaging transactions with a high Gas Price setting. If you want to speed up transfers, you can set the Gas Price higher so that you can be queued higher. If you are not in a hurry, you only need to set a safe Gas Price and the miner will also package your transaction.

Check the lowest Gas Price accepted by miners: https://etherscan.io/gastracker

.：
     ![.：
](https://github.com/guoshijiang/blockchain-wallet/blob/master/img/gas.png)


### 2. Ethereum unit

The smallest unit of Ethereum is wei, and the most commonly used unit is ether.

* Kwei (Babbage) = 10 to the third power Wei
* Mwei (Lovelace) = 10 to the 6th power Wei
*Gwei (Shannon) = 10 to the 9th power Wei
*MicroEther (Szabo) = 10 to the 12th power Wei
* MilliEther (Finney) = 10 to the 15th power Wei
*Ether = 10 to the 18th power Wei

### 3.keystore

Keystore is a representation of an Ethereum account, which contains a series of information such as the address of the Ethereum account, the private key and MAC address of the account ciphertext, etc. The following is the complete information of a keystore.

     {
       "address":"2ffe6e3816b6a84f509ea3be1b8e8bb024c894d8",
       "crypto":
       {
         "cipher":"aes-128-ctr",
         "ciphertext":"c302e468ad640ad6c43d51754caa60964ece820ad98ad0d5aa72a785a93d7a59",
         "cipherparams": {"iv":"f4609a583b28e48a6bda7b6bf1229a26"},
         "mac":"ef8872a3ad0a92a410b15b0e2e662d5cbfc98360d72b190a7b3189bd4151ebcf",
         "kdf":"pbkdf2",
         "kdfparams":
         {
           "c":262144,
           "dklen":32,
           "prf":"hmac-sha256",
           "salt":"5a757ae33c08a46c8a50f34a2a514503fd66481ea56aeab31e25da45ae3f1c39"
         }
      },
      "id":"49a53e88-4f8a-4858-80a4-02ad230da1d3",
      "version":3
     }

### 4. Mining fees

To transfer money or call a smart contract and perform a write operation on the blockchain, you need to pay the miner's calculation fee. The billing is calculated according to Gas. Gas is paid with ETH. For details, please see Part 1 above.

## 3. Open source libraries involved in Ethereum wallet

### 1.keythereum

Keythereum is a JavaScript tool for generating, importing and exporting Ethereum keys. This provides an easy way to use the same account in local and web wallets. It can be used in both verifiable and storage wallets.

Keythereum uses the same key derivation function (PBKDF2-SHA256 or scrypt), symmetric cipher (AES-128-CTR or AES-128-CBC) and message authentication code as geth. You can export the generated key to a file, copy it to the keystore in the data directory, and immediately start using it in your local Ethereum client.

As of version 0.5.0, both keythereum's encryption and decryption functions return Buffers instead of strings. This is a significant change for those who use these features directly.

### 1.1. Use keythereum to produce keystore

Before producing keystore, you must have a nodeJs environment and install keythereum

     npm install keythereum
    
Or use the compressed browser file dist/keythereum.min.js for use in the browser. Use the following code to import

     <script src="dist/keythereum.min.js" type="text/javascript"></script>

Generate a new random private key (256 bits), along with the salt (256 bits) used by the key derivation function, and the initialization vector (128 bits) for AES-128-CTR to encrypt the key. If a callback function is passed, create is asynchronous, otherwise it is synchronous.

The following is the code to generate keystore:

     var keythereum = require("keythereum");

     var params = { keyBytes: 32, ivBytes: 16 };
     var dk = keythereum.create(params);
     keythereum.create(params, function (dk) {
         var password = "wheethereum";
         var kdf = "pbkdf2";
         var options = {
             kdf: "pbkdf2",
             cipher: "aes-128-ctr",
             kdfparams: {
                 c: 262144,
                 dklen: 32,
                 prf: "hmac-sha256"
             }
         };
         keythereum.dump(password, dk.privateKey, dk.salt, dk.iv, options, function (keyObject) {
             console.log(keyObject);
         });
     });

The following figure is the running result:

.：
     ![.：
](https://github.com/guoshijiang/blockchain-wallet/blob/master/img/keystoreone.png)

### 1.2. Store keystore in file

dump creates an object instead of a JSON string. In Node, the exportToFile method provides an easy way to export this formatted key object to a file. It creates a JSON file in the keystore subdirectory and uses geth's current file naming convention (ISO timestamp concatenated with the Ethereum address from which the key was derived).

code show as below:

     var keythereum = require("keythereum");

     var params = { keyBytes: 32, ivBytes: 16 };
     var dk = keythereum.create(params);
     keythereum.create(params, function (dk) {
         var password = "wheethereum";
         var kdf = "pbkdf2";
         var options = {
             kdf: "pbkdf2",
             cipher: "aes-128-ctr",
             kdfparams: {
                 c: 262144,
                 dklen: 32,
                 prf: "hmac-sha256"
             }
         };
         keythereum.dump(password, dk.privateKey, dk.salt, dk.iv, options, function (keyObject) {
             keythereum.exportToFile(keyObject);
         });
     });

After success, you will see the following information in your keystor directory. If an error occurs, the most likely reason is that there is no keystore directory in your directory. Of course, you can also specify the keystore storage directory in the above code.

.：
     ![.：
](https://github.com/guoshijiang/blockchain-wallet/blob/master/img/1541492232(1).png)


### 1.3.Keystore import

Importing keys from geth's keystore can only be done on Node. Parse the JSON file into an object with the same structure as keyObject above.

     var datadir = "/home/jack/.ethereum-test";
     var keyObject = keythereum.importFromFile(address, datadir);
     console.log(keyObject)
     keythereum.importFromFile(address, datadir, function (keyObject) {
        console.log(keyObject)
     });

### 1.4.Restore private key from keystore

The private key recovered here is in buffer format, password is the password you set, and keyObject is keystore.

     var privateKey = keythereum.recover(password, keyObject);
     console.log(privateKey)
     keythereum.recover(password, keyObject, function (privateKey) {
       console.log(privateKey)
     });

### 2.ethereumjs-tx

ethereumjs-tx is a javascript library used to sign transactions for Ethereum or ERC20 tokens.

### 2.1. Install ethereumjs-tx

     npm install ethereumjs-tx
   
### 2.2. Use ethereumjs-tx library to sign transactions

#### 2.2.1. Ethereum transaction signature

     const Web3 =require ('web3')
     const transaction = require('ethereumjs-tx');
     if (typeof web3 !== 'undefined')
     {
         var web3 = new Web3(web3.currentProvider);
     } else {
         var web3 = new Web3(new Web3.providers.HttpProvider("http://localhost:8545"));
     }

     function ethereumSign(privateKey, nonce, toAddress, sendToBalance, sendFee) {
         if(!privateKey || !nonce || !toAddress || !sendToBalance || !sendFee) {
             console.log("one of fromAddress, toAddress, sendToBalance, sendFee is null, please give a valid param");
         } else {
             console.log("param is valid, start sign transaction");
             var transactionNonce = parseInt(nonce).toString(16);
             console.log("transaction nonce is " + transactionNonce);
             var numBalance = parseFloat(sendToBalance);
             var balancetoWei = web3.toWei(numBalance, "ether");
             console.log("balancetoWei is " + balancetoWei);
             var oxNumBalance = parseInt(balancetoWei).toString(16);
             console.log("16 oxNumBalance is " + oxNumBalance);
             var gasFee = parseFloat(sendFee);
             var gasFeeToWei = web3.toWei(gasFee, "ether");
             console.log("gas fee to wei is " + gasFeeToWei);
             var gas = gasFeeToWei / 21000;
             console.log("param gas is " + gas);
             var oxgasFeeToWei = parseInt(gas).toString(16);
             console.log("16 oxgasFeeToWei is " + oxgasFeeToWei);
             var privateKeyBuffer = Buffer.from(privateKey, 'hex');
             var rawTx = {
                 nonce:'0x' + transactionNonce,
                 gasPrice: '0x5208',
                 gas:'0x4a817c800', //+ oxgasFeeToWei,
                 to: '0x' + toAddress,
                 value:'0x' + oxNumBalance,
                 data: '',
                 chainId: 1
             };
             var tx = new transaction(rawTx);
             tx.sign(privateKeyBuffer);var serializedTx = tx.serialize();
             if(serializedTx == null) {
                 console.log("Serialized transaction fail")
             } else {
                 console.log("Serialized transaction success and the result is " + serializedTx.toString('hex'));
                 console.log("The sender address is " + tx.getSenderAddress().toString('hex'));
                 if (tx.verifySignature()) {
                     console.log('Signature Checks out!')
                 } else {
                     console.log("Signature checks fail")
                 }
             }
         }
         return '0x' + serializedTx.toString('hex')
     }

     var privateKey = "5204627f8e58b3c75be408423c121988a1cb942e73470fb19186ef8986cd50b3";
     var nonce = 10;
     var toAddress = "c6328b3a137b3be3f01c35ecda4ecda375be7fdf";
     var sendToBalance = "0.003";
     var sendFee = "0.001";

     var sign = ethereumSign(privateKey, nonce, toAddress, sendToBalance, sendFee);
     console.log(sign);
    
The signature result is as follows:

.：
     ![.：
](https://github.com/guoshijiang/blockchain-wallet/blob/master/img/ethSign.png)

#### 2.2.2.ERC20 token transaction signature

     const Web3 =require ('web3')
     const transaction = require('ethereumjs-tx');

     function addPreZero(num){
         var t = (num+'').length,
             s = '';
         for(var i=0; i<64-t; i++){
             s += '0';
         }
         return s+num;
     }

     function ethereumErc20CoinSign(privateKey/*private key*/, nonce/*identify transaction*/, currentAccount/*current account*/, contractAddress/*contract address*/, toAddress/*transfer address*/, gasPrice/*gasPrice*/ , gasLimit/*gasLimit*/, totalAmount/*Total amount of tokens*/, decimal /*Unit conversion of tokens*/) {
         if(!privateKey || !nonce || !currentAccount || !contractAddress || !toAddress || !gasPrice || !gasLimit || !totalAmount || !decimal) {
             console.log("one of param is null, please give a valid param");
             return ;
         }
         var transactionNonce = parseInt(nonce).toString(16);
         console.log("transaction nonce is " + transactionNonce);
         var gasLimit = parseInt(gasLimit).toString(16);
         console.log("send transaction gasLimit is " + gasLimit);
         var gasPrice = parseFloat(gasPrice).toString(16);
         console.log("send transaction gasPrice is " + gasPrice)
         var totx = parseFloat(totalAmount*(10**decimal)).toString(16); //
         var txData = {
             nonce: '0x'+ transactionNonce,
             gasLimit: '0x' + gasLimit,
             gasPrice: '0x' +gasPrice,
             to: contractAddress,
             from: currentAccount,
             value: '0x00',
             data: '0x' + 'a9059cbb' + addPreZero(toAddress) + addPreZero(totx)
         }
         var tx = new transaction(txData);
         const privateKey1 = new Buffer(privateKey, 'hex');
         tx.sign(privateKey1);
         var serializedTx = tx.serialize().toString('hex');
         // console.log("transaction sign result is " + serializedTx);
         return serializedTx; /*signature string*/
     }

     var privateKey = "5204627f8e58b3c75be408423c121988a1cb942e73470fb19186ef8986cd50b3";
     var nonce = "9";
     var currentAccount = "0xe558be4e90b2ac96ae5cad47dc39cd08316f2e57";
     var contractAddress = "0xfa3118b34522580c35ae27f6cf52da1dbb756288";
     var toAddress = "c6328b3a137b3be3f01c35ecda4ecda375be7fdf";
     var gasPrice = 400000000;
     var gasLimit = 200000;
     var totalAmount = 8;
     var decimal = 6;
     var sign = ethereumErc20CoinSign(privateKey, nonce, currentAccount, contractAddress, toAddress, gasPrice, gasLimit, totalAmount, decimal);

     console.log(sign);

The signature result is as follows:

.：
     ![.：
](https://github.com/guoshijiang/blockchain-wallet/blob/master/img/ERC20Sign.png)


### 2.web3 library

web3 is an important library for Ethereum development, currently including web3.js, web3j (web3java), web3.py (web3python), hs-web3 (haskell), web3j-scala, purescript-web3, web3.php, ethereum-php; We mainly introduce web3.js here, and the others will be introduced in a chapter later. web3.js is an Ethereum-compatible JavaScript API that implements the Common JSON RPC specification. It is available on npm as a node module, Bower and components as embeddable scripts, and as a meteor.js package.

#### 2.1.Installation of web3.js library

##### 2.1.1.nodejs installation

     npm install web3
    
##### 2.1.2.yarn installation

     yarn add web3
    
##### 2.1.3.Meteor.js installation

     meteor add ethereum:web3
 
##### 2.1.4. As a browser module

* CDN

     <script src="https://cdn.jsdelivr.net/gh/ethereum/web3.js/dist/web3.min.js"></script>

*Bower

     bower install web3
    
*Component

     component install ethereum/web3.js
    
#### 2.2.Usage of web3.js

##### 2.2.1. Use web3 to interact with Ethereum

web3.js initialization settings Provider

     var Web3 = require("web3");
    
     if (typeof web3 !== 'undefined')
     {
         web3 = new Web3(web3.currentProvider);
     }
     else
     {
         web3 = new Web3(new Web3.providers.HttpProvider("http://10.23.1.209:8545"));
     }

##### 2.2.2. Get account balance

     web3.eth.getBalance("0x68db18a9cd87403c39e84467b332195b43fc33b5", function(err, result)
     {
         if (err == null)
         {
             console.log('~balance Ether:' +web3.fromWei(result, "ether"));
         }
         else
         {
             console.log('~balance Ether:' + web3.fromWei(result, "ether"));
         }
     });

##### 2.2.3. Obtain the nonce of the transaction

     web3.eth.getTransactionCount("0x68db18a9cd87403c39e84467b332195b43fc33b5", function (err, result)
     {
         if (err == null)
         {
             console.log('nonce:' + result);
         }
         else
         {
             console.log('nonce:' + result);
         }
     });

##### 2.2.4. Initiate transfer

Transfers need to use the ethereumjs-tx library to sign transactions. For the ethereumjs-tx library, please refer to the above.

     var Tx = require('ethereumjs-tx');
     var privateKey = new Buffer.from('0f8e7b1b99f49d1d94ac42084216a95fe5967caec6bba35c62c911f6c4eafa95', 'hex')

     var rawTx = {
         subId:'0x0000000000000000000000000000000000',
         nonce: '0x1',
         gasPrice: '0x1a13b8600',
         gas: '0xf4240',
         to: '0x82aeb528664bb153d2114ae7ca4ef118ef1e7a98',
         value: '0x8ac7230489e80000',
         data:'0xd46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f072445675',
         //chainId:"10"
     };

     var tx = new TX(rawTx);
     tx.sign(privateKey);

     var serializedTx = tx.serialize();

     if (tx.verifySignature())
     {
         console.log('Signature Checks out!')
     }

     web3.eth.sendRawTransaction('0x' + serializedTx.toString('hex'), function(err, hash) {
         if (!err)
         {
             console.log("hash:" + hash); // "0x7f9fade1c0d57a7af66ab4ead79fade1c0d57a7af66ab4ead7c2c2eb7b11a91385"
         }
         else
         {
             console.log(err);
         }
     });

The above is the use of Web3js. web3 is a powerful SDK, and its functions are far more than these. web3.js will also be used in later articles, and we will explain the corresponding content when used.

## 4. Introduction to Ethereum Browser API (some of these APIs are needed in wallet development)

Etherscan’s API address: https://etherscan.io/apis

### 1 Introduction

Etherscan's Ethereum developer API is a service provided by the community without reservation, and you can use the interface you need on it. The request method supported by these interfaces is GET/POST request, and can only send 5 requests per second. To use the API service, create a free Api-Key token within the ClientPortal->MyApiKey area, which you can then use for all api requests.

### 2.Etherscan account system API

* Get the account balance of a single address

         https://api.etherscan.io/api?module=account&action=balance&address=0xddbd2b932c763ba5b1b7ae3b362eac3e8d40121a&tag=latest&apikey=YourApiKeyToken

* Get the account balances of multiple addresses in one call

         https://api.etherscan.io/api?module=account&action=balancemulti&address=0xddbd2b932c763b a5b1b7ae3b362eac3e8d40121a,0x63a9975ba31b0b9626b34300f7f627147df1f526,0x198ef1ec325a96cc 354c7266a038be8b5c558f67&tag=latest&apikey=YourApiKeyToken
    
Separate addresses with commas and include up to 20 accounts at a time
    
* Get a list of account transactions based on address

Parameters: startblock: start block, search results based on the starting block; endblock: end block, search results based on the ending block

         http://api.etherscan.io/api?module=account&action=txlist&address=0xddbd2b932c763ba5b1b7ae3b362eac3e8d40121a&startblock=0&endblock=99999999&sort=asc&apikey=YourApiKeyToken

([BETA] Returns 'isError' value: 0 = no error, 1 = got error) (up to the last 10000 transactions returned)

or

         https://api.etherscan.io/api?module=account&action=txlist&address=0xddbd2b932c763ba5b1b7ae3b362eac3e8d40121a&startblock=0&endblock=99999999&page=1&offset=10&sort=asc&apikey=YourApiKeyToken

(To get paginated results, use page =<page number> and offset =<return maximum number of records>)

* Get the contract transaction list based on the address

Parameters: startblock: start block, search results based on the starting block; endblock: end block, search results based on the ending block

         http://api.etherscan.io/api?module=account&action=txlistinternal&address=0x2c1ba59d6f58433fb1eaee7d20b26ed83bda51a3&startblock=0&endblock=2702578&sort=asc&apikey=YourApiKeyToken
        
([BETA] Returns 'isError' value: 0 = no error, 1 = got error) (up to the last 10000 transactions returned)

or

         https://api.etherscan.io/api?module=account&action=txlistinternal&address=0x2c1ba59d6f58433fb1eaee7d20b26ed83bda51a3&startblock=0&endblock=2702578&page=1&offset=10&sort=asc&apikey=YourApiKeyToken

(To get paginated results, use page =<page number> and offset =<return maximum number of records>)

* Get internal transactions based on transaction Hash

         https://api.etherscan.io/api?module=account&action=txlistinternal&txhash=0x40eb908387324f2b575b4879cd9d7188f69c8fc9d87c901b9e2daaea4b442170&apikey=YourApiKeyToken
        
Returns 'isError' value: 0=OK, 1=deny/cancel
Only the last 10,000 transactions are returned

* Get the "ERC20-Token transfer event" list by address

Optional parameters: startblock: search results based on the starting block number, endblock: search results based on the end block

         http://api.etherscan.io/api?module=account&action=tokentx&address=0x4e83362442b8d1bec281594cea3050c8eb01311c&startblock=0&endblock=999999999&sort=asc&apikey=YourApiKeyToken
        
Only the last 10,000 transactions are returned

or

         https://api.etherscan.io/api?module=account&action=tokentx&contractaddress=0x9f8f72aa9304c8b593d555f12ef6589cc3a579a2&page=1&offset=100&sort=asc&apikey=YourApiKeyToken

To get paginated results, use page=<page number> and offset=<return maximum records>

or

         https://api.etherscan.io/api?module=account&action=tokentx&contractaddress=0x9f8f72aa9304c8b 593d555f12ef6589cc3a579a2&address=0x4e83362442b8d1bec281594cea3050c8eb01311c&page=1&offset=1 00&sort=asc&apikey=YourApiKeyToken
        
To get transfer events for a specific Token contract, include the contract address parameter


* Get list of Blocks Mined by Address
* Get the list of mined blocks by address

         https://api.etherscan.io/api?module=account&action=getminedblocks&address=0x9dd134d14d1e65f84b706d6f205cd5b1cd03a46b&blocktype=blocks&apikey=YourApiKeyToken

or

         https://api.etherscan.io/api?module=account&action=getminedblocks&address=0x9dd134d14d1e65f84b706d6f205cd5b1cd03a46b&blocktype=blocks&page=1&offset=10&apikey=YourApiKeyToken
        
To get paginated results, use page =<page number> and offset =<maximum number of records returned>

### 3.Contract API

Newly verified contracts will be synced to the API server in 5 minutes or less

The code here will involve the code for web3.js to call smart contracts. If you are interested, you can take a closer look.

* Obtain the contract ABI of the verified contract source code

         https://api.etherscan.io/api?module=contract&action=getabi&address=0xBB9bc244D798123fDe783fCc1C72d3Bb8C189413&apikey=YourApiKeyToken
        
A simple example for retrieving contractABI to interact with a contract using Web3.js and Jquery

         var Web3 = require('web3');
         var web3 = new Web3(new Web3.providers.HttpProvider());
         var version = web3.version.api;

         $.getJSON('http://api.etherscan.io/api?module=contract&action=getabi&address=0xfb6916095ca1df60bb79ce92ce3ea74c37c5d359', function (data) {
             var contractABI = "";
             contractABI = JSON.parse(data.result);
             if (contractABI != ''){
                 var MyContract = web3.eth.contract(contractABI);
                 var myContractInstance = MyContract.at("0xfb6916095ca1df60bb79ce92ce3ea74c37c5d359");
                 var result = myContractInstance.memberId("0xfe8ad7dd2f564a877cc23feea6c0a9cc2e783715");
                 console.log("result1 : " + result);
                 var result = myContractInstance.members(1);
                 console.log("result2 : " + result);
             } else {
                 console.log("Error" );
             }
         });
        
* Obtain the contract source code of the verified contract source code

         https://api.etherscan.io/api?module=contract&action=getsourcecode&address=0xBB9bc244D798123fDe783fCc1C72d3Bb8C189413&apikey=YourApiKeyToken
        
[BETA]Verify source code

1. A valid Etherscan API key is required, otherwise it will be rejected
2. The daily limit of submissions per user is 100 per day (subject to change)
3. Due to the maximum transfer size limit of http get, only HTTP post is supported.
4. Supports up to 10 different library pairs
5. Contracts using "Import" will require concatenating the code into one file, as we do not support "Import" in a separate file. You can try using Blockcat solidity-flattener or SolidityFlattery
6. List of supported solc versions, only solc version v0.4.11 and higher is supported. Explosion proof. v0.4.25+ commit.59dbf8f1
7. Upon successful submission, you will receive a GUID (50 characters) as receipt.
8. You can use this GUID to track the status of your submission
9. The verified source code will be displayed in contractsRerified


Source code submission Gist (guid returned as part of result on success):

         $.ajax({
             type: "POST", //Only POST supported
             url: "//api.etherscan.io/api", //Set to the correct API url for Other Networks
             data: {
                 apikey: $('#apikey').val(), //A valid API-Key is required
                 module: 'contract', //Do not change
                 action: 'verifysourcecode', //Do not change
                 contractaddress: $('#contractaddress').val(), //Contract Address starts with 0x...
                 sourceCode: $('#sourceCode').val(), //Contract Source Code (Flattened if necessary)
                 contractname: $('#contractname').val(), //ContractName
                 compilerversion: $('#compilerversion').val(), // see http://etherscan.io/solcversions for list of support versions
                 optimizationUsed: $('#optimizationUsed').val(), //0 = Optimization used, 1 = No Optimization
                 runs: 200, //set to 200 as default unless otherwise
                 constructorArguements: $('#constructorArguements').val(), //if applicable
                 libraryname1: $('#libraryname1').val(), //if applicable, a matching pair with libraryaddress1 required
                 libraryaddress1: $('#libraryaddress1').val(), //if applicable, a matching pair with libraryname1 required
                 libraryname2: $('#libraryname2').val(), //if applicable, matching pair required
                 libraryaddress2: $('#libraryaddress2').val(), //if applicable, matching pair required
                 libraryname3: $('#libraryname3').val(), //if applicable, matching pair required
                 libraryaddress3: $('#libraryaddress3').val(), //if applicable, matching pair required
                 libraryname4: $('#libraryname4').val(), //if applicable, matching pair required
                 libraryaddress4: $('#libraryaddress4').val(), //if applicable, matching pair required
                 libraryname5: $('#libraryname5').val(), //if applicable, matching pair required
                 libraryaddress5: $('#libraryaddress5').val(), //if applicable, matching pair required
                 libraryname6: $('#libraryname6').val(), //if applicable, matching pair required
                 libraryaddress6: $('#libraryaddress6').val(), //if applicable, matching pair required
                 libraryname7: $('#libraryname7').val(), //if applicable, matching pair required
                 libraryaddress7: $('#libraryaddress7').val(), //if applicable, matching pair required
                 libraryname8: $('#libraryname8').val(), //if applicable, matching pair required
                 libraryaddress8: $('#libraryaddress8').val(), //if applicable, matching pair required
                 libraryname9: $('#libraryname9').val(), //if applicable, matching pair required
                 libraryaddress9: $('#libraryaddress9').val(), //if applicable, matching pair required
                 libraryname10: $('#libraryname10').val(), //if applicable, matching pair required
                 libraryaddress10: $('#libraryaddress10').val() //if applicable, matching pair required
             },
             success: function (result) {
                 console.log(result);
                 if (result.status == "1") {
                     //1 = submission success, use the guid returned (result.result) to check the status of your submission.
                     // Average time of processing is 30-60 seconds
                     document.getElementById("postresult").innerHTML = result.status + ";" + result.message + ";" + result.result;
                     // result.result is the GUID receipt for the submission, you can use this guid for checking the verification status
                 } else {
                     //0 = error
                     document.getElementById("postresult").innerHTML = result.status + ";" + result.message + ";" + result.result;
                 }
                 console.log("status : " + result.status);
                 console.log("result : " + result.result);
             },
             error: function (result) {
                 console.log("error!");
                 document.getElementById("postresult").innerHTML = "Unexpected Error"
             }
         });

Check the source code to verify the commit status:

         $.ajax({
             type: "GET",
             url: "//api.etherscan.io/api",
             data: {
                 guid: 'ezq878u486pzijkvvmerl6a9mzwhv6sefgvqi5tkwceejc7tvn', //Replace with your Source Code GUID receipt above
                 module: "contract",
                 action: "checkverifystatus"
             },
             success: function (result) {
                 console.log("status : " + result.status); //0=Error, 1=Pass
                 console.log("message : " + result.message); //OK, NOTOK
                 console.log("result : " + result.result); //result explanation
                 $('#guidstatus').html(">> " + result.result);
             },
             error: function (result) {
                 alert('error');
             }
         });

### 4. Transaction API

* [BETA] Check the contract execution status (if an error occurred during contract execution), note: isError":"0"=Pass, isError":"1"=An error occurred during contract execution

         https://api.etherscan.io/api?module=transaction&action=getstatus&txhash=0x15f8e5ea1079d9a0bb04a4c58ae5fe7654b5b2b4463375ff7ffb490aa0032f3a&apikey=YourApiKeyToken
        
[BETA] Check transaction endorsement status (only applies to Post Byzantium fork transactions), note: status: 0=failed, 1=passed. will return null/empty values ​​for pre-byzantium fork

         https://api.etherscan.io/api?module=transaction&action=gettxreceiptstatus&txhash=0x513c1ba0bebf66436b5fed86ab668452b7805593c05073eb2d51d3a52f480a76&apikey=YourApiKeyToken

### 5. Block API

* [BETA] Get blocks and uncle blocks by block number

         https://api.etherscan.io/api?module=block&action=getblockreward&blockno=2165403&apikey=YourApiKeyToken

### 6.Event log



### 7.Geth/Parity Proxy

The following is a limited list of Proxied APIs supported by Geth via Etherscan. See https://github.com/ethereum/wiki/wiki/JSON-RPC for a list of parameters and descriptions. The provided parameters should be named as shown in the example below. For compatibility with Parity, prepend all hex strings with "0x"

* eth_blockNumber: Returns the latest block number

         https://api.etherscan.io/api?module=proxy&action=eth_blockNumber&apikey=YourApiKeyToken


* eth_getBlockByNumber: Return block information based on block number

         https://api.etherscan.io/api?module=proxy&action=eth_getBlockByNumber&tag=0x10d4f&boolean=true&apikey=YourApiKeyToken


* eth_getUncleByBlockNumberAndIndex: Returns an uncle block information based on the block number

         https://api.etherscan.io/api?module=proxy&action=eth_getUncleByBlockNumberAndIndex&tag=0x210A9B&index=0x0&apikey=YourApiKeyToken

* eth_getBlockTransactionCountByNumber: Returns the number of transactions in the block from the block matching the given block number

         https://api.etherscan.io/api?module=proxy&action=eth_getBlockTransactionCountByNumber&tag=0x10FB78&apikey=YourApiKeyToken


* eth_getTransactionByHash: Returns transaction confidence based on transaction hash

         https://api.etherscan.io/api?module=proxy&action=eth_getTransactionByHash&txhash=0x1e2910a262b1008d0616a0beb24c1a491d78771baa54a33e66065e03b1f46bc1&apikey=YourApiKeyToken


* eth_getTransactionByBlockNumberAndIndex: Returns information about transactions by block number and transaction index position

         https://api.etherscan.io/api?module=proxy&action=eth_getTransactionByBlockNumberAndIndex&tag=0x10d4f&index=0x0&apikey=YourApiKeyToken


eth_getTransactionCount: Get the transaction nonce of the current address, that is, the number of transactions

         https://api.etherscan.io/api?module=proxy&action=eth_getTransactionCount&address=0x2910543af39aba0cd09dbb2d50200b3e800a63d2&tag=latest&apikey=YourApiKeyToken


* eth_sendRawTransaction: Send a signed transaction string to the blockchain network

         https://api.etherscan.io/api?module=proxy&action=eth_sendRawTransaction&hex=0xf904808000831cfde080&apikey=YourApiKeyToken

Replace the hex value with the original hex-encoded transaction to be sent. If your hex code is particularly long, send it as a POST request


* eth_getTransactionReceipt: Returns the endorsement of the transaction through the transaction Hash

         https://api.etherscan.io/api?module=proxy&action=eth_getTransactionReceipt&txhash=0x1e2910a262b1008d0616a0beb24c1a491d78771baa54a33e66065e03b1f46bc1&apikey=YourApiKeyToken


* eth_call: perform a new message call immediately without creating a transaction on the blockchain

         https://api.etherscan.io/api?module=proxy&action=eth_call&to=0xAEEF46DB4855E25702F8 237E8f403FddcaF931C0&data=0x70a0823100000000000000000000000e16359506c028e51f16be38 986ec5746251e9724&tag=latest&apikey=YourApiKeyToken


* eth_getCode: Returns the code of the given address

         https://api.etherscan.io/api?module=proxy&action=eth_getCode&address=0xf75e354c5edc8efed9b59ee9f67a80845ade7d0c&tag=latest&apikey=YourApiKeyToken


* eth_getStorageAt (experimental) Returns the value of the storage location at the given address.https://api.etherscan.io/api?module=proxy&action=eth_getStorageAt&address=0x6e03d9cce9d60f3e9f2597e13cd4c54c55330cfd&position=0x0&tag=latest&apikey=YourApiKeyToken


* eth_gasPrice: Returns the current gas price in wei units

         https://api.etherscan.io/api?module=proxy&action=eth_gasPrice&apikey=YourApiKeyToken


* eth_estimateGas: Makes a call or transaction, does not add the information of the call and transaction to the blockchain, and can return the estimated gas used, which can be used to estimate the gas used.

         https://api.etherscan.io/api?module=proxy&action=eth_estimateGas&to=0xf0160428a8552ac9bb7e050d90eeade4ddd52843&value=0xff22&gasPrice=0x051da038cc&gas=0xffffff&apikey=YourApiKeyToken

### 8. Token-related APIs

* Obtain the total amount of ERC20 Token tokens based on the contract address

         https://api.etherscan.io/api?module=stats&action=tokensupply&contractaddress=0x57d90b64a1a57749b0f932f1a3395792e12e7055&apikey=YourApiKeyToken
        
Abandoned API: Get the total amount of tokens based on the contract name. This API has been abandoned and replaced by the previous API. Get the total amount of ERC20 tokens based on the contract address.

         https://api.etherscan.io/api?module=stats&action=tokensupply&tokenname=DGD&apikey=YourApiKeyToken

* Obtain the total amount of ERC20 tokens based on the Token's contract address and account address

         https://api.etherscan.io/api?module=account&action=tokenbalance&contractaddress=0x57d90b64a1a57 749b0f932f1a3395792e12e7055&address=0xe04f27eb70e025b78871a2ad7eabe85e61212761&tag=latest&apikey =YourApiKeyToken

Abandoned API: Obtain the total amount of ERC20 tokens based on the Token name and account address. This API has been abandoned and replaced by the above API.

         https://api.etherscan.io/api?module=account&action=tokenbalance&tokenname=DGD&address=0x4366ddc115d8cf213c564da36e64c8ebaa30cdbd&tag=latest&apikey=YourApiKeyToken


### 9. Statistics related API

* Obtain the total supply of Ether, and the result is returned in Wei mode to obtain the final result by dividing the value of the Ethereum number result by 1000000000000000000

         https://api.etherscan.io/api?module=stats&action=ethsupply&apikey=YourApiKeyToken

* Get ETHER LastPrice price

         https://api.etherscan.io/api?module=stats&action=ethprice&apikey=YourApiKeyToken

### 10. Miscellaneous, Tools and Utilities

The following are third-party tools and utilities created by the community

* py-etherscan-api module (corpetty), a third-party EtherScan.io API python binding module written by Corey Petty

         https://github.com/corpetty/py-etherscan-api

* Node API (Sebastian Schürmann), Etherscan’s third-party Node API

         https://github.com/sebs/etherscan-api

## 5. Introduction to the Ethereum JSON-RPC interface (some of these interfaces will be used during wallet development)


## 6. Introduction to web3j (web3java)

### 1.web3j overview

Web3j is an SDK for developing Ethereum DAPP through Java, which encapsulates many APIs. web3j is a lightweight, highly modular, reactive, type-safe Java and Android library for handling smart contracts and integrating with clients (nodes) on the Ethereum network:

.：
     ![.：
](https://github.com/guoshijiang/blockchain-wallet/blob/master/img/web3j_network.png)

This allows you to use the Ethereum blockchain without the additional overhead of writing your own integration code for the platform. The Java and Blockchain conversation provides an overview of blockchain, Ethereum, and web3j.

### 3.Features of web3j

* Complete the implementation of Ethereum's JSON-RPC client API through HTTP and IPC
* Ethereum wallet support
* Automatically generate Java smart contract wrappers to create, deploy, trade and call smart contracts from native Java code (supports Solidity and Truffle definition formats)
* React function API for handling filters
* Ethereum Name Service (ENS) support
* Supports Parity's Personal and Geth's personal client API
* Supports Infura so you don't need to run the Ethereum client yourself
* Comprehensive integration tests demonstrate various scenarios mentioned above
* Command line tools
*Android compatible
* Support JP Morgan's quorum via web3j-quorum

### 3. Web3j runtime dependencies

* RxJava for its reactive functional API
* OKHttp is used for HTTP connections
* Jackson Core for fast JSON serialization/deserialization
* Bouncy Castle (Spongy Castle on Android) for encryption
* Jnr-unixsocket for *nix IPC (not for Android)

### 4. Use Web3j in the project

The following projects are all aimed at java8 libraries.

#### 4.1.maven project

     <dependency>
       <groupId>org.web3j</groupId>
       <artifactId>core</artifactId>
       <version>4.0.0</version>
     </dependency>

#### 4.2. Android project

     <dependency>
       <groupId>org.web3j</groupId>
       <artifactId>core</artifactId>
       <version>3.3.1-android</version>
     </dependency>

#### 4.3.gradle project

compile ('org.web3j:core:4.0.0')

Android:
compile ('org.web3j:core:3.3.1-android')

#### 4.4.Simple use of web3j

Before sending a request, you need to choose a geth client

Start an Ethereum client, such as Geth, if you are not already running:

     geth --rpcapi personal,db,eth,net,web3 --rpc --testnet
    
Parity:

     parity --chain testnet
    
Or use Infura, which offers a free client running in the cloud:

     Web3j web3 = Web3j.build(new HttpService("https://ropsten.infura.io/your-token"));

Send sync request

     Web3j web3 = Web3j.build(new HttpService()); // Default http://localhost:8545/
     Web3ClientVersion web3ClientVersion = web3.web3ClientVersion().send();
     String clientVersion = web3ClientVersion.getWeb3ClientVersion();
    
Use CompletableFuture (Future on Android) to send an asynchronous request:

     Web3j web3 = Web3j.build(new HttpService()); // Default http://localhost:8545/
     Web3ClientVersion web3ClientVersion = web3.web3ClientVersion().sendAsync().get();
     String clientVersion = web3ClientVersion.getWeb3ClientVersion();
    
To use RxJava Flowable:

     Web3j web3 = Web3j.build(new HttpService()); // defaults to http://localhost:8545/
     web3.web3ClientVersion().flowable().subscribe(x -> {
         String clientVersion = x.getWeb3ClientVersion();
         ...
     });

### 5.web3 IPC mechanism

web3j also supports fast inter-process communication (IPC) via file sockets with clients running on the same host as web3j. When creating your service, just use the relevant IpcService implementation instead of HttpService:

*Linux

         Web3j web3 = Web3j.build(new UnixIpcService("/path/to/socketfile"));
        
*Windows

         Web3j web3 = Web3j.build(new WindowsIpcService("/path/to/namedpipefile"));
        
NOTE: The IPC mechanism is currently not available on web3j-android

Handling smart contracts using Java smart contract "wrappers"

web3j can automatically generate smart contract wrapper code to deploy and interact with smart contracts without leaving the JVM.

The following command is to generate the wrapper code and compile your smart contract

     solc <contract>.sol --bin --abi --optimize -o <output-dir>/
    
Then use web3j's command line tools to generate the wrapper code:

     web3j solidity generate -b /path/to/<smart-contract>.bin -a /path/to/<smart-contract>.abi -o /path/to/src/main/java -p com.your.organisation .name
    
nowYou can create and deploy your smart contract

     Web3j web3 = Web3j.build(new HttpService()); // defaults to http://localhost:8545/
     Credentials credentials = WalletUtils.loadCredentials("password", "/path/to/walletfile");

     YourSmartContract contract = YourSmartContract.deploy(
             <web3j>, <credentials>,
             GAS_PRICE, GAS_LIMIT,
             <param1>, ..., <paramN>).send(); // constructor params

Alternatively, if you use Truffle, you can use its .json output file:

     # Inside your Truffle project
     $trufflecompile
     $truffledeploy
        
Then use web3j's command line tools to generate the wrapper code:

     $ cd /path/to/your/web3j/java/project
     $ web3j truffle generate /path/to/<truffle-smart-contract-output>.json -o /path/to/src/main/java -p com.your.organisation.name
    
Whether using Truffle directly or solc, either way you get a Java wrapper for your contract.

Therefore, to use an existing contract:

     YourSmartContract contract = YourSmartContract.load(
             "0x<address>|<ensName>", <web3j>, <credentials>, GAS_PRICE, GAS_LIMIT);
    
Transact with smart contracts:

     TransactionReceipt transactionReceipt = contract.someMethod(
                  <param1>,
                  ...).send();
    
    
Call smart contract

     Type result = contract.someMethod(<param1>, ...).send();

Control gasprice price

     contract.setGasProvider(new DefaultGasProvider() {
             ...
             });
    
    
  ### 6.web3 filter
 
Web3j functional reactivity makes it very simple to set up observers that notify subscribers of events occurring on the blockchain.
    
To receive all new blocks as they are added to the blockchain:

     Subscription subscription = web3j.blockFlowable(false).subscribe(block -> {
         ...
     });
    
To receive all new transactions as they are added to the blockchain:

     Subscription subscription = web3j.transactionFlowable().subscribe(tx -> {
         ...
     });
    
Receive all pending transactions upon submission to the network (i.e. before grouping them together into a block):

     Subscription subscription = web3j.pendingTransactionFlowable().subscribe(tx -> {
         ...
     });
    
Or if you wish to replay all blocks up to date and notify new subsequent blocks being created: there are many other transaction and block replays described in our final appendix (not yet written) to Flowable.

Supported topic filters:
    
     EthFilter filter = new EthFilter(DefaultBlockParameterName.EARLIEST,
             DefaultBlockParameterName.LATEST, <contract-address>)
                  .addSingleTopic(...)|.addOptionalTopics(..., ...)|...;
     web3j.ethLogFlowable(filter).subscribe(log -> {
         ...
     });
       
You should always cancel your subscription when it is no longer needed:

     subscription.unsubscribe();

Note: Infura does not support filters.

### 7. Transaction

web3j supports sending transactions using Ethereum wallet files (recommended) and Ethereum client management commands.

To send ether to another party using an Ethereum wallet file:

     Web3j web3 = Web3j.build(new HttpService()); // defaults to http://localhost:8545/
     Credentials credentials = WalletUtils.loadCredentials("password", "/path/to/walletfile");
     TransactionReceipt transactionReceipt = Transfer.sendFunds(
             web3, credentials, "0x<address>|<ensName>",
             BigDecimal.valueOf(1.0), Convert.Unit.ETHER)
             .send();
    
Or if you wish to create your own custom transaction:

* Build httpService

         Web3j web3 = Web3j.build(new HttpService()); // defaults to http://localhost:8545/
         Credentials credentials = WalletUtils.loadCredentials("password", "/path/to/walletfile");
* Get available nonce

         EthGetTransactionCount ethGetTransactionCount = web3j.ethGetTransactionCount(
                      address, DefaultBlockParameterName.LATEST).sendAsync().get();
         BigInteger nonce = ethGetTransactionCount.getTransactionCount();

*Create our deal

         RawTransaction rawTransaction = RawTransaction.createEtherTransaction(
                      nonce, <gas price>, <gas limit>, <toAddress>, <value>);

* Sign and send our transaction

         byte[] signedMessage = TransactionEncoder.signMessage(rawTransaction, credentials);
         String hexValue = Hex.toHexString(signedMessage);
         EthSendTransaction ethSendTransaction = web3j.ethSendRawTransaction(hexValue).send();
   
Although using web3j's Transfer to transact with Ether is much simpler.

Use the Ethereum client's admin command (make sure your wallet is in the client's keystore):
    
     Admin web3j = Admin.build(new HttpService()); //Default http://localhost:8545/
     PersonalUnlockAccount personalUnlockAccount = web3j.personalUnlockAccount("0x000...", "a password").sendAsync().get();
     if (personalUnlockAccount.accountUnlocked()) {
         //Send transaction here
     }

If you want to use Parity's Personal or Trace or Geth's Personal client API, you can use the org.web3j:parity and org.web3j:geth modules respectively.

### 8. Command line tools

The web3j fat jar is released with each version and provides command line tools. You can use some features of web3j from the command line:

*Wallet creation
* Wallet password management
* Transfer funds from one wallet to another
* Generate Solidity smart contract function wrapper

For specific information, please see the Web3j chapter.

## 7. Develop wallet using keystore to store addresses and private keys (without using mnemonic words)

Using the keystore method to develop an Ethereum wallet does not require things like mnemonics. We will use keythereum, ethereumjs-tx and web3js in ethereumjs to develop the wallet. webjs is a NodeJs library similar to web3j. Our wallet development here is just to write the nodeJS code for generating keystore, exporting and importing keystore, importing and exporting private keys, transaction signatures, sending transfers and transfer confirmation fragments. If you want to learn how to develop with keythereum, ethereumjs-tx and web3js For an Ethereum wallet, please read Project Practice 1 of this book. The practical part of the project will explain in detail the entire process of design, development, testing and online deployment of an Ethereum wallet. Our wallet will implement the storage of local private keys, and the private keys will be managed by the wallet client.

### 1.keystore
The following code is based on keystore-related code, including generating keystore, exporting and importing keystore, and importing and exporting private keys. This is a module library encapsulated by the author based on NodeJs. If you have needs, you can use it directly.

     const keythereum = require("keythereum");
     const fs = require('fs');

     var libKeystore = {};

     const paramsErr = {code:1000, message:"input params is null"};
     const createDkErr = {code:1001, message:"create dk error"};
     const createKeystoreErr = {code:1002, message:"create keystore fail"};

     /**
      * @param password
      * @returns {*}
      */
     // Generate keystore
     libKeystore.createKeystore = function (password) {
         if(!password) {
             return paramsErr;
         }
         var keystore = '';
         var params = { keyBytes: 32, ivBytes: 16 };
         var dk = keythereum.create(params);
         var kdf = "pbkdf2";
         var options = {
             kdf: "pbkdf2",
             cipher: "aes-128-ctr",
             kdfparams: {
                 c: 262144,
                 dklen: 32,
                 prf: "hmac-sha256"
             }
         };
         var dk = keythereum.create(params)
         if (!dk) {
             return createDkErr;
         }
         keystore = keythereum.dump(password, dk.privateKey, dk.salt, dk.iv, options);
         if(!keystore) {
             return createKeystoreErr;
         }
         return keystore;
     }

     /**
      * @param keyObject
      * @param path if your path is null, export keystore by default way; if path has value, export keystore by your way
      * @returns {{code: number, message: string}}
      */
      //Export keystore
     libKeystore.exportKeystore = function(keyObject, path) {
         if(!keyObject) {
             return paramsErr;
         }
         if(!path){
             keythereum.exportToFile(keyObject);
         } else {
             var json = JSON.stringify(keyObject);
             var outfile = keythereum.generateKeystoreFilename(keyObject.address);
             var outpath = path + "/" + outfile;
             console.log(outpath);
             fs.writeFile(outpath, json, function (err) {
                 if (err) {
                     return err;
                 } else{
                     outpath;
                 }
             });
         }
     }

     /**
      * @param address
      * @param datadir
      * @returns {*}
      */
      //Import keystore
     libKeystore.importKeystore = function(address, datadir) {
         if(!address || !datadir) {
             return paramsErr;
         }
         return keythereum.importFromFile(address, datadir);
     }

     /**
      * @param keyObject
      * @param password
      * @returns {*}
      */
      //Export private key
     libKeystore.exportPrivateKey = function(keyObject, password) {
         if(!keyObject || !password) {
             return paramsErr;
         }
         return keythereum.recover(password, keyObject);
     }

     /**
      * @param privateKey
      * @param password
      * @returns {*}
      */
      //Import the private key and generate keystore
     libKeystore.importPrivateKey = function(privateKey,password) {
         if(!password || privateKey) {
             return paramsErr;
         }
         var keystore = '';
         var params = { keyBytes: 32, ivBytes: 16 };
         var dk = keythereum.create(params);
         var kdf = "pbkdf2";
         var options = {
             kdf: "pbkdf2",
             cipher: "aes-128-ctr",
             kdfparams: {
                 c: 262144,
                 dklen: 32,
                 prf: "hmac-sha256"
             }
         };
         var dk = keythereum.create(params)
         if (!dk) {
             return createDkErr;
         }
         keystore = keythereum.dump(password, privateKey, dk.salt, dk.iv, options);
         if(!keystore) {
             return createKeystoreErr;
         }
         return keystore;
     }

     module.exports = libKeystore;


### 4. Transaction signature

The following code is the code for Ethereum transaction signature

     const util = require('ethereumjs-util');
     const transaction = require('ethereumjs-tx');

     var ethOrErc20Sign = {};

     /**
      * @param privateKey
      * @param nonce
      * @param toAddress
      * @param sendAmount
      * @param gasPrice
      * @param gasLimit
      * @returns {*}
      */
     ethOrErc20Sign.ethereumSign = function (privateKey, nonce, toAddress, sendAmount, gasPrice, gasLimit) {
         var errData = {code:400, message:"param is null"};
         var serializedErr = {code:400, message:"Serialized transaction fail"};
         if(!privateKey || !nonce || !toAddress || !sendAmount || !gasPrice || !gasLimit) {
             console.log("one of fromAddress, toAddress, sendToBalance, sendFee is null, please give a valid param");
             return errData;
         } else {
             var transactionNonce = parseInt(nonce).toString(16);
             var numBalance = parseFloat(sendAmount);
             var balancetoWei = web3.toWei(numBalance, "ether");
             var oxNumBalance = parseInt(balancetoWei).toString(16);
             var gasPriceHex = parseInt(gasPrice).toString(16);
             var gasLimitHex = parseInt(gasLimit).toString(16);
             var privateKeyBuffer = Buffer.from(privateKey, 'hex');
             var rawTx = {
                 nonce:'0x' + transactionNonce,
                 gasPrice: '0x' + gasPriceHex,
                 gas:'0x'+ gasLimitHex,
                 to:toAddress,
                 value:'0x' + oxNumBalance,
             };
             alert(JSON.stringify(rawTx));
             var tx = new transaction(rawTx);
             tx.sign(privateKeyBuffer);
             var serializedTx = tx.serialize();
             if(serializedTx == null) {
                 return serializedErr;
             } else {if (tx.verifySignature()) {
                     console.log('Signature Checks out!');
                 } else {
                     return serializedErr;
                 }
             }
         }
         return '0x' + serializedTx.toString('hex');
     }

     module.exports = ethOrErc20Sign;
    
Explain the above code, privateKey: is the private key, which is generated during the process of creating an account. The private key is the key to open your account, please keep it carefully; nonce: transaction nonce, which is the identifier to ensure the uniqueness of the transaction. ; toAddress: transfer address, the account address of the user you want to transfer to, similar to a bank card number; sendAmount: transfer amount; for gasPrice and gasLimit, please refer to the gas, gasPrice and gasLimit section above.

A series of signature parameters such as nonce, gasPrice, gas, to, and value in the code are all hexadecimal strings, with the prefix added 0x; in the above signature code, you can also use `chainId:1`,chainId=1 Indicates sending the transaction to the main network.

### 5. Send transaction to blockchain network

There are many ways to send signed transactions to the blockchain network. You can use libraries such as web3j and web3js, or you can initiate an http request yourself. What we use here is web3js to send transactions to the blockchain network. We need to use web3js to send transactions to the blockchain network. You have to do the following things.

#### 5.1.Initialize web3js

     var Web3 = require("web3")
     if (typeof web3 !== 'undefined'){
        web3 = new Web3(web3.currentProvider);
     }else{
        web3 = new Web3(new Web3.providers.HttpProvider("http://localhost:8545"));
     }

#### 5.2. Get the balance on your account

The purpose of getting the balance on your account is to verify whether you have enough money to transfer when you transfer. This can prevent the transaction you signed from failing to be sent.

     web3.eth.getBalance("0xfa319c8ea9b00513bb1a112de2073263aa92c930", function(err, result){
        if (err == null){
           console.log('~balance:' + result);
        }else{
           console.log('~balance:' + result);
        }
     });

`0xfa319c8ea9b00513bb1a112de2073263aa92c930` is the account address. If you are using your account, please replace this address with yours.

#### 5.3. Send transactions to the blockchain network

     web3.eth.sendRawTransaction(hexadecimal signature string, function (err, hash){
        console.log('Transaction result: ' + hash);
        if (callback && typeof(callback) === "function"){
           if(!err){
              callback(null, hash);
           }else{
              callback(err, null);
           }
        }
     });

The position here of "Hex Signature String" is to convert your signature string into a hexadecimal string, just add the prefix `0x`

### 6. Transfer transaction confirmation

There are many mechanisms to realize transfer confirmation, such as using event notification mechanism, block number confirmation mechanism, etc. During the actual implementation of our project, we will explain various confirmation mechanisms in detail. Here we only provide a simple confirmation mechanism to obtain the number of blocks. When you send a transaction to the blockchain network, you obtain the current number of blocks. After the transfer is completed, the number of blocks is obtained in real time. When your number of blocks reaches the confirmed number of blocks, the transfer can be considered successful.

Below is the code to get the number of blocks

     var bnum = web3.eth.blockNumber;
     console.log(bnum);
    
## 8. Node-based wallet development

The so-called node-based money relies on wallet nodes to develop wallets. A series of information such as your address and private key are stored on the wallet node and are not stored locally. This way of developing wallets generally calls the JSON-RPC interface to develop wallets. We also use web3js for development here. Of course, you can also use HTTP to request the interface.

### 1. Create an account

### 1.1. Create an account on the wallet node

Call the `personal.newAccount()` function to generate an account keystore on the wallet node, for example:

     personal.newAccount("123456")
Generate an account address with password `123456`

### 1.2. Check the address of this account on the wallet node

#### 1.2.1.Send a request to obtain the account list

     curl -X POST --data '{"jsonrpc":"2.0","method":"eth_accounts","params":[],"id":1}'

#### 1.2.1. Return account list results

     {
       "id":1,
       "jsonrpc": "2.0",
       "result": ["0xc94770007dda54cF92009BFF0dE90c06F603a09f"]
     }

The accounts here show the addresses of all users you created on this wallet node. After obtaining the address, you can create a transaction.

### 1.3. Get account balance

Call the eth_getBalance function to obtain the account balance of the given address

#### 1.3.1. Parameters

Account address
Number of blocks: latest represents the latest block

     params: [
        '0xc94770007dda54cF92009BFF0dE90c06F603a09f',
        'latest'
     ]
    
#### 1.3.2.Return value

Returns hexadecimal characters, converted to decimal representation, and the currency unit is wei

#### 1.3.3.Send request

     curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getBalance","params":["0xc94770007dda54cF92009BFF0dE90c06F603a09f", "latest"],"id":1}'

#### 1.3.4. Return results

     {
       "id":1,
       "jsonrpc": "2.0",
       "result": "0x0234c8a3397aab58"
     }

### 1.4. Create transaction and send it

The main difference between creating a transaction on a wallet node and signing a transaction locally is here. To create a transaction on a wallet node, you send the entire transaction to the wallet node, and the wallet node completes the signing and transfer. What we call is the eth_sendTransaction function.

#### 1.4.1. Parameters

from: transfer-out address.
to: transfer address.
gas: the gas required for transaction execution
gasPrice: the integer gasPrice of each paid gas
value: the amount of transfer
data: ERC20 token transfer information data
nonce: nonce that identifies the transaction, increasing in sequence.

     params: [{
       "from": "0xb60e8dd61c5d32be8058bb8eb970870f07233155",
       "to": "0xd46e8dd67c5d32be8058bb8eb970870f07244567",
       "gas": "0x76c0", // 30400
       "gasPrice": "0x9184e72a000", // 10000000000000
       "value": "0x9184e72a", // 2441406250
       "data": "0xd46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f072445675"
     }]

#### 1.4.2. Request

     curl -X POST --data '{"jsonrpc":"2.0","method":"eth_sendTransaction","params":[{see above}],"id":1}'

#### 1.4.3. Return results

Successfully returns the Hash of the transaction

     {
       "id":1,
       "jsonrpc": "2.0",
       "result": "0xe670ec64341771606e55d6b4ca35a1a6b75ee3d5145a99d05921026d1527331"
     }

Use the eth_getTransactionReceipt function to obtain the contract address.


### 1.5. Transfer confirmation

There are many mechanisms to realize transfer confirmation, such as using event notification mechanism, block number confirmation mechanism, etc. During the actual implementation of our project, we will explain various confirmation mechanisms in detail. Here we only provide a simple confirmation mechanism to obtain the number of blocks. When you send a transaction to the blockchain network, you obtain the current number of blocks. After the transfer is completed, the number of blocks is obtained in real time. When your number of blocks reaches the confirmed number of blocks, the transfer can be considered successful.

Get the number of blocks by calling the eth_blockNumber function, and change the function to return the latest number of blocks.

#### 1.5.1.Send request

     curl -X POST --data '{"jsonrpc":"2.0","method":"eth_blockNumber","params":[],"id":83}'

#### 1.5.2. Return results

     {
       "id":83,
       "jsonrpc": "2.0",
       "result": "0xc94"
     }


## 9. Non-deterministic Ethereum wallet development

## 10. Hierarchical deterministic Ethereum wallet development

Regarding the advantages of hierarchical deterministic wallets, we have already introduced them in the previous content, so we will not introduce them further here. The serial number of Ethereum in the BIP layered protocol is `60`; for the content of the BIP layered protocol, please see the chapter BIP layered protocol.

The figure below shows the position of ETH in the layered protocol

.：
     ![.：
](https://github.com/guoshijiang/blockchain-wallet/blob/master/img/eth60.png)


### 1. Locally generate the account system of the Ethereum hierarchical deterministic wallet

In order to understand Ethereum's address generation more intuitively, the following code will contain mnemonic words.

     var bip39 = require('bip39')
     var hdkey = require('ethereumjs-wallet/hdkey')
     var util = require('ethereumjs-util')
    
     // Generate mnemonic words
     var mnemonic = bip39.generateMnemonic()
     // Generate random seed
     var seed = bip39.mnemonicToSeed(mnemonic)
     // Generate wallet private key
     var hdWallet= hdkey.fromMasterSeed(seed)
     // Generate the private key of the first account
     var key = hdWallet.derivePath("m/44'/60'/0'/0/0")
     // Generate address
     var address = util.pubToAddress(key._hdkey._publicKey, true)
     //Export private key
     var privateKey = key._hdkey._privateKey.toString('hex')
     console.log("address = " + address + " privateKey = " + privateKey)
    
You can manage the address and private key generated above using a local database or file. How to manage it will be explained in detail in our actual project later.

### 2. Transaction signature

Single transaction signature

     function ethereumSign(privateKey, nonce, toAddress, sendToBalance, gasPrice, gasLimit) {
       var errData = {code:400, message:"param is null"};
       var serializedErr = {code:400, message:"Serialized transaction fail"};
       if(!privateKey || !nonce || !toAddress || !sendToBalance || !gasPrice || !gasLimit) {
         console.log("one of fromAddress, toAddress, sendToBalance, sendFee is null, please give a valid param");
         return errData;
       } else {
         var transactionNonce = parseInt(nonce).toString(16);
         var numBalance = parseFloat(sendToBalance);
         var balancetoWei = web3.toWei(numBalance, "ether");
         var oxNumBalance = parseInt(balancetoWei).toString(16);
         var gasPriceHex = parseInt(gasPrice).toString(16);
         var gasLimitHex = parseInt(gasLimit).toString(16);
         var privateKeyBuffer = Buffer.from(privateKey, 'hex');
         var rawTx = {
           nonce:'0x' + transactionNonce,
           gasPrice: '0x' + gasPriceHex,
           gas:'0x'+ gasLimitHex,
           to:toAddress,
           value:'0x' + oxNumBalance,
         };
         alert(JSON.stringify(rawTx));
         var tx = new transaction(rawTx);
         tx.sign(privateKeyBuffer);
         var serializedTx = tx.serialize();
         if(serializedTx == null) {
           return serializedErr;
         } else {
           if (tx.verifySignature()) {
             console.log('Signature Checks out!');
           } else {
             return serializedErr;
           }
         }
       }
       return '0x' + serializedTx.toString('hex');
     }

Signature for multiple transfers

     function MultiEthSign(sendData) {
       if(sendData == null) {
         console.log("param is invalid, sendData is null");
       }
       console.log("param is valid, start multiSign transaction");
       var calcNonce = sendData.nonce;
       var arrData = sendData.signDta;
       var outArr = [];
       for(var i = 0; i < arrData.length; i++){
         var transactionNonce = parseInt(calcNonce).toString(16);
         var balancetoWei = web3.toWei(parseFloat(sendData.signDta[i].totalAmount), "ether");
         var balanceValue = parseInt(balancetoWei).toString(16);
         var oxGas = parseInt(sendData.gasLimit).toString(16);
         var oxGasPrice = parseInt(sendData.gasPrice).toString(16);
         var privateKeyBuffer = Buffer.from(sendData.privateKey, 'hex');
         var rawTx = {
           nonce:'0x' + transactionNonce,
           gasPrice: '0x' + oxGasPrice,
           gas:'0x' + oxGas,
           to: sendData.signDta[i].toAddress,
           value:'0x' + balanceValue
         };
         var tx = new transaction(rawTx);
         tx.sign(privateKeyBuffer);
         var serializedTx = tx.serialize();
         if(serializedTx == null) {
           console.log("Serialized transaction fail")
         } else {
           outArr = outArr.concat('0x' + serializedTx.toString('hex'))
           if (tx.verifySignature()) {
             console.log('Signature Checks out!')
           } else {
             console.log("Signature checks fail")
           }
         }
         calcNonce = calcNonce + 1;
       }
       return { signCoin:"ETH", signDataArr:outArr}
     }

Sending transactions and transaction confirmations are consistent with the above if using NodeJs

Next we use web3j to send transactions, SSM code, using SpringMVC

Transaction sending controller

         package com.biwork.controller;

         import java.util.ArrayList;
         import java.util.HashMap;
         import java.util.List;
         import java.util.Map;

         import javax.servlet.http.HttpServletRequest;

         import org.apache.commons.lang3.StringUtils;
         import org.slf4j.Logger;
         import org.slf4j.LoggerFactory;
         import org.springframework.beans.factory.annotation.Autowired;
         import org.springframework.stereotype.Controller;
         import org.springframework.web.bind.annotation.RequestBody;
         import org.springframework.web.bind.annotation.RequestMapping;
         import org.springframework.web.bind.annotation.ResponseBody;
         import org.springframework.web.bind.annotation.RequestMethod;

         import com.biwork.entity.RawTx;

         import com.biwork.exception.BusiException;

         import com.biwork.po.RespPojo;
         import com.biwork.po.request.BatchRawTxFlowPojo;
         import com.biwork.po.request.RawTxFlowPojo;
         import com.biwork.po.RawTxPojo;

         import com.biwork.service.RawTxService;

         import com.biwork.util.Constants;

         import io.swagger.annotations.Api;
         import io.swagger.annotations.ApiOperation;
         import io.swagger.annotations.ApiParam;

         @Controller
         @RequestMapping("/v1")
         @Api(value = "/v1", description = "Send signaturepost transaction data to the blockchain network")
         public class RawTxController {
             private Logger logger = LoggerFactory.getLogger(getClass());
             //Send the signed transaction to the blockchain network
             @Autowired
             RawTxService rawTxService;

             @ResponseBody
             @RequestMapping(value = "/eth_sendBatchRawTransaction", method=RequestMethod.POST, produces="application/json;charset=utf-8;")
             @ApiOperation(value = "Send signed transaction data in batches to the Ethereum blockchain network", notes = "Send signed transaction data in batches to the Ethereum blockchain network", httpMethod = "POST")
             public RespPojo getBatchEthRawTx(HttpServletRequest request, @RequestBody
                     @ApiParam(name="Send signed transaction object", value="Incoming json format", required=true) BatchRawTxFlowPojo batchRwatxFlowPojo){
                 logger.info("---Send signed transaction data in batches to the Ethereum blockchain network---");
                 RawTxPojo rawTx_pojo=new RawTxPojo();
                 RespPojo resp=new RespPojo();
                 String signCoin = batchRwatxFlowPojo.getSignCoin();
                 List<String> arrList = new ArrayList<>();
                 arrList = batchRwatxFlowPojo.getSignDataArr();
                 System.out.println("signCoin = " + signCoin);
                 System.out.println("sign = " + arrList.get(0));

                 if(StringUtils.isBlank(signCoin) || arrList.size() == 0){
                       resp.setRetCode(Constants.PARAMETER_CODE);
                       resp.setRetMsg("Data cannot be empty after batch signing");
                       return resp;
                 }

                 RawTx rawTx;
                 List<String> hashArray = new ArrayList<>();
                 try {
                     for(int i = 0; i < arrList.size(); i++) {
                         rawTx = rawTxService.getEthRawTx(arrList.get(i));
                         hashArray.add(rawTx.getRawTx());
                     }
                 }catch(BusiException e){
                      logger.error("Batch sending signed transaction data to the Ethereum blockchain network abnormality - business exception {}",e);
                       resp.setRetCode(e.getCode());
                       resp.setRetMsg(e.getMessage());
                       return resp;
                 }
                 catch (Exception e) {
                       logger.error("Batch sending signed transaction data to the Ethereum blockchain network exception - ordinary exception {}", e);
                       resp.setRetCode(Constants.FAIL_CODE);
                       resp.setRetMsg(Constants.FAIL_MESSAGE);
                       return resp;
                 }
                 resp.setRetCode(Constants.SUCCESSFUL_CODE);
                 resp.setRetMsg(Constants.SUCCESSFUL_MESSAGE);
                 resp.setData(hashArray);
                 System.out.println("hashArray = " + hashArray);
                 return resp;
             }

             @ResponseBody
             @RequestMapping(value = "/eth_sendRawTransaction", method=RequestMethod.POST, produces="application/json;charset=utf-8;")
             @ApiOperation(value = "Send signed transaction data to the Ethereum blockchain network", notes = "Send signed transaction data to the Ethereum blockchain network", httpMethod = "POST")
             public RespPojo getEthRawTx(HttpServletRequest request, @RequestBody
                     @ApiParam(name="Send signed transaction object", value="Incoming json format", required=true) RawTxFlowPojo rwatxFlowPojo){
                 logger.info("---Method to send signed transaction data to the Ethereum blockchain network---");
                 RawTxPojo rawTx_pojo=new RawTxPojo();
                 RespPojo resp=new RespPojo();
                 String data = rwatxFlowPojo.getData();
                 System.out.println("data = " + data);

                 if(StringUtils.isBlank(data)){
                       resp.setRetCode(Constants.PARAMETER_CODE);
                       resp.setRetMsg("The data cannot be empty after signing");
                       return resp;
                 }

                 RawTx rawTx;
                 try {
                     rawTx = rawTxService.getEthRawTx(data);
                 }catch(BusiException e){
                      logger.error("Exception when sending signed transaction data to the Ethereum blockchain network {}",e);
                       resp.setRetCode(e.getCode());
                       resp.setRetMsg(e.getMessage());
                       return resp;
                 }
                 catch (Exception e) {
                       logger.error("Exception when sending signed transaction data to the Ethereum blockchain network {}",e);
                       resp.setRetCode(Constants.FAIL_CODE);
                       resp.setRetMsg(Constants.FAIL_MESSAGE);
                       return resp;
                 }
                 if(rawTx!=null){
                     rawTx_pojo = new RawTxPojo();
                     rawTx_pojo.setRawTx(rawTx.getRawTx());
                     Map<String, Object> rtnMap = new HashMap<String, Object>();
                     rtnMap.put("transactionHash", rawTx.getRawTx());
                     resp.setRetCode(Constants.SUCCESSFUL_CODE);
                     resp.setRetMsg(Constants.SUCCESSFUL_MESSAGE);
                     resp.setData(rtnMap);
                     return resp;
                 }
                 return resp;
             }

             @ResponseBody
             @RequestMapping(value="/btc_sendRawTransaction", method=RequestMethod.POST, produces="application/json;charset=utf-8;") @ApiOperation(value = "Send signed transaction data to the BTC blockchain network", notes = "Send signed transaction data to the BTC blockchain network", httpMethod = "POST")
             publicRespPojo getBtcRawTx(HttpServletRequest request,@RequestBody
                     @ApiParam(name="process object",value="incoming json format",required=true) RawTxFlowPojo rwatxFlowPojo){
                 logger.info("---Method to send signed transaction data to the BTC blockchain network---");
                 RawTxPojo rawTx_pojo=new RawTxPojo();
                 RespPojo resp=new RespPojo();

                 String data = rwatxFlowPojo.getData();
                 System.out.println("data = " + data);

                 if(StringUtils.isBlank(data)){
                       resp.setRetCode(Constants.PARAMETER_CODE);
                       resp.setRetMsg("The data cannot be empty after signing");
                       return resp;
                 }

                 RawTx rawTx;
                 try {
                     rawTx = rawTxService.getBtcRawTx(data);
                 }catch(BusiException e){
                      logger.error("Exception in sending signed transaction data to the BTC blockchain network {}",e);
                       resp.setRetCode(e.getCode());
                       resp.setRetMsg(e.getMessage());
                       return resp;
                 }
                 catch (Exception e) {
                       logger.error("Exception in sending signed transaction data to the BTC blockchain network {}",e);
                       resp.setRetCode(Constants.FAIL_CODE);
                       resp.setRetMsg(Constants.FAIL_MESSAGE);
                       return resp;
                 }
                 if(rawTx!=null){
                     rawTx_pojo = new RawTxPojo();
                     rawTx_pojo.setRawTx(rawTx.getRawTx());
                     Map<String, Object> rtnMap = new HashMap<String, Object>();
                     rtnMap.put("transactionHash", rawTx.getRawTx());
                     resp.setRetCode(Constants.SUCCESSFUL_CODE);
                     resp.setRetMsg(Constants.SUCCESSFUL_MESSAGE);
                     resp.setData(rtnMap);
                     return resp;
                 }
                 return resp;
             }
         }

service code

     package com.biwork.service.Impl;

     import org.springframework.stereotype.Service;
     import org.apache.http.impl.client.CloseableHttpClient;
     import org.apache.http.impl.client.HttpClients;
     import org.apache.http.impl.conn.PoolingHttpClientConnectionManager;
     import org.slf4j.Logger;
     import org.slf4j.LoggerFactory;

     import com.biwork.entity.RawTx;
     import com.biwork.service.RawTxService;

     import org.web3j.protocol.Web3j;
     import org.web3j.protocol.core.methods.response.EthSendTransaction;
     import org.web3j.protocol.http.HttpService;

     import java.io.BufferedInputStream;
     import java.io.FileInputStream;
     import java.io.IOException;
     import java.io.InputStream;
     import java.math.BigDecimal;

     import com.biwork.exception.BusiException;


     import com.biwork.util.HttpUtil;
     import com.neemre.btcdcli4j.core.BitcoindException;
     import com.neemre.btcdcli4j.core.CommunicationException;
     import com.neemre.btcdcli4j.core.client.BtcdClient;
     import com.neemre.btcdcli4j.core.client.BtcdClientImpl;

     import java.util.HashMap;
     import java.util.Map;
     import java.util.Properties;

     @Service("RawTxService")
     public class RawTxServiceImpl implements RawTxService {

         static Logger log = LoggerFactory.getLogger(RawTxService.class);
         private static final String PRO_URL = "https://mainnet.infura.io/PVMw2QL6TZTb2TTgIgrs";

         @Override
         public RawTx getEthRawTx(String data) throws Exception {
             RawTx rawTx = new RawTx();
             Web3j web3j = Web3j.build(new HttpService(PRO_URL, true));
             EthSendTransaction ethSendTransaction = new EthSendTransaction();
             String hash = "";
             try {
                 ethSendTransaction = web3j.ethSendRawTransaction(data).send();
             } catch (IOException e) {
                 e.printStackTrace();
             }
             if (ethSendTransaction.hasError()) {
                 throw new BusiException(Integer.toString(ethSendTransaction.getError().getCode()), ethSendTransaction.getError().getMessage());
             } else {
                 hash = ethSendTransaction.getTransactionHash();
             }
             rawTx.setRawTx(hash);
             return rawTx;
         }

         @Override
         public RawTx getBtcRawTx(String data) throws Exception {
             RawTx rawTx = new RawTx();
             String txid = "";
             Map<String,String> params=new HashMap<String,String>();
             System.out.println("dataTwo = " + data);
             params.put("tx", data);
              try {
                  txid = HttpUtil.testPost(params, "https://blockchain.info/pushtx");
                  System.out.println("txid =" + txid);
              } catch (Exception e) {
                  throw new BusiException("pushtx", e.getMessage());
              }
             rawTx.setRawTx(txid);
             return rawTx;
         }
     }

So far, the chapter on Ethereum wallet development has been completed. If you want to learn more, please continue to pay attention. There will be more useful information in the actual project.
