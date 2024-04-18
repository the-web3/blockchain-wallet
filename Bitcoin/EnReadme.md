# Chapter 6: Bitcoin Wallet Development

The main contents of this chapter include: Bitcoin address and Bitcoin address generation, Bitcoin private key generation, Bitcoin transaction signature, and sending Bitcoin transactions to the blockchain network.

## 1. Bitcoin address

### 1.Bitcoin address prefix

Blockchain-based currencies use encoded strings, which are encoded in Base58Check but not Bech32. The encoding includes a prefix (traditionally a single version byte) that affects the leading symbols in the encoding result. Below is a list of some prefixes used in the Bitcoin codebase.
```
| Decimal prefix | Hexadecimal | Case usage | Preface identifier | Example |
|---------------|----------|--------------------- --------|---------------|------------------------- ----------------------------|
| 0 | 00 | Public Key Hash (P2PKH Address) | 1 | 17VZNX1SN5NtKa8UQFxwQbFeFc3iqRYhem |
| 5 | 05 | Script Hash(P2SH address) | 3 | 3EktnHQD7RiAE6uzMj2ZifT9YgRrkSgzQX |
| 128 | 80 | Private key (WIF, uncompressed public key) | 5 |5Hwgr3u458GLafKBgxtssHSPqJnYoGrSzgQsPwLFhLNYskDPyyA |
| 128 | 80 | Private key (WIF, compressed public key) | K or L |L1aW4aubDFB7yfras2S1mN3bqg9nwySY8nkoLmJebSLD5BWv3ENZ|
|4 136 178 30 | 0488B21E| BIP32 Public Key | 5e4cp9LB|
| 4 136 173 228 | 0488ADE4 | BIP32 Private Key | xprv | ZB21Teqt1VvEHx |
| 111 | 6F | Test public key hash | m or n | mipcBbFg9gMiCh81Kj8tqqdgoZub1ZJRfn |
| 196 | C4 | Test script hash | 2 | 2MzQwSSnBHWHqSAqtTVQ6v47XtaisrJa1Vc |
| 239 | EF | Testnet private key (WIF, uncompressed public key) | 9 | 92Pg46rUhgTT7romnV7iGW6W1gbGdeezqdbJCzShkCsYNzyyNcc |
| 239 | EF | Testnet private key (WIF, compressed public key) | c | cNJFgo1driFnPcBdBX8BrJrpxchBWXwXCvNH5SoSkdcF6JXXwHMm |
| 4 53 135 207 | 043587CF | Testnet BIP32 public key | tpub | tpubD6NzVbkrYhZ4WLczPJWReQycCJdd6YVWXubbVUFnJ5KgU5MDQrD998ZJLNGbhd2pq7ZtDiPYTfJ7iBenLVQpYgSQqPjU sQeJXH8VQ8xA67D|
| 4 53 131 148 | 04358394 | Testnet BIP32 private key | tprv | tprv8ZgxMBicQKsPcsbCVeqqF1KVdH7gwDJbxbzpCxDUsoXHdb6SnTPYxdwSAKDC6KKJzv7khnNWRAJQsRA8BBQyiSfYnRt6zuu 4vZQGKjeW4YF|
| | | Bech32 Public Key Hash and Script Hash | bc1 | bc1qw508d6qejxtdg4y5r3zarvary0c5xw7kv8f3t4 |
| | |Bech32 testnet public key hash and script hash| tb1 |tb1qw508d6qejxtdg4y5r3zarvary0c5xw7kxpjzsx|
```
Note that the private keys of compressed and uncompressed Bitcoin public keys use the same version bytes. The reason the compressed form starts with different characters is because the private key is appended with 0x01 bytes before base58 encoding.

### 2. Principle of Bitcoin address generation

The figure below is the conversion of elliptic curve public key to Bitcoin address

