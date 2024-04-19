# Chapter 4: BIP Wallet Layered Protocol

## 1. Why layered protocols are needed

### 1. Non-deterministic wallet generates Ethereum address

     var keythereum = require("keythereum");
     var params = { keyBytes: 32, ivBytes: 16 };

     keythereum.create(params, function (dk) {
         var options = {
           kdf: "pbkdf2",
           cipher: "aes-128-ctr",
           kdfparams: {
             c: 262144,
             dklen: 32,
             prf: "hmac-sha256"
           }
         };
         var password = "wheethereum";
         var kdf = "pbkdf2";
         keythereum.dump(password, dk.privateKey, dk.salt, dk.iv, options, function (keyObject) {
           console.log(keyObject.address)
           keythereum.exportToFile(keyObject);
       });
     });
    
To run the above code, you need a nodejs environment and install the keythereum library. The above code is the code to generate the Ethereum keystore. The keystore contains information such as the address, encrypted private key, and MAC address. dk.privateKey is the plain text private key. There is no concept of mnemonic words in the above code.

### 2. Deterministic layered wallet Ethereum address generation

     var bip39 = require('bip39')
     var hdkey = require('ethereumjs-wallet/hdkey')
     var util = require('ethereumjs-util')

     var mnemonic = bip39.generateMnemonic()
     var seed = bip39.mnemonicToSeed(mnemonic)
     var hdWallet = hdkey.fromMasterSeed(seed)

     var key1 = hdWallet.derivePath("m/44'/60'/0'/0/0")
     console.log(key1._hdkey._privateKey.toString('hex'))
     var address1 = util.pubToAddress(key1._hdkey._publicKey, true).toString('hex')
    
The above code is an Ethereum address and private key generated through a strict layered wallet mechanism. The private key here is in plain text. If you need a ciphertext private key, you can write a code to encode the private key yourself. Encoding, here is a piece of code to encode the private key:

         var encrypt = function (str, pwd) {
           if(pwd == null || pwd.length <= 0) {
             console.log("Please enter a password with which to encrypt the message");
             return null;
           }
           var prand = "";
           for(var i=0; i<pwd.length; i++) {
             prand += pwd.charCodeAt(i).toString();
           }
           var sPos = Math.floor(prand.length / 5);
           var mult = parseInt(prand.charAt(sPos) + prand.charAt(sPos*2) + prand.charAt(sPos*3) + prand.charAt(sPos*4) + prand.charAt(sPos*5));
           var incr = Math.ceil(pwd.length / 2);
           var modu = Math.pow(2, 31) - 1;
           if(mult < 2) {
             console.log("Algorithm cannot find a suitable hash. Please choose a different password. \nPossible considerations are to choose a more complex or longer password.");
             return null;
           }
           var salt = Math.round(Math.random() * 1000000000) % 100000000;
           prand += salt;
           while(prand. length > 10) {
             prand = (parseInt(prand.substring(0, 10)) + parseInt(prand.substring(10, prand.length))).toString();
           }
           prand = (mult * prand + incr) % modu;
           var enc_chr = "";
           var enc_str = "";
           for(var i=0; i<str.length; i++) {
             enc_chr = parseInt(str.charCodeAt(i) ^ Math.floor((prand / modu) * 255));
             if(enc_chr < 16) {
               enc_str += "0" + enc_chr.toString(16);
             } else enc_str += enc_chr.toString(16);
             prand = (mult * prand + incr) % modu;
           }
           salt = salt.toString(16);
           while(salt.length < 8)salt = "0" + salt;
           enc_str += salt;
           return enc_str;
         }

         var decrypt = function (str, pwd) {
           if(str == null || str.length < 8) {
             console.log("A salt value could not be extracted from the encrypted message because it's length is too short. The message cannot be decrypted.");
             return null;
           }
           if(pwd == null || pwd.length <= 0) {
             console.log("Please enter a password with which to decrypt the message.");
             return null;
           }
           var prand = "";
           for(var i = 0; i < pwd.length; i++) {
             prand += pwd.charCodeAt(i).toString();
           }
           var sPos = Math.floor(prand.length / 5);
           var mult = parseInt(prand.charAt(sPos) + prand.charAt(sPos*2) + prand.charAt(sPos*3) + prand.charAt(sPos*4) + prand.charAt(sPos*5));
           var incr = Math.round(pwd. length / 2);
           var modu = Math.pow(2, 31) - 1;
           var salt = parseInt(str.substring(str.length - 8, str.length), 16);
           str = str.substring(0, str.length - 8);
           prand += salt;
           while(prand. length > 10) {
             prand = (parseInt(prand.substring(0, 10)) + parseInt(prand.substring(10, prand.length))).toString();
           }
           prand = (mult * prand + incr) % modu;
           var enc_chr = "";
           var enc_str = "";
           for(var i=0; i<str.length; i+=2) {
             enc_chr = parseInt(parseInt(str.substring(i, i+2), 16) ^ Math.floor((prand / modu) * 255));
             enc_str += String.fromCharCode(enc_chr);
             prand = (mult * prand + incr) % modu;
           }
           return enc_str;
         }

         export {encrypt, decrypt}

