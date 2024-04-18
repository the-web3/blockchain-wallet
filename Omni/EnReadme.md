# Chapter 9: omni-USDT wallet development

USDT is a virtual currency that pegs cryptocurrency to the U.S. dollar, the legal currency. It is a virtual currency that is held in a foreign exchange reserve account and supported by legal currency. It is also the most stable currency among digital currencies. USDT currently issues two tokens, one is an ERC20 Token based on the Ethereum standard, and the other is a token based on the Omni Layer protocol. On omni, the USDT token ID is 31. The tokens on omni are also the most widely supported tokens by current exchanges, while the ERC20 USDT Token seems to be used by very few people. The development of Ethereum ERC20 tokens has been mentioned in previous articles, so we will not elaborate further here.

Omni is a platform for creating and trading custom digital assets and currencies. It is a software layer built on top of the most popular, most audited, and most secure blockchain - Bitcoin. Omni transactions are Bitcoin transactions that enable next-generation functionality on the Bitcoin blockchain. Omni Core is an enhanced Bitcoin core that provides all the features of Bitcoin as well as evolved Omni Layer features.

Let’s explain the entire omni-USDT access process in the wallet.

## What is Omni Layer

Omni Layer is a communications protocol that uses the Bitcoin blockchain to enable features such as smart contracts, user currency, and decentralized peer-to-peer exchange. A common analogy used to describe the relationship of Omni Layer to Bitcoin is HTTP to TCP/IP: HTTP, like Omni Layer, is the more basic transport and application layer of TCP/IP and the Internet layer, like Bitcoin.

## What is Omni Core

Omni Core is a fast, portable Omni Layer implementation based on the Bitcoin Core codebase (currently 0.13). This implementation requires no external dependencies unrelated to Bitcoin Core and is native to the Bitcoin network like other Bitcoin nodes. It currently supports wallet mode and works seamlessly on three platforms: Windows, Linux and Mac OS. Omni Layer extensions are exposed through the JSON-RPC interface. Development has been integrated into the Omni Core product.


## 1. Omni wallet node construction

Since omni does not have a bare wallet node available, if you need to develop an omni wallet, you have to build your own wallet node.

### 1. Testnet


Testnet mode allows Omni Core to run on the Bitcoin testnet blockchain for security testing.

* To run Omni Core in testnet mode, run Omni Core with the following options: -testnet.
* To receive OMNI (and TOMNI) on testnet, send TBTC to moneyqMan7uh8FqdCA2BV5yZ8qVrc9ikLP. For every 1 TBTC, you will receive 100 OMNI and 100 TOMNI.

### 2.Wallet node construction


#### 2.1. Installation environment instructions

*Centos system
*C++ and java
* OmniCore 0.3.1 fork bitcoin 0.13

#### 2.2. Required dependencies

* libssl
Crypto library for random number generation and elliptic curve cryptography

* libboost
Application library, mainly using threads, data structures, etc., the version must be greater than 1.53

*libevent
Network, OS independent asynchronous network

#### 2.3. Optional dependencies

*miniupnpc
Firewall traversal support

* libdb4.8
Wallet storage (only required if the wallet is available)

*qt
Interface support (only required if the interface can be used)

* protobuf
Data exchange format in the payment protocol (only required if the interface can be used)

* libqrencode
Generate QR code (two-dimensional code, only required if the interface is available)

*univalue
Parse and generate

* libzmq3
Generate zmq messages (ZMQ, ZeroMQ, message queue), the version number must be greater than 4.x

If you do not have g++ on your system, please install it yourself

#### 2.4. Memory requirements

C++ compilers require a lot of memory. When compiling Bitcoin Core, it is recommended to have at least 1.5 GB of memory available. On fewer systems, gcc can be tuned to save memory with additional CXXFLAGS: `./configure CXXFLAGS="--param ggc-min-expand=1 --param ggc-min-heapsize=32768"
`
#### 2.5. Installation and configuration

##### 2.4.1. Install git. If you have git, you can skip this step.

     sudo yum install git
     sudo yum install pkg-config

##### 2.4.2. Install dependencies

     sudo yum install build-essential libtool autotools-dev automake pkg-config libssl-dev libevent-dev bsdmainutils
  
##### 2.4.3. Install boost library

     sudo yum install libboost-system-dev libboost-filesystem-dev libboost-chrono-dev libboost-program-options-dev libboost-test-dev libboost-thread-dev

In order to be compatible with various system versions, it is recommended to install all boost development packages

     sudo yum install libboost-all-dev

##### 2.4.4. The wallet requires BerkeleyDB. The db4.8 package can be found here. You can add the repository and install it using the following commands:

     sudo yum install software-properties-common
     sudo add-apt-repository ppa:bitcoin/bitcoin
     sudo yum update
     sudo yum install libdb4.8-dev libdb4.8++-dev

##### 2.4.5. Optional installation items

* libminiupnpc

        sudo yum install libminiupnpc-dev

*ZMQ dependencies

       sudo yum install libzmq3-dev


##### 2.4.5. GUI dependency installation

If you need to compile bitcoin-qt, you need to install the qt development environment. Both qt4 and qt5 are available. If both are installed, qt5 will be used by default. You can also use --with-gui=qt4 during configuration. Choose to use the qt4 version, or use --without-gui to choose not to compile the gui.

How to install qt5

     sudo yum install libqt5gui5 libqt5core5a libqt5dbus5 qttools5-dev qttools5-dev-tools libprotobuf-dev protobuf-compiler
How to install qt4

     sudo yum install libqt4-dev libprotobuf-dev protobuf-compiler
    
libqrencode libqrendoce is a support module for QR codes (QR codes), optional installation

     sudo yum install libqrencode-dev
    
If these environment packages are installed, they will be detected by configure, and bitcoin-qt will be compiled and generated by default.

##### 2.4.6.Clone code

     git clone https://github.com/OmniLayer/omnicore.git


##### 2.4.7.Build OmniCore

     cd omnicore/
     ./autogen.sh
     ./configure
     make && make install

After the compilation is completed, there will be omnicored, omnicore-cli and other executable files in omnicore/src/. It is executed in the same way as bitcoin and requires a configuration file named bitcoin.conf. The following is the command to synchronize data

     ./omnicored -conf=Configuration file directory -datadir=Data synchronization directory

After startup, you can view the log under the data synchronization directory/omnicore.log.

The time for omni to synchronize blocks is related to your network. It took me 3 days to complete the synchronization. Now there are about 550,000 blocks.

##### 2.4.8. Bitcoin basic configuration
Before starting, you need to configure the OmniCore configuration file bitcoin.conf located in the .bitcoin folder under the project directory.

     server=1
     txindex=1
     rpcuser=your rpc username
     rpcpassword=your rpc password
     rpcallowip=127.0.0.1
     rpcport=8332
     paytxfee=0.00001
     minrelaytxfee=0.00001
     datacarriersize=80
     logtimestamps=1
     omnidebug=tally
     omnidebug=packets
     omnidebug=pending