.：
     ![.：
](https://github.com/guoshijiang/blockchain-wallet/blob/master/img/PubKeyToAddr.jpg)


### 3. Bitcoin address generation code practice

Regarding the address generation part of Bitcoin, we mainly generate the main network address and the test network address, which are implemented using nodeJs and java.

#### 3.1. Mainnet Bitcoin address generation (NodeJs version)

The rng () function generates a random number seed. The following code uses the bitcoinjs library.

     const bitcoin = require('bitcoinjs-lib')
     const baddress = require('bitcoinjs-lib/src/address')
     const bcrypto = require('bitcoinjs-lib/src/crypto')
     const NETWORKS = require('bitcoinjs-lib/src/networks')

     // deterministic RNG for testing only
     function rng () { return Buffer.from('sssszzddzzzzzzzzzzzzzzzzzzzz') }

     const testnet = bitcoin.networks.testnet
     const keyPair = bitcoin.ECPair.makeRandom({ network: testnet, rng: rng })
     const wif = keyPair.toWIF()
     console.log("wif = " + wif)

     const { address } = bitcoin.payments.p2pkh({ pubkey: keyPair.publicKey, network: testnet })
     console.log("address = " + address)
    
#### 3.2. Testnet Bitcoin address generation (NodeJs version)

The following code generates the change address and non-change address

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

     //Normal address
     console.log(getAddress(key1))
     console.log(getAddress(key2))

     //Change address
     console.log(getAddress(key3))
     console.log(getAddress(key4))

#### 3.3. Mainnet Bitcoin address generation (Java version)



#### 3.4. Testnet Bitcoin address generation (Java version)


## 2. Bitcoin procedure suggestions

Check out the recommended Bitcoin fees website `https://bitcoinfees.earn.com/`

### 1. Which handling fee should be used?

On the website above you can see how much the current fee is per satoshi. Generally speaking, a medium transaction is 225 bits, and the fee results in 8550 satoshi. But many wallets use satoshis per kilobyte or bitcoins per kilobyte, so you may need to convert the units.

### 2. What is the cost shown?

The fees shown here are Satoshis (0.00000001 BTC) per byte of transaction data. Miners usually include transactions with the highest fees/bytes first. Wallets should base their fee calculations on this number based on how quickly the user needs to confirm.

### 3. What does transaction delay mean?

The latency shown here is the predicted number of blocks before the transaction will be confirmed. If a transaction is expected to be delayed 1-3 blocks, there is a 90% chance it will be within that range (approximately 10 to 30 minutes) Confirm.

Transactions with higher fees usually have 0 latency, meaning they are likely to be confirmed in the next block (usually around 5-15 minutes).

### 4. How to predict delays

Predictions are based on blockchain data from the past 3 hours, as well as the current unconfirmed transaction pool (mempool).

First, Monte Carlo simulation is used to predict possible future mempool and miner behavior. It can be seen from the simulation how quickly transactions with different fees are likely to be included in an upcoming block.

The prediction latencies shown here were chosen to represent 90% confidence intervals.

### 5. Bitcoin fee development API

Get current Bitcoin transaction fee predictions in JSON format for use with wallets or other services.

#### 5.1. Recommended transaction fee interface

1.Interface name:

     https://bitcoinfees.earn.com/api/v1/fees/recommended
    
2. Request method

     HTTP GET method

3. The interface returns data:

     { "fastestFee": 40, "halfHourFee": 20, "hourFee": 10 }

4. Parameter explanation

* astestFee: The lowest fee (satoshis per byte) that will currently result in the fastest transaction confirmations (typically 0 to 1 block latency).
* halfHourFee: The minimum fee (satoshis per byte) will confirm the transaction within half an hour (90% probability).
* hourFee: The minimum fee (satoshis per byte) will confirm the transaction within one hour (90% probability).

#### 5.2. Transaction Fee Summary

1.Interface name:

     https://bitcoinfees.earn.com/api/v1/fees/list
    
2. Request method

     HTTP GET method

3. The interface returns data:

     { "fees": [
       {"minFee":0,"maxFee":0,"dayCount":545,"memCount":87,
       "minDelay":4,"maxDelay":32,"minMinutes":20,"maxMinutes":420},
     ...
      ] }

4.Return

Returns a list of Fee objects containing predictions about fees in the given range from minFee to maxFee in satoshis/bytes.
Fee objects have the following properties (in addition to the minFee-maxFee range they refer to):

5. Parameter explanation

*dayCount: The number of confirmed transactions charged this fee in the past 24 hours.
*memCount: The number of unconfirmed transactions for this fee.
*minDelay: Estimated minimum delay in blocks before a transaction is confirmed (90% confidence interval).
* maxDelay: Estimated maximum delay in blocks before a transaction is confirmed (90% confidence interval).
*minMinutes: Minimum time in minutes before a transaction is confirmed (90% confidence interval).
* maxMinutes: Estimated maximum time in minutes before a transaction is confirmed (90% confidence interval).

6.Error code

* Status 503: Service unavailable (please wait while predictions are generated)
* Status 429: Too many requests (API rate limit reached)

## 3. Bit Developer API

Blockchain provides free-to-use APIs to help you start building Bitcoin applications. When using the blockchain API, you need to apply for a secret key. The address for applying for the secret key is as follows:

     https://api.blockchain.info/customer/signup
    

### 1. Payment process API (receive payment API V2)

A very simple way for websites to accept Bitcoin payments. This service is completely free and secure. Suitable for business or personal use. The user provides an extended public key (xPub) and the blockchain generates a unique, unused corresponding address for the user's client to send payments to. Blockchain will immediately notify you of payments to that address using the callback URL you selected.

Blockchain Accept Payments API V2 is the fastest and easiest way to start accepting automatic Bitcoin payments. With just a simple HTTP GET request, you can be up and running in minutes.

One of the difficulties involved in receiving Bitcoin payments is the need to generate a unique address for each new user or invoice. These addresses need to be monitored and stored securely. The blockchain payment API is responsible for address generation and monitoring. Whenever a payment is received, the blockchain notifies your server using a simple callback.

#### 1.1. Request API key to access Blockchain.info API

To use Receive Payments API V2, please request an API key via https://api.blockchain.info/v2/apikey/request/. This API key only works with our Receive Payments API. You cannot use standard blockchain wallet API keys with Receive Payments V2 and vice versa.

#### 1.2. Get the extended public key (xPub) [xPub can be created using our new blockchain wallet]

This API requires you to have a BIP 32 account with xPub to receive payments. The easiest way to start receiving payments is to open a blockchain wallet at https://blockchain.info/wallet/#/signup. You should create a new account within your wallet specifically for transactions facilitated by this API. Please use this account's xPub (located in Settings -> Addresses -> Manage -> More Options -> Show xPub) when making API calls.

#### 1.3. Generate a receiving address [GET] to provide your client with a unique, [unused Bitcoin address]

This method creates a unique address that should be presented to the customer. You will receive HTTP notifications for any payments sent to this address. Please note that each call to the server will increment the `index` parameter. This is done so that the same address is not displayed to two different customers. However, all funds will still appear in the same account.

     https://api.blockchain.info/v2/receive?xpub=$xpub&callback=$callback_url&key=$key

As defined in BIP 44, the wallet software will not scan more than 20 unused addresses. If enough requests from this API don't have matching payments, you can generate addresses outside of this range, which will make it very difficult to pay out funds to these addresses. Therefore, this API will return an error and refuse to generate new addresses if it detects it will create a gap of more than 20 unused addresses. If you encounter this error, you will need to switch to a new xPub (within the same wallet is ok), or receive a payment from one of the first 20 created addresses

You can control this behavior by optionally passing "gap_limit" as an additional URL parameter. Please note that this does not increase the number of addresses our servers will monitor. Passing the `gap_limit` parameter changes the maximum gap allowed before the API stops generating new addresses. Using this feature requires you to understand the gap limit and how to deal with it (advanced users only):

     https://api.blockchain.info/v2/receive?xpub=$xpub&callback=$callback_url&key=$key&gap_limit=$gap_limit

* xpub: your xPub (where you wish to pay)
* callback_url: The callback URL to be notified when payment is received. Remember to URL encode the callback URL when calling the create method.
* key: Your blockchain.info payment receiving v2 api key. Request an API key.
* gap_limit: optional. How many unused addresses are allowed before an error occurs.

Use xPub to export unused addresses:

     curl "https://api.blockchain.info/v2/receive?xpub=xpub6CWiJoiwxPQni3DFbrQNHWq8kwrL2J1HuBN7zm4xKPCZRmEshc7Dojz4zMah7E4o2GEEbD6HgfG7sQid186Fw9x9akMNKw2mu1PjqacTJB2&callback=https%3A%2 F%2Fmystore.com%3Finvoice_id%3D058921123&key=[yourkeyhere]"

Have your client send Bitcoin to the address included in the response:
Reply: 200 OK, application/json

     {"address":"19jJyiC6DnKyKvPg38eBE8R6yCSXLLEjqw","index":23,"callback":"https://mystore.com?invoice_id=058921123"}

PHP request example

     $secret = 'ZzsMLGKe162CfA5EcG6j';
     $my_xpub = '{YOUR XPUB ADDRESS}';
     $my_api_key = '{YOUR API KEY}';
     $my_callback_url = 'https://mystore.com?invoice_id=058921123&secret='.$secret;
     $root_url = 'https://api.blockchain.info/v2/receive';
     $parameters = 'xpub=' .$my_xpub. '&callback=' .urlencode($my_callback_url). '&key=' .$my_api_key;
     $response = file_get_contents($root_url . '?' . $parameters);
     $object = json_decode($response);
     echo 'Send Payment To : ' . $object->address;

Code address on github: https://github.com/blockchain/receive-payments-demos

#### 1.4. Balance update

This method monitors the addresses of your choice for which payments have been received and/or prepaid. You will be sent an HTTP notification immediately when a transaction is made and subsequently when the number of confirmations specified in the request is reached.

You need to specify the requested notification behavior. Setting the behavior to "DELETE" will delete the request after the first relevant notification is sent to your callback address. Setting the behavior to "KEEP" will send an additional notification each time a transaction with the specified acknowledgment and action type is sent to or from the address in the request.

The operation type is an optional parameter that indicates whether the address for received or spent transactions will be monitored, or both. By default, two operation types are monitored.

You can also choose to specify the number of confirmations a transaction reaches before sending notifications. Please note that you will be notified at 0 confirmations (i.e. immediately upon transaction) and again when the number of confirmations specified in the request is reached (3 confirmations by default).


     https://api.blockchain.info/v2/receive/balance_update

* Address: the address to be monitored
* Callback: The callback URL to be notified when payment is received.
* Key: Your blockchain.info receiving payments v2 api key. Request an API key.
* onNotification: Request notification behavior ('KEEP'|'DELETE).
* Confs: optional (default 3). The number of confirmations a transaction requires before a notification is sent.
* Op: Optional (default is "ALL"). The type of operation ('SPEND'|'RECEIVE'|'ALL') you wish to receive notifications for.

Monitor each address receiving payments with 5 confirmations:

     curl -H "Content-Type: text/plain" --data '{"key":"[your-key-here]","addr":"183qrMGHzMstARRh2rVoRepAd919sGgMHb","callback":"https://mystore. com?invoice_id=123","onNotification":"KEEP", "op":"RECEIVE", "confs": 5}' https://api.blockchain.info/v2/receive/balance_update

Reply: 200 OK,application/json

     {
       "id" : 70,
       "addr" : "183qrMGHzMstARRh2rVoRepAd919sGgMHb",
       "op" : "RECEIVE",
       "confs" : 5,
       "callback" : "https://mystore.com?invoice_id=123",
       "onNotification" : "KEEP"
     }

The id in the response can be used in delete requests:

     curl -X DELETE "https://api.blockchain.info/v2/receive/balance_update/70?key=[your-key-here]"

Reply: 200 OK, application/json

     { "deleted": true }

#### 1.5. Block notification [POST]

This method allows you to request a callback when a new block with a specified height and confirmation number is added to the blockchain.

As with balance update requests, you need to specify the requested notification behavior as "Keep" or "Delete".

height is an optional parameter indicating at what height you wish to receive block notifications - if not specified, this will be the height the next block reaches.

Confs is another optional parameter that indicates how many confirmations the block should have when sending notifications.

     https://api.blockchain.info/v2/receive/block_notification

* Callback: The callback URL to be notified when a block matching the query is added.
*Key: Your blockchain.info receive payments v2 api key. Request an API key.
* onNotification: Request notification behavior ('KEEP'|'DELETE).
* Confs: optional (default 1). The number of confirmations a block should have before sending notifications.
* Height: optional (default current chain height + 1). The height at which notifications should be sent.

Request a single notification when the Bitcoin blockchain reaches 500,000 blocks:

     curl -H "Content-Type: text/plain" --data '{"key":"[your-key-here]","height":500000,"callback":"https://mysite.com/ block?request_id=1234","onNotification":"DELETE"}' https://api.blockchain.info/v2/receive/block_notification

Reply: 200 OK, application/json

     {
       "id" : 64,
       "height" : 500000,
       "callback" : "https://mysite.com/block?request_id=1234",
       "confs" : 1,
       "onNotification" : "DELETE"
     }
    
The id in the response can be used in delete requests:

     curl -X DELETE "https://api.blockchain.info/v2/receive/block_notifcation/64?key=[your-key-here]"
    
Reply: 200 OK, application/json

     { "deleted": true }

#### 1.6. Implement callback processing (callback sent from blockchain.info)

##### 1.6.1. Receive and balance update callbacks

Please note that callback URLs are limited to 255 characters in length.

Blockchain.info will notify you at the specified callback URL when a payment is received to the address generated or monitored by the balance update request. For balance update callbacks, an additional notification will be sent once the transaction reaches the specified number of confirmations.

transaction_hash: payment transaction hash.
address: Target Bitcoin address (part of the xPub account).
Confirmations: The number of confirmations for this transaction.
Value: The value of the payment received (in satoshi, so divide by 100,000,000 to get the value in BTC).
{custom parameter}: Any parameters included in the callback URL will be passed back to the callback URL in the notification. You can use this feature to include parameters in the callback URL, such as invoice_id or customer_id, to track which payments are associated with which of your customers.

##### 1.6.2. Block notification callbacks

Each time a new block is added to the blockchain, a block notification is sent, matching the height and number of confirmations set in the notification request.

hash: block hash.
Confirmations: The number of confirmations for this block.
height: block height.
timestamp: A unix timestamp indicating the time the block was added.
size: block size in bytes.
{custom parameter}: Any parameters included in the callback URL will be passed back to the callback URL in the notification.

##### 1.6.3.PHP code example

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

##### 1.6.4. Expected callback response

To confirm successful handling of the callback, your server should reply with the text "*ok*" (no quotes) as plain text, no HTML. If the server responds in any other way, or responds with nothing, the callback will be resent again every new block (approximately every 10 minutes), up to 1000 times (1 week). It is possible to block callback domains in services that appear to be dead or never return an "*ok*" response.

#### 1.7. Check xPub address gap [GET]

Check the index gap between the last address paid and the last address generated using the checkgap endpoint. Use the xpub and API key you want to check as follows:

     curl "https://api.blockchain.info/v2/receive/checkgap?xpub=[yourxpubhere]]&key=[yourkeyhere]"
response

     {"gap":2}

#### 1.8. Callback log [GET] (debugging unpaid)

Use the callback_logs endpoint to view logs related to callback attempts. Use the relevant exact callback and your API key like this:

     curl "https://api.blockchain.info/v2/receive/callback_log?callback=https%3A%2F%2Fmystore.com%3Finvoice_id%3D05892112%26secret%3DZzsMLGKe162CfA5EcG6j&key=[yourkeyhere]"

response

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

#### 1.9. Security

Custom key parameters should be included in the callback URL. When the callback is triggered, the secret will be passed back to the callback script and should be checked by your code to see if it is valid. This prevents someone from trying to call your server and incorrectly marking an invoice as "paid."

#### 1.10. Currency conversion

Convert the value of local currency to BTC using the Exchange Rates API. will be introduced in the following content

#### 1.11. Double spending and refunds

Double spending occurs when a malicious user spends the same BTC twice. Payments that initially appear to be successful can be reversed later. This is counteracted by waiting for the transaction to be included in the blockchain and reach some confirmation. For high value transactions, 6 confirmations are generally considered safe.

Verify transaction confirmations in the callback script by checking the `$_GET['confirmations']` parameter. It is recommended that you confirm a transaction with zero confirmation, but only trust the transaction after confirmation. For example, if a product is purchased, we will show the order was successful at zero confirmations (first callback, but not yet responded with "*ok*"), but only ship once it reaches 4 or more confirmations. See the PHP demo callback.php for an example.

     if ($_GET['confirmations'] >= 6) {
         //Insert into confirmed payments
         echo '*ok*';
     } else {
         //Insert into pending payments
         //Don't print *ok* so the notification resent again on next confirmation
     }

#### 1.12.Address expiration

The receiving address never expires and will continue to be monitored until an "*ok*" is received in the callback response or blockchain.info has notified the callback 1000 times.

#### 1.13. Safe use

There is no limit to the number of receive addresses that can be generated (as long as the 20 address gap limit is met), and the service is designed to monitor millions of addresses.

It is possible to block callback domains in services that appear to be dead or never return an "*ok*" response.

### 2. Bitcoin Blockchain Wallet API (a simple API for Blockchain Wallet users to send and receive Bitcoin)

The Blockchain Wallet API provides a simple interface that merchants can use to interact with the wallet programmatically.

#### 2.1.AnPack

To use this API, you need to run a small local service that is responsible for managing the blockchain wallet. Your application interacts with this service locally through HTTP API calls. Here are the installation instructions

nodejs and npm are required to install and use this API service. Install:

     npm install -g blockchain-wallet-service
    
For optimal stability and performance, make sure to always use the latest version.

To check your version:

     blockchain-wallet-service -V

To update to the latest version:

     npm update -g blockchain-wallet-service
    
Require

* node >= 6.0.0
* npm >= 3.0.0

#### 2.2.Run

Start the blockchain wallet service using the following command:

     blockchain-wallet-service start --port 3000

The following are the problems you may encounter during installation and operation

* If you receive EACCESS or permission-related errors, you may need to run the installation as root using the sudo command.
* If you get errors about node-gyp or python, install using npm install --no-optional
* Failed to start node if using /usr/bin/env: No such file or directory, node may not be installed, or it may be installed under a different name (e.g. Ubuntu installed node as nodejs). If you installed node under a different name, create a symbolic link for the node binary: sudo ln -s/usr/bin/nodejs/usr/bin/node, or install node through Node Version Manager.
* If you see a TypeError claiming that object has no method 'compare', that's because your node version is older than 0.12 before the compare method was added to Buffers. Try upgrading to at least node version 0.12.
* If you get wallet decryption errors despite having the correct credentials, then you may not have Java installed, which is required as a dependency of the my-wallet-v3 module. Not installing Java during the npm installation process will result in the inability to decrypt the wallet. Download the JDK for Mac from here or run apt-get install default-jdk on a debian based linux system.
* If you receive a timeout response, additional authorization from your blockchain wallet may be required. This can happen when using an unrecognized browser or IP address. An email authorizing the API access attempt will be sent to the registered user, who will need to take steps to authorize future requests.

#### 2.3. Create wallet API

The create_wallet method can be used to create a new blockchain.info Bitcoin wallet.

Interface path: http://localhost:3000/api/v2/create
Request method: POST or GET
parameter
* password: Password of new wallet. Must be at least 10 characters in length.
* api_code: API code with create wallets permission.
* priv: Private key added to wallet (preferred wallet import format). (optional)
* label: The label set for the first address in the wallet. Alphanumeric only. (optional)
* email: The email associated with the new wallet, which represents the email address of the user you created this wallet with. (optional)

Please create the API code here, including permission to "Create Wallet".
Reply: 200 OK, application/json

     {
         "guid": "4b8cd8e9-9480-44cc-b7f2-527e98ee3287",
         "address": "12AaMuRnzw6vW6s2KPRAGeX53meTf8JbZS",
         "label": "Main address"
     }

#### 2.4. Payment

Send Bitcoins from your wallet to another Bitcoin address. All transactions include 0.0001BTC mining fee.

All Bitcoin values ​​are Satoshi, i.e. divided by 100000000 to get the amount in BTC. Base URL for all requests: https://blockchain.info/merchant/$guid/ guid should be replaced with your blockchain wallet identifier (found on the login page).

     http://localhost:3000/merchant/$guid/payment?password=$main_password&second_password=$second_password&to=$address&amount=$amount&from=$from&fee=$fee

* main_password: main blockchain wallet password
* second_password: Your second blockchain wallet password if double encryption is enabled.
* to: Endorse Bitcoin address.
* amount: the amount sent with satoshi.
* Send from a specific Bitcoin address (optional)
* fee satoshi transaction fee value (must be greater than default fee) (optional)
Reply: 200 OK, application/json

     { "message" : "Response Message" , "tx_hash" : "Transaction Hash", "notice" : "Additional Message" }

     { "message" : "Sent 0.1 BTC to 1A8JiWcwvpY7tAopUkSnGuEYHmzGYfZPiq" , "tx_hash" : "f322d01ad784e5deeb25464a5781c3b20971c1863679ca506e702e3e33c18e9c" , " notice" : "Some funds are pending confirmation and cannot be spent yet (Value 0.001 BTC)" }

#### 2.5. Send many transactions

Send a transaction to multiple recipients in the same transaction.

     http://localhost:3000/merchant/$guid/sendmany?password=$main_password&second_password=$second_password&recipients=$recipients&fee=$fee

* main_password: main blockchain wallet password
* second_password: Your second blockchain wallet password if double encryption is enabled.
*recipients is a JSON object using the Bitcoin address as the key, and the amount sent as the value (see below).
*Send from specific Bitcoin address (optional)
* fee satoshi transaction fee value (must be greater than default fee) (optional)
Reply: 200 OK, application/json

     {
         "1JzSZFs2DQke2B3S4pBxaNaMzzVZaG4Cqh": 100000000,
         "12Cf6nCcRtKERh9cQm3Z29c9MWvQuFSxvT": 1500000000,
         "1dice6YgEVBf88erBFra9BHf6ZMoyvG88": 200000000
     }

The above example would send 1BTC to 1JzSZFs2DQke2B3S4pBxaNaMzzVZaG4Cqh, 15BTC to 12Cf6nCcRtKERh9cQm3Z29c9MWvQuFSxvT and 2BTC to 1dice6YgEVBf88erBFra9BHf6ZMoyvG88 in the same transaction.

Reply: 200 OK, application/json

     { "message" : "Response Message" , "tx_hash" : "Transaction Hash" }

     { "message" : "Sent To Multiple Recipients" , "tx_hash" : "f322d01ad784e5deeb25464a5781c3b20971c1863679ca506e702e3e33c18e9c" }

PHP sample code

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

#### 2.6. Get wallet balance

Get the balance of the wallet. This should be used as an estimate only and includes unconfirmed transactions and possible double spend.

     http://localhost:3000/merchant/$guid/balance?password=$main_password

Reply: 200 OK, application/json

     { "balance": Wallet Balance in Satoshi }
     { "balance": 1000}

#### 2.7. List addresses

List all active addresses in the wallet. Also included is a 0 confirmed balance, which is used only as an estimate and includes unconfirmed transactions and possible double spends.

     http://localhost:3000/merchant/$guid/list?password=$main_password

Reply: 200 OK, application/json

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
             },{
                 "balance": 0,
                 "address": "17p49XUC2fw4Fn53WjZqYAm4APKqhNPEkY",
                 "total_received": 0
             }
         ]
     }