From the above deterministic wallet generation Ethereum address generation process, we can see that the process of generating the address of the hierarchical wallet is to generate a mnemonic -> generate a random number seed -> export the master private key -> export according to the hierarchical specification Sub-private key->Export address. Just looking at the two cases above, we may not be able to see the advantages and disadvantages of uncertainty wallets and certainty wallets. But if we have many accounts in our wallet, it will be difficult to manage using the non-deterministic wallet private key. If we use a hierarchical deterministic wallet, we only need to remember the mnemonic phrase. The following question will analyze why deterministic wallets only need to remember mnemonic words, while hierarchical wallets need to remember and manage many private keys.

## 2. SLIPS project

The project needed a way to document its technical decisions and features, and the SatoshiLabs project providedProvides such a mechanism to manage Bitcoin and cryptocurrencies. SLIP is the implementation of the project, and the SLIP repository is an extension of the Bitcoin Improvement Proposal (BIP) process and contains documents that are not suitable for submission to the BIP repository.

Each SLIP should provide a concise technical specification of the feature and the rationale for the feature.
```

  | label | title | type | status |
  |------------------|---------------------------------- -------------|--------------|-------------------- |
  | SLIP-0000 | SLIP template | Informatization | Recognized |
  | SLIP-0010 | Deriving a Universal Private Key from a Master Private Key | Standard | Draft |
  | SLIP-0011 | Symmetric encryption of key-value pairs using a deterministic hierarchy | Standard | Draft |
  | SLIP-0012 | Public-key encryption using deterministic levels | Standard | Draft |
  | SLIP-0013 | Using Certainty Level Certification | Standard | Draft |
  | SLIP-0014 | Stress Test Deterministic Wallet | Informatization | Draft |
  | SLIP-0015 | The format of Bitcoin metadata and its encryption in HD wallets | Standard | Draft |
  | SLIP-0016 | Password storage format and encryption | Standard | Draft |
  | SLIP-0017 | Deterministic Hierarchy Elliptic Curve Diffie-Hellman | Standard | Draft |
  | SLIP-0018 | Reserved (CoSi) | Standard | Draft |
  | SLIP-0032 | Extended serialization format for BIP-32 wallets | Standard | Draft |
  | SLIP-0039 | Shamir's Mnemonic Code Secret Sharing | Standard | Draft |
  | SLIP-0044 | BIP-0044 Categories of registered coins | Standard | Draft |
  | SLIP-0048 | Deterministic key hierarchy for graphene-based networks | Standard | Draft |
  | SLIP-0132 | Registered HD version byte of BIP-0032 | Standard | Draft |
  | SLIP-0173 | Registered human-readable components of BIP-0173 | Standard | Draft |
```

### 3. Introduction to each component

Forcing the use of public derived keys is unsafe for any software that supports private key export and can lead to (result in) loss of funds. The lack of hooks for version control is poor for future scalability (although this can be handled at a higher level). This is generally not recommended, except for hardware wallets.

Among the components introduced in Section 2, the most widely used one is SLIP-0044. Strict layered deterministic wallets will use the provisions of the SLIP-0044 layered protocol. Of course, in actual wallet development, because ERC20 tokens are relatively confusing, most coins are air coins, and many project parties do not know the existence of this protocol. Therefore, in actual wallet development, the address of ERC20 tokens is generated. For the protocol, we will all choose to use the sequence number specified by Ethereum’s BIP44. Let's first introduce this component.