server=1 means enabling RPC access
txindex=1 represents the initial index of the transaction
recuser and rpcpassword represent authentication for rpc access,
rpcallowip and rpcport represent the IP address and port allowed to access the wallet.
paytxfee and minrelattxfee control the handling fees of Bitcoin transactions. Omni transactions are also a special type of Bitcoin transactions. Packaging and broadcasting also require payment to miners. Setting the handling fee too low will cause slow transaction confirmation or even transaction failure. Too high a handling fee will cause a waste of resources (converted based on the BTC price of 2018.09.13, every additional 0.0001btc consumed will waste 4rmb), so set a dynamically configured transaction fee Very necessary. To estimate Bitcoin transaction fees, you can use the following URL bitcoinfees.earn, buybitcoinworldwide. Assuming that the current estimated Bitcoin transaction fee is 0.0000001BTC/Byte, then paytxfee=0.00001BTC/kByte needs to be set.


##### 2.4.8. Use the node strip to use the RPC interface once

Import the address and execute the following command

     ./omnicore-cli importaddress 1C6UYrmbtvdi8dHZNnZD3YoVwit2mccSgw

Then go to the directory where you synchronized data to view the import log. The synchronization log effect is as follows.

.：
     ![.：
](https://github.com/guoshijiang/blockchain-wallet/blob/master/img/640.jpg)


To list UTXO, the command is as follows:

     /omnicore-cli "listunspent" 0 999999 '["1C6UYrmbtvdi8dHZNnZD3YoVwit2mccSgw"]'


.：
     ![.：
](https://github.com/guoshijiang/blockchain-wallet/blob/master/img/641.png)

## 2. Omni browser interface

A minimum wait time of 5-10 seconds is recommended between repeated calls to prevent triggering the API rate limiter. After triggering, the system will respond with the error message: `{‘error’:True, ‘msg’:‘Rate Limit Reached. Please limit consecutive requests to no more than “limit” every “time frame in seconds”.’}`. and subsequent calls will be blocked for up to one minute. If you keep calling repeatedly, the system will prohibit you from using the API interface.

### API detailed description

#### 1. Get balance

##### 1.1. Get the balance of multiple addresses at the same time

Interface name: https://api.omniexplorer.info/v2/address/addr
Request method: POST
Call example:
*Curl mode call

         curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -H "Content-Type: application/x-www-form-urlencoded" -d "addr=1FoWyxwPXuj4C6abqwhjDWdz6D4PZgYRjA&addr=1KYiKJEfdJtap9QX2v9BXJMpz2SfU4pgZw" "https : //api.omniwallet.org/v2/address/addr/"

* http method calling instructions

         POST /v2/address/addr/ HTTP/1.1
         Host: api.omniwallet.org
         Content-Type: application/x-www-form-urlencoded
         Content-Type: application/x-www-form-urlencoded

         addr=1FoWyxwPXuj4C6abqwhjDWdz6D4PZgYRjA&addr=1KYiKJEfdJtap9QX2v9BXJMpz2SfU4pgZw

Return results:

     {
         "1FoWyxwPXuj4C6abqwhjDWdz6D4PZgYRjA": {
             "balance": [
                 {
                     "pendingpos": "231154059052026",
                     "reserved": "0",
                     "divisible": true,
                     "symbol": "SP31",
                     "value": "81672408571806609",
                     "frozen": "0",
                     "pendingneg": "-16422699999141",
                     "id": "31"
                 },
                 {
                     "pendingpos": "0",
                     "divisible": true,
                     "symbol": "BTC",
                     "value": "860484222",
                     "pendingneg": "0",
                     "error": false,
                     "id": 0
                 }
             ]
         },
         "1KYiKJEfdJtap9QX2v9BXJMpz2SfU4pgZw": {
             "balance": [
                 {
                     "pendingpos": "0",
                     "reserved": "0",
                     "divisible": true,
                     "symbol": "SP31",
                     "value": "12587438660387443",
                     "frozen": "0",
                     "pendingneg": "0",
                     "id": "31"
                 },
                 {
                     "pendingpos": "0",
                     "divisible": true,
                     "symbol": "BTC",
                     "value": "8916129408",
                     "pendingneg": "0",
                     "error": false,
                     "id": 0
                 }
             ]
         }
     }

##### 1.2. Get the balance of a single address

Returns the balance information for the given address. For multiple addresses in a single query, the API above

Interface name: https://api.omniexplorer.info/v1/address/addr/
Request method: POST
Call example:
*Curl mode call

         curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -H "Content-Type: application/x-www-form-urlencoded" -d "addr=1EXoDusjGwvnjZUyKkxZ4UHEf77z6A5S4P" "https:// api.omniexplorer.info/v1/address/addr/"


* http method calling instructions

         POST /v1/address/addr/ HTTP/1.1
         Host: api.omniexplorer.info
         Content-Type: application/x-www-form-urlencoded
         Content-Type: application/x-www-form-urlencoded

         addr=1EXoDusjGwvnjZUyKkxZ4UHEf77z6A5S4P


Return results:

         {
             "balance": [
                 {
                     "divisible": true,
                     "frozen": "0",
                     "id": "1",
                     "pendingneg": "0",
                     "pendingpos": "0",
                     "reserved": "0",
                     "symbol": "OMNI",
                     "value": "3054147959984"
                 },
                 {
                     "divisible": true,
                     "frozen": "0",
                     "id": "2",
                     "pendingneg": "0",
                     "pendingpos": "0",
                     "reserved": "0",
                     "symbol": "T-OMNI",
                     "value": "0"
                 },
                 {
                     "divisible": true,
                     "error": false,
                     "id": 0,
                     "pendingneg": "0",
                     "pendingpos": "0",
                     "symbol": "BTC",
                     "value": "214236735"
                 }
             ]
         }
    
#### 2. Return the balance information and transaction history of the given address

Interface name: https://api.omniexplorer.info/v1/address/addr/details/
Request method: POST
Call example:
*Curl mode call

         curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -H "Content-Type: application/x-www-form-urlencoded" -d "addr=1EXoDusjGwvnjZUyKkxZ4UHEf77z6A5S4P" "https:// api.omniexplorer.info/v1/address/addr/details/"


* http method calling instructions

         POST /v1/address/addr/details/ HTTP/1.1
         Host: api.omniexplorer.info
         Content-Type: application/x-www-form-urlencoded
         Content-Type: application/x-www-form-urlencoded

         addr=1EXoDusjGwvnjZUyKkxZ4UHEf77z6A5S4P


Return results:

     {
         "address": "1EXoDusjGwvnjZUyKkxZ4UHEf77z6A5S4P",
         "balance": [
             {
                 "divisible": true,
                 "frozen": "0",
                 "id": "1",
                 "pendingneg": "0",
                 "pendingpos": "0",
                 "reserved": "0",
                 "symbol": "OMNI",
                 "value": "3054147959984"
             },
             {
                 "divisible": true,
                 "frozen": "0",
                 "id": "2",
                 "pendingneg": "0",
                 "pendingpos": "0",
                 "reserved": "0",
                 "symbol":"T-OMNI",
                 "value": "0"
             },
             {
                 "divisible": true,
                 "error": false,
                 "id": 0,
                 "pendingneg": "0",
                 "pendingpos": "0",
                 "symbol": "BTC",
                 "value": "214236735"
             }
         ],
         "pages": 3,
         "transactions": [
             {
                 "amount": "0.00000100",
                 "block": 342733,
                 "blockhash": "000000000000000014a6858e7da93606ffe624331a2f295732c8c9499ffa1025",
                 "blocktime": 1423503818,
                 "confirmations": 176418,
                 "divisible": true,
                 "fee": "0.00001000",
                 "ismine": false,
                 "positioninblock": 1569,
                 "propertyid": 1,
                 "propertyname": "Omni",
                 "sendingaddress": "1F9jCeixNbK6GEkdQFq1ejkbJjAGwVVVqG",
                 "txid": "318fb016144f3dadb16acf39cd7e00089b808418f1c4b343926af55882824966",
                 "type": "Send To Owners",
                 "type_int": 3,
                 "valid": true,
                 "version": 0
             },
             {
                 "additional records": "..."
             }
         ]
     }


#### 4. Returns the Armory-encoded version of the unsigned transaction for use with Armory offline transactions. data: unsigned_hex: raw bitcoin hex formatted tx converted to pubkey: pubkey of sending address

Interface name: https://api.omniexplorer.info/v1/armory/getunsigned
Request method: POST
Call example:
*Curl mode call

         curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -H "Content-Type: application/x-www-form-urlencoded" -d "unsigned_hex=01000000011c2d89d15d4853194e0e617e5d6d6d151752b09f72bf62a34453643270ebc763000000001976a914946cb2e08075bcbaf157e47bcb67eb2b2339d24288acffffffff01d8d60000000000001976a914946cb2e08075bcbaf157e47bcb67eb2b2339d24288ac00000000&pubkey=04ad90e5b6bc86b3ec7fac2c5fbda7423fc8ef0d58df594c773fa05e2c281b2bfe877677c668bd13603944e34f4818ee03cadd81a88542b8b4d5431264180e2c28" "https: //api.omniexplorer.info/v1/armory/getunsigned"


* http method calling instructions

         POST /v1/armory/getunsigned HTTP/1.1
         Host: api.omniexplorer.info
         Content-Type: application/x-www-form-urlencoded
         Content-Type: application/x-www-form-urlencoded

         unsigned_hex = 01000000011c2d89d15D4853194E0E617E5D6D6D6D6D151752BF72BF62A34453270EBC76300001976CB2E08075BCBAF157BCB6BCB6 7EB2B2339D2424288ACFFFFFFFF01D8D6000000000000001976A914946CB2E08075BCBAF157E47BCB67EB2339D2424288AC000000000000 & PubKey 2C5FBDA7423FC8EF0D58DF594C773FA05E2C28776777C668BD136034F4818EE03CADD8854D5431264180E2C28


Return results:

     {
         "armoryUnsigned": "=====TXSIGCOLLECT-7fK9x5wF======================================== = \ naqaaapm+TNKAAAAAAAF2GAQEAAAD5VRTZHC2J0V1IUXLODMF+XW1TFRDSSJ9YV2KJ \ NRFNKMNDRX2MAAAAA/ 91+LIUYJV3KSWG \ NZMXHNAAAAAAABQRZBEAAWH+ZK7+MQKY/RPARLWAXLKGMB2OI5JWXLRGODWIG \ RVee0biqix3EVJDPRTFPETFZ1Z1Z \ ND/SD7M8Z0/5DZUFPNFHPPFRINV ///8eyooaaaaaaaaaaAAAAAAAAAZDQKULGYY4IB1VLRXV+R7 \ NY2FRKIRJB3DAEAAAAGXAPFFFF JHMMJ4F756OJPTKTKHCMLUL83KWIKXG6GAA \ NaaaaABL2QRSCGUFAIQZKLABF7ZF7ZFYALB2GEYISYISYISYISYISYISAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAACII Raaaaaaaaad ///// Aueerzdltrygs+X + n================================ ================================"
     }


#### 5. Decode and return the original hex and signature status from the armory transaction. Data: armory_tx: armory transaction in text format

Interface name: https://api.omniexplorer.info/v1/armory/getrawtransaction
Request method: POST
Call example:
*Curl mode call

         curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -H "Content-Type: application/x-www-form-urlencoded" -d "armory_tx======TXSIGCOLLECT- 7fK9x5wF======================================
         AQAAAPm+tNkAAAAAAf2gAQEAAAD5vrTZHC2J0V1IUxlODmF+XW1tFRdSsJ9yv2Kj
         RFNkMnDrx2MAAAAA/SUBAQAAAAHb6cvxUOY3xjmumbvja4ws2Y91+lIUyjV3kswG
         ZmxHNAAAAABqRzBEAiAWH+zK7+MqKy/RpARlwAxlkGmB2oI5jwXCzwXlrgoDdwIg
         RIt5APjkheiGF64yCgGDXHpLdlB7cYRgvuEePdRvee0BIQIx3eVJDpRTFPeTFz1z
         D/sd7M8z0/5dZUFpNFHpPFrinv////8EYOoAAAAAAAAAZdqkUlGyy4IB1vLrxV+R7
         y2frKyM50kKIrJB3dAEAAAAAGXapFJHmmj4F756OJPTktKHCmlUL83kWiKxg6gAA
         AAAAABl2qRSCgUFaaIQzk5laBF7zFYAlb2gEyYisYOoAAAAAAAAAZdqkUgQAAAAAA
         AAACAAAAAAX14QAAAACIrAAAAAAAAAD/////AUEErZDltryGs+x/rCxfvadCP8jv
         DVjfWUx3P6BeLCgbK/6HdnfGaL0TYDlE409IGO4Dyt2BqIVCuLTVQxJkGA4sKAAA
         ATQBAAAA+b602Rl2qRSUbLLggHW8uvFX5HvLZ+srIznSQois2NYAAAAAAAAAARO
         T05FAAAAAAAAAA==
         ================================================== ==============" "https://api.omniexplorer.info/v1/armory/getrawtransaction"

* http method calling instructions

         POST /v1/armory/getrawtransaction HTTP/1.1
         Host: api.omniexplorer.info
         Content-Type: application/x-www-form-urlencoded
         Content-Type: application/x-www-form-urlencoded

         armory_tx======TXSIGCOLLECT-7fK9x5wF========================================AQAAAPm+tNkAAAAAAf2gAQEAAAD5vrTZHC2J0V1IUxlODmF+XW1tFRdSsJ9yv2Kj
         RFNkMnDrx2MAAAAA/SUBAQAAAAHb6cvxUOY3xjmumbvja4ws2Y91+lIUyjV3kswG
         ZmxHNAAAAABqRzBEAiAWH+zK7+MqKy/RpARlwAxlkGmB2oI5jwXCzwXlrgoDdwIg
         RIt5APjkheiGF64yCgGDXHpLdlB7cYRgvuEePdRvee0BIQIx3eVJDpRTFPeTFz1z
         D/sd7M8z0/5dZUFpNFHpPFrinv////8EYOoAAAAAAAAAZdqkUlGyy4IB1vLrxV+R7
         y2frKyM50kKIrJB3dAEAAAAAGXapFJHmmj4F756OJPTktKHCmlUL83kWiKxg6gAA
         AAAAABl2qRSCgUFaaIQzk5laBF7zFYAlb2gEyYisYOoAAAAAAAAAZdqkUgQAAAAAA
         AAACAAAAAAX14QAAAACIrAAAAAAAAAD/////AUEErZDltryGs+x/rCxfvadCP8jv
         DVjfWUx3P6BeLCgbK/6HdnfGaL0TYDlE409IGO4Dyt2BqIVCuLTVQxJkGA4sKAAA
         ATQBAAAA+b602Rl2qRSUbLLggHW8uvFX5HvLZ+srIznSQois2NYAAAAAAAAAARO
         T05FAAAAAAAAAA==
         ================================================== ==============

Return results:

     {
         "rawTransaction": "01000000011c2d89d15d4853194e0e617e5d6d6d151752b09f72bf62a34453643270ebc7630000000000ffffffff01d8d60000000000001976a914946c b2e08075bcbaf157e47bcb67eb2b2339d24288ac00000000",
         "signed": false
     }


#### 6. Decode signed transaction information

Interface name: https://api.omniexplorer.info/v1/decode/
Request method: POST
Call example:
*Curl mode call

         Curl-X Post -H "Content-Type: Application/X-WWW-Form-Urlencoded" -H "Content-Type: Application/X-WWW-Form-Urlencoded" -D 6897A5C5751D3B4bb841D1686DBF43DE16E1F4D581A2274EECD8ECD8E000000006B48 3045022100ac16E938D135341E7DE9E7EF3DB8DB04ED639888559 6D0F8DF260220297FC6FB9D39D9042B859B07DD78B634827F8D541213C16C0 D68B4073937201210312BA191886C6666624988949249889C6249889C62498 25ff4FA3C7E05479A08823feffff030000000000000000166F6D6E69000000000000000000000000 A1E9FE94688ac2202000000000000001976a91488d924F5103B74A8958FB57FD545529ACF9EB0700 "https: // api.omniexplorer.info/v1/decode/"


* http method calling instructions

         POST /v1/decode/ HTTP/1.1
         Host: api.omniexplorer.info
         Content-Type: application/x-www-form-urlencoded
         Content-Type: application/x-www-form-urlencoded

         HEX = 01000000018442ba55406897A5C5751D3B4bb841686DBF43DE16E1F4D581A2274EECD8E000000006B48304502138D1353441E9E9E7DE9E9E9E F3D232FB8DB04ED639885596D0F8DF260260297FC6FB9D39D9042B859B07DD 78B634827F8D541213C168B40739310312B2B2BA19187be8866 E4BFA4C5C66C66CBDAC7C6249825FF4FA3C7E05479A08823feffff0300000000000000166 A146F6D6E69000000000000000000000000000000000000197676025d87ddd 0602BD86308B354E038F82bA1E9FE94688ac220200000000001976a91488d924F5103B74A8958FD545529ACF9EB070070070070070070070070070070070070070070070070070070070070070077D


Return results:

         {
             "BTC": {
                 "hash": "5451c8e67d7ab3f947064337e8cf87c416f1fb163630913dcf307d4ca5d5ccaa",
                 "locktime": 519161,
                 "size": 257,
                 "txid": "5451c8e67d7ab3f947064337e8cf87c416f1fb163630913dcf307d4ca5d5ccaa",
                 "version": 1,
                 "vin": [
                     {
                         "scriptSig": {
                             "asm":" 16c0d68b40b60739372[ALL] 03124d5b2ba19187be886e4bfa4c5c66cbdac7c6249825ff4fa3c7e05479a08823",
                             "hex": "483045022100ac16e6e938d1353440b88d41e7de9e7ef3d232fb8db04ed639885596d0f8df260220297fc6fb9d39d9042b859b07dd78b634827f8d54121 3c16c0d68b40b60739372012103124d5b2ba19187be886e4bfa4c5c66cbdac7c6249825ff4fa3c7e05479a08823"
                         },
                         "sequence": 4294967294,
                         "txid": "8ecdee74221a584d1f6ee13df4db86161d84bbb4d351575c7a890654a52b44b8",
                         "vout": 0
                     }
                 ],
                 "vout": [
                     {
                         "n": 0,
                         "scriptPubKey": {
                             "asm": "OP_RETURN 6f6d6e69000000000000001f000000003b023380",
                             "hex": "6a146f6d6e69000000000000001f000000003b023380",
                             "type": "nulldata"
                         },
                         "value": 0.0
                     },
                     {
                         "n": 1,
                         "scriptPubKey": {
                             "addresses": [
                                 "1DWQ1gZ8VhL1fUCABqKbXtUZv63roGvXf"
                             ],
                             "asm": "OP_DUP OP_HASH160 025d87dd0602bd86308b354e038f82ba1e9fe946 OP_EQUALVERIFY OP_CHECKSIG",
                             "hex": "76a914025d87dd0602bd86308b354e038f82ba1e9fe94688ac",
                             "reqSigs": 1,
                             "type": "pubkeyhash"
                         },
                         "value": 0.00495989
                     },
                     {
                         "n": 2,
                         "scriptPubKey": {
                             "addresses": [
                                 "1DUb2YYbQA1jjaNYzVXLZ7ZioEhLXtbUru"
                             ],
                             "asm": "OP_DUP OP_HASH160 88d924f51033b74a895863a5fb57fd545529df7d OP_EQUALVERIFY OP_CHECKSIG",
                             "hex": "76a91488d924f51033b74a895863a5fb57fd545529df7d88ac",
                             "reqSigs": 1,
                             "type": "pubkeyhash"
                         },
                         "value": 5.46e-06
                     }
                 ],
                 "vsize": 257
             },
             "OMNI": {
                 "amount": "9.90000000",
                 "confirmations": 0,
                 "divisible": true,
                 "fee": "0.00003465",
                 "ismine": false,
                 "propertyid": 31,
                 "referenceaddress": "1DUb2YYbQA1jjaNYzVXLZ7ZioEhLXtbUru",
                 "sendingaddress": "1DWQ1gZ8VhL1fUCABqKbXtUZv63roGvXf",
                 "txid": "5451c8e67d7ab3f947064337e8cf87c416f1fb163630913dcf307d4ca5d5ccaa",
                 "type": "Simple Send",
                 "type_int": 0,
                 "version": 0
             },
             "Reference": "1DUb2YYbQA1jjaNYzVXLZ7ZioEhLXtbUru",
             "Sender": "1DWQ1gZ8VhL1fUCABqKbXtUZv63roGvXf",
             "error": "None",
             "inputs": {
                 "1DWQ1gZ8VhL1fUCABqKbXtUZv63roGvXf": 500000
             }
         }

#### 7. Return the currently valid/available base currency list of orders opened by omnidex. Data: Ecosystem: 1 for mainnet/production ecosystem, 2 for test/dev ecosystem

Interface name: https://api.omniexplorer.info/v1/omnidex/designatingcurrencies
Request method: POST
Call example:
*Curl mode call

         curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -H "Content-Type: application/x-www-form-urlencoded" -d "ecosystem=1" "https:// api.omniexplorer.info/v1/omnidex/designatingcurrencies"


* http method calling instructions

         POST /v1/omnidex/designatingcurrencies HTTP/1.1
         Host: api.omniexplorer.info
         Content-Type: application/x-www-form-urlencoded
         Content-Type: application/x-www-form-urlencoded

         ecosystem=1


Return results:

         {
             "currencies": [
                 {
                     "displayname": "Omni #1",
                     "propertyid": 1,
                     "propertyname": "Omni"
                 },
                 {
                     "displayname": "MaidSafeCoin #3",
                     "propertyid": 3,
                     "propertyname": "MaidSafeCoin"
                 },
                 {
                     "displayname": "TetherUS #31",
                     "propertyid": 31,
                     "propertyname": "TetherUS"
                 },
                 {
                     "additional records": "..."
                 }
             ],
             "status": 200
         }

#### 8. Return a list of transactions related to the queried Property ID (up to 10 per page). The returned transaction types include: Create Tx, Change Issuer Txs, Grant Txs, Revoke Txs, Crowdsale Participation Txs, Early Close Crowdsale tx

Interface name: https://api.omniexplorer.info/v1/properties/gethistory
Request method: POST
Call example:
*Curl mode call

         curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -H "Content-Type: application/x-www-form-urlencoded" -d "page=0" "https:// api.omniexplorer.info/v1/properties/gehistory/3"


* http method calling instructions

         POST /v1/properties/gehistory/3 HTTP/1.1
         Host: api.omniexplorer.info
         Content-Type: application/x-www-form-urlencoded
         Content-Type: application/x-www-form-urlencoded

         page=0


Return results:

         {
             "pages": 19,
             "total": 193,
             "transactions": [
                 {
                     "amount": "0",
                     "block": 297115,
                     "blockhash": "00000000000000004c93a71e1b0dd876c07cbc5ebd385ff8e5ba8379ffa1e377",
                     "blocktime": 1398154020,
                     "category": "Crowdsale",
                     "confirmations": 213140,
                     "data": "SAFE Network Crowdsale (MSAFE)",
                     "deadline": 1400749200,
                     "divisible": false,
                     "earlybonus": 10,
                     "ecosystem": "main",
                     "fee": "0.00010001",
                     "ismine": false,
                     "percenttoissuer": 0,
                     "positioninblock": 215,
                     "propertyid": 3,
                     "propertyiddesired": 1,
                     "propertyname": "MaidSafeCoin",
                     "propertytype": "indivisible",
                     "sendingaddress": "1ARjWDkZ7kT9fwjPrjcQyvbXDkEySzKHwu",
                     "subcategory": "MaidSafe",
                     "tokensperunit": "0.00003400",
                     "txid": "86f214055a7f4f5057922fd1647e00ef31ab0a3ff15217f8b90e295f051873a7",
                     "type": "Create Property - Variable",
                     "type_int": 51,
                     "url": "www.buysafecoins.com",
                     "valid": true,
                     "version": 0
                 },
                 {
                     "amount": "100.00000000",
                     "block": 297117,
                     "blockhash": "00000000000000002cf2f281f4a69c6757f9e468fd30b50c5c85433a7b17b1c5","blocktime": 1398155998,
                     "confirmations": 213138,
                     "divisible": true,
                     "fee": "0.00050000",
                     "ismine": false,
                     "issuertokens": "0",
                     "positioninblock": 83,
                     "propertyid": 1,
                     "purchasedpropertydivisible": false,
                     "purchasedpropertyid": 3,
                     "purchasedpropertyname": "MaidSafeCoin",
                     "purchasedtokens": "485781",
                     "referenceaddress": "1ARjWDkZ7kT9fwjPrjcQyvbXDkEySzKHwu",
                     "sendingaddress": "1LEVELTb6dkZmsUXgQE8nqc8naAVotX86D",
                     "txid": "4ec15360a03d23c069c2fd4a035cfee28af5ddfdd5a6839934dd2629129d477e",
                     "type": "Crowdsale Purchase",
                     "type_int": 0,
                     "valid": true,
                     "version": 0
                 },
                 {
                     "additional records": "..."
                 }
             ]
         }

#### 9. Return the attribute list created by the query address.

Interface name: https://api.omniexplorer.info/v1/properties/listbyowner
Request method: POST
Call example:
*Curl mode call

         curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -H "Content-Type: application/x-www-form-urlencoded" -d "addresses=1ARjWDkZ7kT9fwjPrjcQyvbXDkEySzKHwu" "https:// api.omniexplorer.info/v1/properties/listbyowner"

* http method calling instructions

         POST /v1/properties/listbyowner HTTP/1.1
         Host: api.omniexplorer.info
         Content-Type: application/x-www-form-urlencoded
         Content-Type: application/x-www-form-urlencoded

         addresses=1ARjWDkZ7kT9fwjPrjcQyvbXDkEySzKHwu

Return results:

         {
             "properties": [
                 {
                     "active": false,
                     "addedissuertokens": "0",
                     "amount": "0",
                     "amountraised": "93249.21601551",
                     "block": 297115,
                     "blockhash": "00000000000000004c93a71e1b0dd876c07cbc5ebd385ff8e5ba8379ffa1e377",
                     "blocktime": 1398154020,
                     "category": "Crowdsale",
                     "closedearly": true,
                     "closetx": "b8864525a2eef4f76a58f33a4af50dc24461445e1a420e21bcc99a1901740e79",
                     "confirmations": 213140,
                     "creationtxid": "86f214055a7f4f5057922fd1647e00ef31ab0a3ff15217f8b90e295f051873a7",
                     "data": "SAFE Network Crowdsale (MSAFE)",
                     "deadline": 1400749200,
                     "divisible": false,
                     "earlybonus": 10,
                     "ecosystem": "main",
                     "endedtime": 1398173740,
                     "fee": "0.00010001",
                     "fixedissuance": false,
                     "ismine": false,
                     "issuer": "1ARjWDkZ7kT9fwjPrjcQyvbXDkEySzKHwu",
                     "managedissuance": false,
                     "maxtokens": false,
                     "name": "MaidSafeCoin",
                     "percenttoissuer": 0,
                     "positioninblock": 215,
                     "propertyid": 3,
                     "propertyiddesired": 1,
                     "propertyname": "MaidSafeCoin",
                     "propertytype": "indivisible",
                     "sendingaddress": "1ARjWDkZ7kT9fwjPrjcQyvbXDkEySzKHwu",
                     "starttime": 1398154020,
                     "subcategory": "MaidSafe",
                     "tokensissued": "452552412",
                     "tokensperunit": "3400",
                     "totaltokens": "452552412",
                     "txid": "86f214055a7f4f5057922fd1647e00ef31ab0a3ff15217f8b90e295f051873a7",
                     "type": "Create Property - Variable",
                     "type_int": 51,
                     "url": "www.buysafecoins.com",
                     "valid": true,
                     "version": 0
                 }
         ],
             "status": "OK"
         }

#### 10. Return the currently active crowdsourcing list. Data: Ecosystem: 1 for production/mainnet ecosystem. 2 for testing/development ecosystem

Interface name: https://api.omniexplorer.info/v1/properties/listactivecrowdsales
Request method: POST
Call example:
*Curl mode call

         curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -H "Content-Type: application/x-www-form-urlencoded" -d "ecosystem=1" "https:// api.omniexplorer.info/v1/properties/listactivecrowdsales"

* http method calling instructions

         POST /v1/properties/listactivecrowdsales HTTP/1.1
         Host: api.omniexplorer.info
         Content-Type: application/x-www-form-urlencoded
         Content-Type: application/x-www-form-urlencoded

         ecosystem=1

Return results:

         {
             "crowdsales": [
                 {
                     "active": true,
                     "amountraised": "300000000",
                     "category": "Proof of Stake",
                     "creationtxid": "b01d1594a7e2083ebcd428706045df003f290c4dc7bd6d77c93df9fcca68232f",
                     "data": "Introducing ProzCoin which utilizes the new Proof of Action algorithm. PoA paysusers for article submission, moderating forums, interaction and participation. PoA adjusts to pay out higher rates for higher quality content based on user ratings.",
                     "deadline": 1406967060000,
                     "divisible": true,
                     "earlybonus": 0,
                     "fixedissuance": false,
                     "issuer": "1PRozi3UhpXtC4kZtPD1nfCFXJkXrV27Wp",
                     "name": "ProzCoin",
                     "percenttoissuer": 100,
                     "propertyid": 28,
                     "propertyiddesired": 24,
                     "propertyiddesiredname": "BitStrapAccessToken",
                     "starttime": 1406925999,
                     "subcategory": "Proof of Action",
                     "tokensissued": "600000000.00000000",
                     "tokensperunit": "100000000.00000000",
                     "totaltokens": "600000000.00000000",
                     "url": "http://Coinproz.com/Coinpage.html?alt=proz"
                 },
                 {
                     "additional records": "..."
                 }
             ],
             "status": "OK"
         }


#### 10 Returns a list of created properties filtered by the ecosystem. Data: Ecosystem: 1 for production/mainnet ecosystem. 2 for testing/development ecosystem


Interface name: https://api.omniexplorer.info/v1/properties/listbyecosystem
Request method: GET
Call example:
*Curl mode call

         curl -X GET -H "Content-Type: application/x-www-form-urlencoded" "https://api.omniexplorer.info/v1/properties/list"

* http method calling instructions

         POST /v1/properties/listbyecosystem HTTP/1.1
         Host: api.omniexplorer.info
         Content-Type: application/x-www-form-urlencoded
         Content-Type: application/x-www-form-urlencoded

         ecosystem=1
        
Return results:

         {
             "properties": [
                 {
                     "blocktime": 1377994675,
                     "category": "N/A",
                     "creationtxid": "0000000000000000000000000000000000000000000000000000000000000000",
                     "data": "Omni serve as the binding between Bitcoin, smart properties and contracts created on the Omni Layer.",
                     "divisible": true,
                     "fixedissuance": false,
                     "issuer": "1EXoDusjGwvnjZUyKkxZ4UHEf77z6A5S4P",
                     "managedissuance": false,
                     "name": "Omni",
                     "propertyid": 1,
                     "subcategory": "N/A",
                     "totaltokens": "617211.68177584",
                     "url": "http://www.omnilayer.org"
                 },
                 {
                     "additional records": "..."
                 }
             ],
             "status": "OK"
         }

#### 11. Return a list of all created properties.

Interface name: https://api.omniexplorer.info/v1/properties/list
Request method: GET
Call example:
*Curl mode call

         curl -X GET -H "Content-Type: application/x-www-form-urlencoded" "https://api.omniexplorer.info/v1/properties/list"

* http method calling instructions

         GET /v1/properties/list HTTP/1.1
         Host: api.omniexplorer.info
         Content-Type: application/x-www-form-urlencoded

Return results:

         {
             "properties": [
                 {
                     "blocktime": 1377994675,
                     "category": "N/A",
                     "creationtxid": "0000000000000000000000000000000000000000000000000000000000000000",
                     "data": "Omni serve as the binding between Bitcoin, smart properties and contracts created on the Omni Layer.",
                     "divisible": true,
                     "fixedissuance": false,
                     "issuer": "1EXoDusjGwvnjZUyKkxZ4UHEf77z6A5S4P",
                     "managedissuance": false,
                     "name": "Omni",
                     "propertyid": 1,
                     "subcategory": "N/A",
                     "totaltokens": "617211.68177584",
                     "url": "http://www.omnilayer.org"
                 },
                 {
                     "blocktime": 1377994675,
                     "category": "N/A",
                     "creationtxid": "0000000000000000000000000000000000000000000000000000000000000000",
                     "data": "Test Omni serve as the binding between Bitcoin, smart properties and contracts created on the Omni Layer.",
                     "divisible": true,
                     "fixedissuance": false,
                     "issuer": "1EXoDusjGwvnjZUyKkxZ4UHEf77z6A5S4P",
                     "managedissuance": false,
                     "name": "Test Omni",
                     "propertyid": 2,
                     "subcategory": "N/A",
                     "totaltokens": "563162.35759628",
                     "url": "http://www.omnilayer.org"
                 },
                 {
                     "additional records": "..."
                 }
             ],
             "status": "OK"
         }

#### 12. Search by transaction ID, address or attribute ID. Data: Query: Text string of transaction ID, address or attribute ID to search for

Interface name: https://api.omniexplorer.info/v1/search
Request method: POST
Call example:
*Curl mode call

         curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -H "Content-Type: application/x-www-form-urlencoded" -d "query=3" "https://api.omniexplorer.info/v1/search"

* http method calling instructions

         POST /v1/search HTTP/1.1
         Host: api.omniexplorer.info
         Content-Type: application/x-www-form-urlencoded
         Content-Type: application/x-www-form-urlencoded

         query=3


Return results:

         {
             "data": {
                 "address": {},
                 "asset": [
                     [
                         3,
                         "MaidSafeCoin",
                         "1ARjWDkZ7kT9fwjPrjcQyvbXDkEySzKHwu"
                     ]
                 ],
                 "tx": {}
             },
             "query": "3",
             "status": 200
         }

#### 13. Return the transaction list of the query address. Data: Address: The address of the query page: Loop through the available response pages (10 tx per page)

Interface name: https://api.omniexplorer.info/v1/transaction/address
Request method: POST
Call example:
*Curl mode call

         curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -H "Content-Type: application/x-www-form-urlencoded" -d "addr=1EXoDusjGwvnjZUyKkxZ4UHEf77z6A5S4P&page=0" "https: //api.omniexplorer.info/v1/transaction/address"


* http method calling instructions

         POST /v1/transaction/address HTTP/1.1
         Host: api.omniexplorer.info
         Content-Type: application/x-www-form-urlencoded
         Content-Type: application/x-www-form-urlencoded

         addr=1EXoDusjGwvnjZUyKkxZ4UHEf77z6A5S4P&page=0


Return results:

         {
             "address": "1EXoDusjGwvnjZUyKkxZ4UHEf77z6A5S4P",
             "pages": 3,
             "transactions": [
                 {
                     "amount": "0.00000100",
                     "block": 342733,
                     "blockhash": "000000000000000014a6858e7da93606ffe624331a2f295732c8c9499ffa1025",
                     "blocktime": 1423503818,
                     "confirmations": 176424,
                     "divisible": true,
                     "fee": "0.00001000",
                     "ismine": false,
                     "positioninblock": 1569,
                     "propertyid": 1,
                     "propertyname": "Omni",
                     "sendingaddress": "1F9jCeixNbK6GEkdQFq1ejkbJjAGwVVVqG",
                     "txid": "318fb016144f3dadb16acf39cd7e00089b808418f1c4b343926af55882824966",
                     "type": "Send To Owners",
                     "type_int": 3,
                     "valid": true,
                     "version": 0
                 },
                 {
                     "additional records": "..."
                 }
             ]
         }

#### 14. Broadcast the signed transaction to the network. Data: signedTransaction: signed hex broadcast

Interface name: https://api.omniexplorer.info/v1/transaction/pushtx
Request method: POST
Call example:
*Curl mode call

         curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -H "Content-Type: application/x-www-form-urlencoded" -d "signedTransaction=" "https://api .omniexplorer.info/v1/transaction/pushtx/"

* http method calling instructions

         POST /v1/transaction/pushtx/ HTTP/1.1
         Host: api.omniexplorer.info
         Content-Type: application/x-www-form-urlencoded
         Content-Type: application/x-www-form-urlencoded

         Signedtransaction = 01000000010B7AD3946C6FCD15D39FD4312C4F8DFD68C51A61075CFB0722C4403EEF1000000008A47302206Eabf81cc4C4C4C6CEE3cee3 DEDC6EA9BE46bb685C1B61DA577bb9aead8188B802201404284C52AD2E8 57BD3931FB0E1646575D1E72CDCDCDCDC7BEE1C191014AD90E5B6E BC86B3EC7FAC2C2C5FBDA7423FC8EF0D58DF594C773FA05E2C28777677 C668BD1360394F4818EE03CADD81A885431264180E2C28FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF fff0470170000000000001976A9143B00000000000000000000000000009d828d96000088ac701 700000000001976a9143C79155509D8578E2BF0E0E0E0E0E0E0E0E0E0E0E0E0E0E0E0E0E0E0E0E0E0E0E0E0E0E0E0E0E0E0E0E 00001976A914946CB2E08075BCBAF157E47BCB67EB2B2339D24242400000000001976A914946CBAF157BCB67EB2339D242888AC000000000000000000000000000000


Return results:

         {
             "status": "OK",
             "pushed": "Success",
             "tx": "9975dcd1f574fe8356183e34eb435df6aa5620e7f9fea98a3c2349a10383a62b"
         }

#### 15. Return transaction details of the queried transaction hash

Interface name: https://api.omniexplorer.info/v1/transaction/tx
Request method: GET
Call example:
*Curl mode call

         curl -X GET -H "Content-Type: application/x-www-form-urlencoded" "https://api.omniexplorer.info/v1/transaction/tx/e0e3749f4855c341b5139cdcbb4c6b492fcc09c49021b8b15462872b4ba69d1b"

* http method calling instructions

         GET /v1/transaction/tx/e0e3749f4855c341b5139cdcbb4c6b492fcc09c49021b8b15462872b4ba69d1b HTTP/1.1
         Host: api.omniexplorer.info
         Content-Type: application/x-www-form-urlencoded

Return results:
{
             "amount": "6167.00000000",
             "block": 511660,
             "blockhash": "0000000000000000003f37e72e599fbdaa14396a2e9251e493f0d7d15b1fd915",
             "blocktime": 1520009505,
             "confirmations": 7499,
             "divisible": true,
             "fee": "0.00009124",
             "ismine": false,
             "positioninblock": 825,
             "propertyid": 31,
             "propertyname": "TetherUS",
             "referenceaddress": "3GyeFJmQynJWd8DeACm4cdEnZcckAtrfcN",
             "sendingaddress": "3D4r9ERiM3HSc4eC4EhcT31tXoSV96HsPg",
             "txid": "e0e3749f4855c341b5139cdcbb4c6b492fcc09c49021b8b15462872b4ba69d1b",
             "type": "Simple Send",
             "type_int": 0,
             "valid": true,
             "version": 0
         }


## 3. Wallet development

### 1. Use wallet nodes for transfer development

If your address was not created on the node, then you need to import the address in order to find the UTXO. If the address is created on the node and the transfer occurs on the node, just obtain the UTXO directly.

#### 1.1. Import address

     ./omnicore-cli importaddress 1C6UYrmbtvdi8dHZNnZD3YoVwit2mccSgw

#### 1.2. List UTXO

The prerequisite that you need to import the address to obtain UTXO is that the above address has scanned all blocks and imported them.

     ./omnicore-cli "listunspent" 0 999999 '["1C6UYrmbtvdi8dHZNnZD3YoVwit2mccSgw"]'
    
The following are the results of the query

     [
       {
         "txid": "4f5b2b5ac6f7402f2edd8d4dafbdcca71758076d13f9b58dccd02ad76ac0f2ce",
         "vout": 0,
         "address": "1C6UYrmbtvdi8dHZNnZD3YoVwit2mccSgw",
         "account": "",
         "scriptPubKey": "76a91479b275dd5f136c241f3c28549b4c338177d5cb2188ac",
         "amount": 0.01411554,
         "confirmations": 105,
         "spendable": false,
         "solvable": false
       }
     ]


#### 1.3. Determine the amount of USDT to be transferred out

     ./omnicore-cli omni_createpayload_simplesend 31 5.0

31 in the above code means that the token ID of USDT on Omni is 31, which is similar to Ethereum's ERC20 token. 2.0 means that 2 USDT need to be transferred out.

The execution results are as follows:

     000000000000001f000000001dcd6500

#### 1.4.Create transaction

This is the same as Bitcoin, the code is as follows:

     ./omnicore-cli "createrawtransaction" '[{"txid":"4f5b2b5ac6f7402f2edd8d4dafbdcca71758076d13f9b58dccd02ad76ac0f2ce","vout":0}]' '{}'

The execution results are as follows:
    
     0100000001cef2c06ad72ad0cc8db5f9136d075817a7ccbdaf4d8ddd2e2f40f7c65a2b5b4f0000000000ffffffff0000000000
        

#### 1.5. Bind token data to the transaction

     ./omnicore-cli omni_createrawtx_opreturn 0100000001cef2c06ad72ad0cc8db5f9136d075817a7ccbdaf4d8ddd2e2f40f7c65a2b5b4f0000000000ffffffff0000000000 0001f000000001dcd6500
    
The execution results are as follows:
     0100000001cef2c06ad72ad0cc8db5f9136d075817a7ccbdaf4d8ddd2e2f40f7c65 a2b5b4f0000000000ffffffff010000000000000000166a146f6d6e690000000000 00001f000000001dcd650000000000

#### 1.6. Add the address for receiving coins

     ./omnicore-cli omni_createrawtx_reference 0100000001cef2c06ad72ad0cc8db5f9136d075817a7ccbdaf4d8ddd2e2f40f7c65a2b5b4f0000000000ffffffff010000000000000000166a1 46f6d6e69000000000000001f000000001dcd650000000000 1DefiYRCAD4wVS7rXwFkqhEn6R88EkSUnh
    
Results of the
            
     0100000001cef2c06ad72ad0cc8db5f9136d075817a7ccbdaf4d8ddd2e2f40f7c65 a2b5b4f0000000000ffffffff020000000000000000166a146f6d6e690000000000 00001f000000001dcd650022020000000000001976a9148ac137fb413490ec6f69792552c5f5ddf2ecd4cf88ac00000000
    
#### 1.6. Set change and mining fee

Instructions: (transaction HASH, transaction information, change address, handling fee)

     ./omnicore-cli omni_createrawtx_change 0100000001cef2c06ad72AD72AD0C8F9136D075817CCBDAF4DDDD2E2F7C65B5B5000000000000000000000000000000 166A146F6D6E69000000000000001F000000001dcd65002000000000000001976a9148AC137FB413490697925F5DDF2ECD4CF88AC00000000 ' 402F2edd8d4DAFBDCCA71758076D13F9B58DCCD02AD76AC0F2ce "," Vout ": 0," scriptpubkey ":" 76A91479B275F136C28549B4C338177CB211 88AC "," Value ": 0.01411554}] '1C6UYRMBTVDI8DHZD3YOVWIT2MCCSGW" 0.0002 "
    
Results of the:   

     0100000001cef2c06ad72ad0cc8db5f9136d075817a7ccbdaf4d8ddd2e2f40f7c65a2b5b4f0000000000ffffffff03a0391500000000001976a91479b275dd5f136c241f3c28549b4c338177d5cb2188ac0000000000000000166a146f6d6e69000000000000001f000000001dcd650022020000000000001976a9148ac137fb413490ec6f69792552c5f5ddf2ecd4cf88ac00000000

#### 1.7. Sign the transaction

Signature instructions:

     ./Mnicore-CLI SINRAWTRANSACTION 1976A91479B275F136C241F3C28549B4C338177D5CB2188AC0000000000000000166a146 F6D6E690000000000000000000000001dcd65002000000001976AC137FB41349 0EC6F69792552C5F5DDF2ECD4CF88AC00000000
    
Results of the:

     {
       "he": "0100000001cef2c06ad72ad72ad0cc8db5f9136D075817CBDAF4DDD2E2F40F7C65A2B5B4F0000000000000000000000001976795F136666 C241F3C28549B4C338177D5CB2188ac0000000000000000166F6D6E69000000000000000000000000001dcd65002222000000001976fb413497925F5DDDF2 ECD4CF88ac000000 ",
       "complete": false,
       "errors": [
         {
           "txid": "4f5b2b5ac6f7402f2edd8d4dafbdcca71758076d13f9b58dccd02ad76ac0f2ce",
           "vout": 0,
           "scriptSig": "",
           "sequence": 4294967295,
           "error": "Operation not valid with the current stack size"
         }
       ]
     }

The reason for the above error is that your address is not generated on the node, but imported by importing the address. In this case, you need to add the private key when signing.

as follows:

     ./omnicore-cli signrawtransaction 0100000001cef2c06ad72ad0cc8db5f9136d075817a7ccbdaf4d8ddd2e2f40f7c65a2b5b4f0000000000ffffffff03a0391500000000001976a91479b275dd5f13 6c241f3c28549b4c338177d5cb2188ac0000000000000000166a146f6d6e69000 000000000001f000000001dcd650022020000000000001976a9148ac137fb413490ec6f69792552c5f5ddf2ecd4cf88ac00000000 '[]' '["Private Key"]'

The execution results are as follows:

     {
       "hex": "Signature string",
       "complete": true
     }

#### 1.7. Send transactions to the blockchain network

The instructions are as follows:

     ./omnicore-cli sendrawtransaction signature string
    
Return results:

     2ae377b5d928132910ffa4419d52913c34d90baec3ed53fc621a07a9bcfb2bcf


### 2.NodeJS offline signature development USDT wallet

Signature code:

     function addPreZero(num){
         var t = (num+'').length,
             s = '';
         for(var i=0; i<16-t; i++){
             s += '0';
         }
         return s+num;
     }

     function usdtSign(privateKey, utxo, feeValue, usdtValue, fromAddress, toAddress) {
         var txb = new bitcoin.TransactionBuilder();
         var set = bitcoin.ECPair.fromWIF(privateKey);
         const fundValue = 546;
         var usdtAmount = parseInt(usdtValue*1e8).toString(16);
         var totalUnspent = 0;
         for(var i = 0; i < utxo.length; i++){
             totalUnspent = totalUnspent + utxo[i].value;
         }
         const changeValue = totalUnspent - fundValue - (feeValue*1e8);
         if (totalUnspent < feeValue + fundValue) {
             console.log("Total less than fee");
             return constant.LessValue;
         }
         for(var i = 0; i< utxo.length; i++){
             txb.addInput(utxo[i].tx_hash_big_endian, utxo[i].tx_output_n, 0xfffffffe);
         }
         const usdtInfo = [
             "6f6d6e69",
             "0000",
             "00000000001f",
             addPreZero(usdtAmount)
         ].join('');
         const data = Buffer.from(usdtInfo, "hex");
         const omniOutput = bitcoin.script.compile([
             bitcoin.opcodes.OP_RETURN,
             data
         ]);
         txb.addOutput(toAddress, fundValue);
         txb.addOutput(omniOutput, 0);
         txb.addOutput(fromAddress, changeValue);
         for(var i = 0;i < utxo.length; i++){
             txb.sign(0, set);
         }
         return txb.buildIncomplete().toHex();
     };

The method of obtaining UXTO in the above code is the same as that of Bitcoin.

Case code:

     var utxo ={
         "unspent_outputs":[
             {
                 "tx_hash":"1bf1e457ac7572518cde36945e94728659dfae7fb2229411c1e13c085054c506",
                 "tx_hash_big_endian":"06c55450083ce1c1119422b27faedf598672945e9436de8c517275ac57e4f11b",
                 "tx_index":399167492,
                 "tx_output_n": 2,
                 "script":"76a91479b275dd5f136c241f3c28549b4c338177d5cb2188ac",
                 "value": 1363462,
                 "value_hex": "14ce06",
                 "confirmations":0
             }
         ]
     }

     var privateKey = "private key";
     var usdtValue = 2;
     var feeValue = 0.0002;
     var fromAddress = "1C6UYrmbtvdi8dHZNnZD3YoVwit2mccSgw";
     var toAddress = "1DefiYRCAD4wVS7rXwFkqhEn6R88EkSUnh";
     var sign = usdtSign(privateKey, utxo.unspent_outputs, feeValue, usdtValue, fromAddress, toAddress);
     console.log(sign);

After the signature is successful, call the Omni blockchain browser interface `https://api.omniexplorer.info/v1/transaction/pushtx` to send it to the blockchain network.