#### 2.8. Obtain the balance of the address

Retrieve the balance of a Bitcoin address. Query address balance by label is depreciated.

     http://localhost:3000/merchant/$guid/address_balance?password=$main_password&address=$address

* main_password: main blockchain wallet password
* address: the Bitcoin address to be found

Reply: 200 OK, application/json

     {"balance" : Balance in Satoshi ,"address": "Bitcoin Address", "total_received" : Total Satoshi Received}
     {"balance" : 50000000, "address" : "19r7jAbPDtfTKQ9VJpvDzFFxCjUJFKesVZ", "total_received" : 100000000}


#### 2.9. Create a new address

     http://localhost:3000/merchant/$guid/new_address?password=$main_password&second_password=$second_password&label=$label

* main_password: main blockchain wallet password
* second_password: Your second blockchain wallet password if double encryption is enabled.
* label An optional label to append to this address. It is recommended that this be a human-readable string, such as "Order Number: 1234". You can use this as a reference to check the balance of your order (recorded later)

Reply: 200 OK, application/json

     { "address" : "The Bitcoin Address Generated" , "label" : "The Address Label"}
     { "address" : "18fyqiZzndTxdVo7g9ouRogB4uFj86JJiy" , "label": "Order No : 1234" }

#### 2.10.Address management