To talk about BIP44, we must first start with BIP32. BIP32 is the core proposal of HD wallet. It uses seeds to generate a master private key and then derives a large number of sub-private keys and addresses. However, the seed is a long string of random numbers, which is not conducive to Record, so we use an algorithm to convert the seed into a string of mnemonic words to facilitate record keeping. This is BIP39, which expands the HD wallet seed generation algorithm. BIP43 adds the extension `m/purpose'/*` of the sub-index identifier purpose to the BIP32 tree structure. BIP44 adds multiple currencies on the basis of BIP43 and BIP32, and derives multiple addresses through HD wallets. BIP44 proposes a five-layer path recommendation, as follows:
`m/purpse’/coin_type’/account’/change/address_index`
The rules of BIP44 make the HD wallet very powerful. Users only need to save a seed to control the wallets of all currencies and accounts.

#### 1.BIP44

BIP44 defines the logical hierarchy of deterministic wallets based on the algorithms described in the Purpose Scheme described in BIP-0032 (from now on BIP32) and BIP-0043 (from now on BIP43). BIP44 is a specific application of BIP43. The hierarchy of BIP44 is very comprehensive. It allows handling of multiple coins, multiple accounts, external and internal chains per account and millions of addresses per chain.

##### 1.1. Path level

There are 5 path levels defined in BIP32:

     m/purpose'/coin_type'/account'/change/address_index

The apostrophe in the path indicates the use of BIP32 hardened derivatives. Each level of the above path has its own special meaning, which we will explain in detail below.

##### 1.2.purpose

The purpose is to set the constant to 44' (or 0x8000002C) following the BIP43 recommendation. It represents the subtree of this node that is used according to this specification.

Use fortified derivation at this level.

##### 1.3.coin_type

One masternode (seed) can be used for an unlimited number of independent cryptocoins, such as Bitcoin, Litecoin or Namecoin. However, there are some drawbacks to sharing the same space for various cryptocurrencies.

This level creates a separate subtree for each cryptocurrency, avoiding reuse of addresses in the crypto chain and improving privacy concerns.

The coin type is a constant, set for each cryptocurrency. Cryptocoin developers may ask for unused numbers to be registered for their projects.

The list of assigned coin types is in the "Registered Coin Types" chapter below.

Use fortified derivation at this level.

##### 1.4. Account

This level splits the key space into independent user identities so the wallet never mixes coins among different accounts.

Users can use these accounts to organize funds in the same way as bank accounts; for donation purposes (all addresses are considered public addresses), for saving purposes, common expenses, etc.

Accounts are numbered in sequential order starting from index 0. This number is used as a sub-index in BIP32 derivation.

Use fortified derivation at this level.

The software should prevent the creation of an account if the previous account has no transaction history (meaning no address has been used before).

After importing a torrent from an external source, the software needs to discover all accounts used. This algorithm is described in the "Account Discovery" chapter.

##### 1.5.Change

The constant 0 is used for the external link and the constant 1 is used for the internal link (also called the change address). External chains are used for addresses that are visible outside the wallet (for example, used to receive payments). The internal chain is used for addresses that are not meant to be visible outside the wallet and for returning transaction changes.

Use public derivation at this level.

##### 1.6.Index

Addresses are numbered in sequential order starting from index 0. This number is used as a sub-index in BIP32 derivation.

Use public derivation at this level.

#### 2. Account discovery mechanism

When importing a master seed from an external source, the software should start discovering accounts in the following way:

* The node from which the first account is derived (index = 0)
* Derive the external link node of this account
* Scan the addresses of external links; follow the gap restrictions described below
* If no transactions are found on the external chain, stop discovery
* If there are certain transactions, increase the account index and go to step 1

This algorithm is successful because the program should prevent the creation of new accounts if the previous account has no trading history, as described in the "Accounts" chapter above.

Note that the algorithm works on transaction history, not account balances, so you can have an account with a total of 0 coins and the algorithm will still keep discovering.

##### 2.1. Address gap restriction

The address gap limit is currently set to 20. If the program hits 20 unused addresses in a row, it expects no used addresses after this and stops searching the address chain. We only scan external links because internal links only receive currency from relevant external links.

Wallet programs should issue a warning when a user attempts to exceed the gap limit of an external chain by generating new addresses.

#### 3. Registered currency

The registered currency is "Digital Currency Type"; this describes the default registered coin type used by BIP44 Level 2. All these constants are used for hardening derivation.

| Index | Hex | Coins |
|------|--------------|----------------|
| 0 | 0x80000000 | Bitcoin |
| 1 | 0x80000001 | Bitcoin Testnet |

This BIP is not a central directory of registered currency types, please visit SatoshiLabs for a complete list:

To register a new coin type, an existing wallet that implements the standard is required and a pull request to the above files should be created.

#### example

.：
     ![.：
](https://github.com/guoshijiang/blockchain-wallet/blob/master/img/coinExample.png)