##### 2.10.1. Archive address

To improve wallet performance, addresses that have not been used recently should be moved to archived status. They will still remain in the wallet, but will no longer be included in the "list" or "list-transaction" calls.

For example, if an invoice is generated for a user after the invoice has been paid, that address should be archived.

Alternatively, if you generate unique Bitcoin addresses for each user, you should archive users whose addresses have not been logged in recently (~30 days).

     http://localhost:3000/merchant/$guid/archive_address?password=$main_password&second_password=$second_password&address=$address

* main_password: main blockchain wallet password
* address: Bitcoin address to be archived

Reply: 200 OK, application/json

     {"archived" : "18fyqiZzndTxdVo7g9ouRogB4uFj86JJiy"}


##### 2.10.2. Unarchive address

     http://localhost:3000/merchant/$guid/unarchive_address?password=$main_password&second_password=$second_password&address=$address

* main_password main blockchain wallet password
* addressThe Bitcoin address to be unarchived

Reply: 200 OK, application/json

     {"active" : "18fyqiZzndTxdVo7g9ouRogB4uFj86JJiy"}


### 3. Blockchain data API

#### 3.1.Single block

     https://blockchain.info/rawblock/$block_hash

What else can you use? format=hex request block is returned in binary form (hexadecimal encoding)

     {
         "hash":"0000000000000bae09a7a393a8acded75aa67e46cb81f7acaa5ad94f9eacd103",
         "ver":1,
         "prev_block":"00000000000007d0f98d9edca880a6c124e25095712df8952e0439ac7409738a",
         "mrkl_root":"935aa0ed2e29a4b81e0c995c39e06995ecce7ddbebb26ed32d550a72e8200bf5",
         "time":1322131230,
         "bits":437129626,
         "nonce":2964215930,
         "n_tx":22,
         "size":9195,
         "block_index":818044,
         "main_chain":true,
         "height":154595,
         "received_time":1322131301,
         "relayed_by":"108.60.208.156",
         "tx":[--Array of Transactions--]
     }

#### 3.2.Single transaction

     https://blockchain.info/rawtx/$tx_hash

What else can you use? format = hex request transaction to be returned in binary form (hex encoding)

     {
         "hash":"b6f6991d03df0e2e04dafffcd6bc418aac66049e2cd74b80f14ac86db1e3f0da",
         "ver":1,
         "vin_sz":1,
         "vout_sz":2,
         "lock_time":"Unavailable",
         "size":258,
         "relayed_by":"64.179.201.80",
         "block_height, 12200,
         "tx_index":"12563028",
         "inputs":[


                 {
                     "prev_out":{
                         "hash":"a3e2bcc9a5f776112497a32b05f4b9e5b2405ed9",
                         "value":"100000000",
                         "tx_index":"12554260",
                         "n":"2"
                     },
                     "script":"76a914641ad5051edd97029a003fe9efb29359fcee409d88ac"
                 }

             ],
         "out":[

                     {
                         "value":"98000000",
                         "hash":"29d6a3540acfa0a950bef2bfdc75cd51c24390fd",
                         "script":"76a914641ad5051edd97029a003fe9efb29359fcee409d88ac"
                     },

                     {
                         "value":"2000000",
                         "hash":"17b5038a413f5c5ee288caa64cfab35a0c01914e",
                         "script":"76a914641ad5051edd97029a003fe9efb29359fcee409d88ac"
                     }
             ]
     }

#### 3.3. Chart data

     https://blockchain.info/charts/$chart-type?format=json

     {
         "values" : [
             {
                 "x" : 1290602498, //Unix timestamp
                 "y" : 1309696.2116000003
             }]
     }

#### 3.4. Block height

     https://blockchain.info/block-height/$block_height?format=json
    
     {
         "blocks" :
         [
             --Array Of Blocks at the specified height--
         ]
     }

#### 3.5.Single address

     https://blockchain.info/rawaddr/$bitcoin_address

* The address can be base58 or hash160
* Display optional limit parameters for n transactions, such as &limit = 50 (default: 50, maximum: 50)
* Optional offset parameter, used to skip the first n transactions, for example & offset = 100 (page 2 with limit 50)

         {
             "hash160":"660d4ef3a743e3e696ad990364e555c271ad504b",
             "address":"1AJbsFZ64EpEfS5UAjAfcUG8pH8Jn3rn1F",
             "n_tx":17,
             "n_unredeemed":2,
             "total_received":1031350000,
             "total_sent":931250000,
             "final_balance":100100000,
             "txs":[--Array of Transactions--]
         }

#### 3.6. Multiple addresses

     https://blockchain.info/multiaddr?active=$address|$address

* Multiple addresses separated by |
* The address can be base58 or xpub
* Display optional limit parameters for n transactions, such as &n= 50 (default: 50, maximum: 100)
* (optional offset parameter to skip the first n transactions, e.g. &offset = 100 (page 2 with limit 50)

         {
             "addresses":[

             {
                 "hash160":"641ad5051edd97029a003fe9efb29359fcee409d",
                 "address":"1A8JiWcwvpY7tAopUkSnGuEYHmzGYfZPiq",
                 "n_tx":4,
                 "total_received":1401000000,
                 "total_sent":1000000,
                 "final_balance":1400000000
             },

             {
                 "hash160":"ddbeb8b1a5d54975ee5779cf64573081a89710e5",
                 "address":"1MDUoxL1bGvMxhuoDYx6i11ePytECAk9QK",
                 "n_tx":0,
                 "total_received":0,
                 "total_sent":0,
                 "final_balance":0
             },

             "txs":[--Latest 50 Transactions--]

#### 3.7. Get unspent output

     https://blockchain.info/unspent?active=$address

* Multiple addresses allowed are separated by "|"
* The address can be base58 or xpub
* Show optional limit parameters for n transactions, e.g. &limit=50 (default: 250, maximum: 1000)
* Optional confirmation parameter to limit minimum confirmations, e.g. &confirmations=6

         {
             "unspent_outputs":[
                 {
                     "tx_age":"1322659106",
                     "tx_hash":"e6452a2cb71aa864aaa959e647e7a4726a22e640560f199f79b56b5502114c37",
                     "tx_index":"12790219",
                     "tx_output_n":"0",
                     "script":"76a914641ad5051edd97029a003fe9efb29359fcee409d88ac", (Hex encoded)
                     "value":"5000661330"
                 }
             ]
         }

tx hashes are in reverse byte order. This means that in order to get the html transaction hash from the JSON tx hash of the following transaction, you need to decode the hex (e.g. using this site). This will produce a binary output that you need to invert (last 8 bits/1byte moved to the front, second to last 8 bits/1byte needs to be moved to the second, etc.). Then once the reversed bytes are decoded you get the html transaction hash.

#### 3.8. Balance

     https://blockchain.info/balance?active=$address

* Use "|" to allow multiple addresses
* The delimited address can be base58 or xpub

Lists a summary of the balance for each address listed.

     {
         "1MDUoxL1bGvMxhuoDYx6i11ePytECAk9QK": {
             "final_balance": 0,
             "n_tx": 0,
             "total_received": 0
         },
         "15EW3AMRm2yP6LEF5YKKLYwvphy3DmMqN6": {
             "final_balance": 0,
             "n_tx": 2,
             "total_received": 310630609
         }
     }

#### 3.9. Latest block

     https://blockchain.info/latestblock

     {
         "hash":"0000000000000538200a48202ca6340e983646ca088c7618ae82d68e0c76ef5a",
         "time":1325794737,
         "block_index":841841,
         "height":160778,
         "txIndexes":[13950369,13950510,13951472]
      }


#### 3.11. Unconfirmed transaction

     https://blockchain.info/unconfirmed-transactions?format=json


     {
         "txs":[--Array of Transactions--]
     }

#### 3.12. Block

Block one day: `https://blockchain.info/blocks/$time_in_milliseconds?format=json`
Blocks for a specific pool: `https://blockchain.info/blocks/$pool_name? format = json`

     {
         "blocks" : [
         {
             "height" : 166107,
             "hash" : "00000000000003823fa3667d833a354a437bdecf725f1358b17f949c991bfe0a",
             "time" : 1328830483
         },
         {
             "height" : 166104,
             "hash" : "00000000000008a34f292bfe3098b6eb40d9fd40db65d29dc0ee6fe5fa7d7995",
             "time" : 1328828041
         }]
     }

### 4. Query API

Plaintext query api to retrieve data from blockchain.info

Some API calls can use CORS headers if you add the &cors=true parameter to the GET request

Please limit your queries to a maximum of 1 every 10 seconds. All Bitcoin values ​​are Satoshi, i.e. divide by 100000000 to get the amount in BTC

#### 4.1. Instant API

* getdifficulty: The current difficulty target is a decimal number
* getblockcount: current block height in the longest chain
* latesthash: the hash of the latest block
* bcperblock: BTC’s current block reward
* totalbc: total amount of Bitcoin in circulation (delayed up to 1 hour)
* probability: The probability of finding a valid block for each hashing attempt
* hashestowin: average number of hash attempts required to solve a block
* nextretarget: The block height of the next difficulty retargeting
* avgtxsize: Average transaction size over the past 1000 blocks. Change the number of blocks by passing an integer as the second argument, e.g. avgtxsize/2000
* avgtxvalue: average transaction value (1000 default)
* interval: average time between blocks (seconds)
* eta: Estimated time to next block (in seconds)
* avgtxnumber: average number of transactions per block (100 default)

#### 4.2. Address query

To filter by x confirmations, include the confirmation parameter

     e.g. /q/addressbalance/1EzwoHtiXB4iFwedPr49iywjZn2nnekhoj?confirmations=6

Only transactions with 6 or more confirmations are included. This is very important if you are dealing with valuable transactions.

* getreceivedbyaddress/Address: Get the total number of Bitcoins received by the address (in satoshi). Multiple addresses separated by | Do not use to process payments without confirmation parameters
* Add parameters start_time and end_time to limit reception to a specific time period. The time provided should be a unix timestamp in milliseconds. Multiple addresses separated by |
* getsentbyaddress/Address: Gets the total number of Bitcoins sent by an address (in satoshi). Multiple addresses separated by | Do not use to process payments without confirmation parameters
* addressbalance/Address: Get the balance of addresses (in satoshi). Multiple addresses separated by | Do not use to process payments without confirmation parameters
* addressfirstseen/ddress: The timestamp of the block that first confirmed the address.

#### 4.3. Transaction query

* txtotalbtcoutput/TxHash: Get the total output value of the transaction (in satoshi)
* txtotalbtcinput/TxHash: Get the total input value of the transaction (in satoshi)
* txfee/TxHash: fee included in the transaction (in satoshi)
* txresult/TxHash/Address: Calculate the result of the transaction sending or receiving Address. Multiple addresses separated by |

#### 4.4.Tools

* Addresstohash/Address: Convert Bitcoin address to hash 160
* Hashtoaddress/Hash: Convert hash value 160 to Bitcoin address
* Hashpubkey/Pubkey: Convert public key to hash 160
* Addrpubkey/Pubkey: Convert public key to address
* Pubkeyaddr/Address: Convert address to public key (if available)

#### 4.5.Miscellaneous

* unconfirmedcount: The number of pending unconfirmed transactions
*24hrprice: 24-hour weighted price from the largest exchanges
*marketcap: USD market capitalization (based on 24-hour weighted prices)
* 24hrtransactioncount: Number of transactions in the past 24 hours
*24hrbtcsent: Number of btc sent in the past 24 hours (in satoshi)
* hashrate: Estimated network hash rate in gigahash
* rejected: Find the reason why the provided tx or block hash was rejected (if any)

### 5.WebSocket API (real-time block data)

The WebSocket API allows developers to receive real-time notifications about new transactions and blocks. Websocket echo testing is useful for debugging.

#### 5.1.Connection address

     wss://ws.blockchain.info/inv

Once the socket is open, you can subscribe to the channel by sending an "op" message.

#### 5.2.Ping

     {"op":"ping"}

#### 5.3. Subscribe to unconfirmed transactions

Subscribe to be notified of all new Bitcoin transactions.

     {"op":"unconfirmed_sub"}

Unsubscribe

     {"op":"unconfirmed_unsub"}


#### 5.4. Subscription address

Receive new transactions to a specific Bitcoin address:

     {"op":"addr_sub", "addr":"$bitcoin_address"}

Unsubscribe

     {"op":"addr_unsub", "addr":"$bitcoin_address"}
    
News about the new deal:

     {
         "op": "utx",
         "x": {
             "lock_time": 0,
             "ver": 1,
             "size": 192,
             "inputs": [
                 {
                     "sequence": 4294967295,
                     "prev_out": {
                         "spent": true,
                         "tx_index": 99005468,
                         "type": 0,
                         "addr": "1BwGf3z7n2fHk6NoVJNkV32qwyAYsMhkWf",
                         "value": 65574000,
                         "n": 0,
                         "script": "76a91477f4c9ee75e449a74c21a4decfb50519cbc245b388ac"
                     },
                     "script":" 0959ef0f1d685756c012102618e08e0c8fd4c5fe539184a30fe35a2f5fccf7ad62054cad29360d871f8187d"
                 }
             ],
             "time": 1440086763,
             "tx_index": 99006637,
             "vin_sz": 1,
             "hash": "0857b9de1884eec314ecf67c040a2657b8e083e1f95e31d0b5ba3d328841fc7f",
             "vout_sz": 1,
             "relayed_by": "127.0.0.1",
             "out": [
                 {
                     "spent": false,
                     "tx_index": 99006637,
                     "type": 0,
                     "addr": "1A828tTnkVFJfSvLCqF42ohZ51ksS3jJgX",
                     "value": 65564000,
                     "n": 0,
                     "script": "76a914640cfdf7b79d94d1c980133e3587bd6053f091f388ac"
                 }
             ]
         }
     }

#### 5.5. Subscribe to new blocks

Receive notifications when new blocks are found. NOTE: If the chain is broken, you will receive multiple notifications for a specific block height.

     {"op":"blocks_sub"}

Unsubscribe

     {"op":"blocks_unsub"}
    
News about the new block:

     {
         "op": "block",
         "x": {
             "txIndexes": [
                 3187871,
                 3187868
             ],
             "nTx": 0,
             "totalBTCSent": 0,
             "estimatedBTCSent": 0,
             "reward": 0,
             "size": 0,
             "blockIndex": 190460,
             "prevBlockIndex": 190457,
             "height": 170359,
             "hash": "00000000000006436073c07dfa188a8fa54fefadf571fd774863cda1b884b90f",
             "mrklRoot": "94e51495e0e8a0c3b78dac1220b2f35ceda8799b0a20cfa68601ed28126cfcc2",
             "version": 1,
             "time": 1331301261,
             "bits": 436942092,
             "nonce": 758889471
         }
     }
   
   
Debugging OP:

     {"op":"ping_block"}

Reply to latest block

     {"op":"ping_tx"}

Reply to latest transaction. If any addresses are subscribed, it will reply to the latest transactions involving those addresses.

### 6. Exchange rate API (market price and exchange rate api)

CORS headers are available for some API calls if you add the cors=true parameter to the GET request

     https://blockchain.info/ticker
    
Returns a JSON object with currency codes as keys. "15m" is the market price delayed by 15 minutes, "last" is the most recent market price, and "symbol" is the currency symbol.

     {
       "USD" : {"15m" : 478.68, "last" : 478.68, "buy" : 478.55, "sell" : 478.68, "symbol" : "$"},
       "JPY" : {"15m" : 51033.99, "last" : 51033.99, "buy" : 51020.13, "sell" : 51033.99, "symbol" : "¥"},
       "CNY" : {"15m" : 2937.05, "last" : 2937.05, "buy" : 2936.25, "sell" : 2937.05, "symbol" : "¥"},
       "SGD" : {"15m" : 605.39, "last" : 605.39, "buy" : 605.22, "sell" : 605.39, "symbol" : "$"},
       "HKD" : {"15m" : 3709.91, "last" : 3709.91, "buy" : 3708.9, "sell" : 3709.91, "symbol" : "$"},
       "CAD" : {"15m" : 526.72, "last" : 526.72, "buy" : 526.58, "sell" : 526.72, "symbol" : "$"},
       "NZD" : {"15m" : 582.26, "last" : 582.26, "buy" : 582.1, "sell" : 582.26, "symbol" : "$"},
       "AUD" : {"15m" : 524.61, "last" : 524.61, "buy" : 524.46, "sell" : 524.61, "symbol" : "$"},
       "CLP" : {"15m" : 283014.81, "last" : 283014.81, "buy" : 282937.95, "sell" : 283014.81, "symbol" : "$"},
       "GBP" : {"15m" : 297.4, "last" : 297.4, "buy" : 297.32, "sell" : 297.4, "symbol" : "£"},
       "DKK" : {"15m" : 2756.84, "last" : 2756.84, "buy" : 2756.09, "sell" : 2756.84, "symbol" : "kr"},
       "SEK" : {"15m" : 3403.41, "last" : 3403.41, "buy" : 3402.49, "sell" : 3403.41, "symbol" : "kr"},
       "ISK" : {"15m" : 56797.78, "last" : 56797.78, "buy" : 56782.35, "sell" : 56797.78, "symbol" : "kr"},
       "CHF" : {"15m" : 447.19, "last" : 447.19, "buy" : 447.07, "sell" : 447.19, "symbol" : "CHF"},
       "BRL" : {"15m" : 1093.06, "last" : 1093.06, "buy" : 1092.77, "sell" : 1093.06, "symbol" : "R$"},
       "EUR" : {"15m" : 370.13, "last" : 370.13, "buy" : 370.03, "sell" : 370.13, "symbol" : "€"},
       "RUB" : {"15m" : 17806.28, "last" : 17806.28, "buy" : 17801.44, "sell" : 17806.28, "symbol" : "RUB"},
       "PLN" : {"15m" : 1557.38, "last" : 1557.38, "buy" : 1556.96, "sell" : 1557.38, "symbol" : "zł"},
       "THB" : {"15m" : 15398.04, "last" : 15398.04, "buy" : 15393.86, "sell" : 15398.04, "symbol" : "฿"},
       "KRW" : {"15m" : 494436.55, "last" : 494436.55, "buy" : 494302.27, "sell" : 494436.55, "symbol" : "₩"},
       "TWD" : {"15m" : 14340.68, "last" : 14340.68, "buy" : 14336.79, "sell" : 14340.68, "symbol" : "NT$"}
     }

URL: `https://blockchain.info/tobtc? money=USD&value=500`

Convert x value in provided currency to btc.

For example: `https://blockchain.info/tobtc? money=USD&value=500`

parameter

* currency: currency code. See list above.
* value: The value to be converted.

Returns the value in BTC.
response:

     10

### 7. Blockchain Charts and Statistics API

The Blockchain Charts & Statistics API provides a simple interface to programmatically interact with the charts and statistics displayed on blockchain.info.

Date parameters are represented as YYYY-MM-DDThh:mm:ss or YYYY-MM-DD. The time zone is UTC. Duration is expressed by connecting the number of time units and the time unit it represents (e.g. "1 year", "3 months", etc.). Available time units are: minutes, hours, days, weeks and years.

#### 7.1. Chart API (get the data behind the Blockchain chart)

This method can be used to obtain and manipulate the data behind the Blockchain.info chart.

* URL: https://api.blockchain.info/charts/$chartName?timespan=$timespan&rollingAverage=$rollingAverage&start=$start&format=$format&sampled=$sampled
* Request method: GET
* Example: https://api.blockchain.info/charts/transactions-per-second?timespan=5weeks&rollingAverage=8hours&format=json

* timespan: The duration of the chart, which defaults to 1 year for most charts and 1 week for mempool charts. (optional)
* rollingAverage: The duration over which the data should be averaged. (optional)
* start: date and time to start the chart. (optional)
* format: JSON or CSV, the default is JSON. (optional)
* sampled:Boolean set to 'true' or 'false' (default is 'true'). If true, limits the number of data points returned to ~1.5k for performance reasons. (optional)

Note that the values ​​of the chart can be expressed in scientific notation (14,627,700 is represented as 1.46277E7)

Reply: 200 OK, application/json

     {
       "status": "ok",
       "name": "Confirmed Transactions Per Day",
       "unit": "Transactions",
       "period": "day",
       "description": "The number of daily confirmed Bitcoin transactions.",
       "values": [
         {
           "x": 1442534400, // Unix timestamp (2015-09-18T00:00:00+00:00)
           "y": 188330.0
         },
         ...
     }

Reply: 200 OK, text/csv; character set = ASCII

     2015-09-18 00:00:00,188330.0
     2015-09-19 00:00:00,117999.0
     2015-09-20 00:00:00,105933.0
    

#### 7.2.Stats API (Get the data behind Blockchain statistics)

This method can be used to get the data behind Blockchain.info’s statistics.

URL: https://api.blockchain.info/stats
Method: GET
Example: https://api.blockchain.info/stats

Reply: 200 OK, application/json

     {
       "market_price_usd": 610.036975,
       "hash_rate": 1.8410989266292908E9,
       "total_fees_btc": 6073543165,
       "n_btc_mined": 205000000000,
       "n_tx": 233805,
       "n_blocks_mined": 164,
       "minutes_between_blocks": 8.2577,
       "totalbc": 1587622500000000,
       "n_blocks_total": 430098,
       "estimated_transaction_volume_usd": 1.2342976868108143E8,
       "blocks_size": 117490685,
       "miners_revenue_usd": 1287626.6577490852,
       "nextretarget": 431423,
       "difficulty": 225832872179,
       "estimated_btc_sent": 20233161880242,
       "miners_revenue_btc": 2110,
       "total_btc_sent": 184646388663542,
       "trade_volume_btc": 21597.09997288,
       "trade_volume_usd": 1.3175029536228297E7,
       "timestamp": 1474035340000
     }

#### 7.3.Pools API (obtain data of Blockchain pool information)

His method can be used to obtain the data behind Blockchain.info’s pool information.

URL: https://api.blockchain.info/pools? timespan = $timespan
Method: GET
Example: https://api.blockchain.info/pools? timespan=5days

$timespan calculates the duration of data, up to 10 days, defaults to 4 days. (optional)
Reply: 200 OK, application/json

     {
       "GHash.IO": 7,
       "95.128.48.209": 1,
       "NiceHash Solo": 1,
       "Solo CKPool": 2,
       "176.9.31.178": 1,
       "1Hash": 11,
       "217.11.225.189": 1,
       "Unknown": 10,
       "BitClub Network": 23,
       "Telco 214": 5,
       "HaoBTC": 29,
       "GBMiners": 2,
       "SlushPool": 44,
       "91.220.131.39": 1,
       "Kano CKPool": 13,
       "BTCC Pool": 74,
       "60.205.107.55": 1,
       "BitMinter": 1,
       "BitFury": 58,
       "AntPool": 87,
       "F2Pool": 104,
       "ViaBTC": 54,
       "BW.COM": 77,
       "BTC.com": 2,
       "47.89.51.25": 1,
       "74.118.157.122": 2
     }

## 4. Create and initiate Bitcoin transactions online



## 5. Bitcoin transaction offline signature

### 1. Single transfer signature

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


### 2. Batch transfer signature


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
                 txb.addOutput(sendInfo.addressAmount[j].toAddress, parseFloat(sendInfo.addressAmount[j].amount));
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


## 6. Send transaction to Bitcoin mainnet

Controller code

     @ResponseBody
         @RequestMapping(value="/btc_sendRawTransaction", method=RequestMethod.POST, produces="application/json;charset=utf-8;") @ApiOperation(value = "Send signed transaction data to the BTC blockchain network", notes = "Send signed transaction data to the BTC blockchain network", httpMethod = "POST")
         public RespPojo getBtcRawTx(HttpServletRequest request,@RequestBody
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


Service code

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
