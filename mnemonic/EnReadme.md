# Chapter 3: Mnemonic Phrases

Most wallets on the blockchain market now use mnemonic words to back up their wallets. Of course, there are also many wallets that use private keys to back up their wallets. Whether it is backing up a wallet through a mnemonic phrase or backing up a wallet using a private key, the principles are basically the same. Generally, wallets generate random number seeds through mnemonic words, then generate root private keys through random number seeds, and then generate private keys for each currency account through the BIP layered protocol. Therefore, the mnemonic phrase is the starting point of a wallet and an important technical component of a wallet. In this chapter we will explain the knowledge related to mnemonics in detail.

## 1. Principle of mnemonic words

Mnemonic words are not a new concept. The concept of mnemonic words has appeared in many industries. Pronounced "ne-manik" in its purest form, a mnemonic is a letter, word, or pattern of associations that allows you to easily remember information, and has been used by humans for thousands of years. In other words, it can be a very useful tool in helping us remember important information that we need to remember.

In Bitcoin, mnemonics work on the same basic principle of using a set of words to generate a unique wallet phrase, giving you a human-readable word format to back up your wallet for recovery. The mnemonic code used to generate these phrases was introduced into Bitcoin in 2013 through the Bitcoin Improvement Proposal, or BIP. BIP describes the implementation of a mnemonic code or mnemonic sentence, a set of easy-to-remember words used to generate a deterministic wallet. It consists of two parts: generating a mnemonic and converting it into a binary seed. This seed can later be used to generate a deterministic wallet using BIP-0032 or similar methods.

Generally speaking, private keys have 256 bits, represented by a hexadecimal string of 64 alphanumeric characters. When backing up the private key, directly transcribing 64 characters is obviously prone to errors, and the legibility is also poor, which will obviously reduce the user experience. The emergence of mnemonic words just solves this embarrassing situation. Mnemonic A word is another representation of a plaintext private key. The purpose of mnemonic words is to help users remember complex private keys (64-bit hash values). Mnemonic words are generally composed of 12, 15, 18, and 21 words. These words are taken from a fixed vocabulary, and their generation order is also based on a certain algorithm, so users do not need to worry that just inputting 12 words will generate a address. Although both mnemonic words and Keystore can be used as another form of expression of private keys, unlike Keystore, mnemonic words are unencrypted private keys and have no security at all. Anyone who gets your help can Memorizing words can take away your assets without any effort.

Currently there are two types of wallets: non-deterministic wallets and deterministic wallets. Non-deterministic wallets randomly generate multiple private keys, and the wallet manages these private keys. This kind of wallet private key is not easy to manage. But if the person who designs the wallet is too skilled, it can be designed very well. Deterministic wallets use seeds to generate private keys for different accounts. We only need to remember the mnemonic phrase, use the mnemonic phrase to generate the seed, and then generate the wallet-related address and private key.

Let’s take a look at the process of seed generation

.：
     ![.：
](https://github.com/guoshijiang/blockchain-wallet/blob/master/img/mnemonic.png)


### 1. From entropy to mnemonics


Randomly generate a 128 to 258-bit number, called entropy; entropy is hashed through SHA256 to get a value, take the first few digits (entropy length/32), recorded as y; entropy and y form a new sequence, and the new sequence With 11 bits as a part, a dictionary of 2048 words has been predefined for correspondence; the generated sequential word group is the mnemonic word

As shown below

.：
     ![.：
](https://github.com/guoshijiang/blockchain-wallet/blob/master/img/mnemonicone.png)

### 2. Generate seeds from mnemonic words

The mnemonic represents an entropy of length 128 to 256 bits. Entropy is used to derive a longer (512-bit) seed by using the key stretch function PBKDF2.
The basic principle of PBKDF2 is to use a pseudo-random function (such as the HMAC function) to take the plaintext and a salt value as input parameters, then repeat the operation, and finally generate a key. If the number of repetitions is large enough, the cost of cracking becomes very high. The addition of salt value will also increase the difficulty of "rainbow table" attack.
In the Bitcoin wallet, the first parameter of the PBKDF2 function is the mnemonic phrase, and the second parameter salt is composed of the string constant "mnemonic phrase" concatenated with the optional user-provided password string. Using the HMAC-SHA512 algorithm, the mnemonic and salt parameters are stretched using 2048 hashes, producing a 512-bit value as its final output. This 512-bit value is the seed. As shown in the picture

.：
     ![.：
](https://github.com/guoshijiang/blockchain-wallet/blob/master/img/mnemonictwo.png)

### 3. From seed to mother key

The 512 bits are divided equally into two parts, the 256 bits on the left are the parent private key, and the 256 bits on the right are the chain code. Parent private key, chain code and index number, CKD (child key derivation) function to derive the child key from the parent key. As shown in the picture

.：
     ![.：
](https://github.com/guoshijiang/blockchain-wallet/blob/master/img/mnemonicthree.png)


### 4. From parent key to child key

The parent key, chain code, and index are combined and hashed with the HMAC-SHA512 function to produce a 512-bit hash. The resulting hash can be split into two parts. The 256-bit output of the right half of the hash can be used as chain code for the sub-chain. The left half of the 256-bit hash and index code are loaded onto the parent private key to derive the child private key. In the figure, we see this illustrated - the index set is set to 0 to produce the 0th child key of the parent key (the first one by index).

.：
     ![.：
](https://github.com/guoshijiang/blockchain-wallet/blob/master/img/mnemonicfour.png)

### 5. Extended key

The combination of the parent key and the chain code is called an extended key. Having the extended private key can derive the child private key, and the extended public key can derive the child public key. With the extended public key, you can derive the child public key without the parent private key on the server, which is safer and more convenient. But there is another problem, that is, the extended public key contains a chain code. If the sub-private key is known or leaked, the chain code can be used to derive all other sub-private keys. Simply leaking the private key along with a parent chaincode can expose all child keys. To make matters worse, the child private key and the parent chain code can be used to infer the parent private key.
To do this, HD Wallet uses an alternative derivation function called hardened derivation. This "breaks" the relationship between the parent public key and the child chain code. This hardened derivation function uses the parent private key to derive the child chaincode, rather than the parent public key. This creates a "firewall" in the parent/child sequence - there is a chain code but it cannot be used to deduce child chain codes or sister private keys. The enhanced derivation function looks almost the same as a regular derived sub-private key, except that the parent private key is used as input into the hash function instead of the parent public key. As shown in the picture

.：
     ![.：
](https://github.com/guoshijiang/blockchain-wallet/blob/master/img/mnemonicfive.png)


## 2. Generate mnemonic words

### 1. Generate 12 mnemonics

var bip39 = require('bip39')
var mnemonic = bip39.generateMnemonic()
console.log(mnemonic);

var bip39 = require('bip39')
var mnemonic = bip39.generateMnemonic(128)
console.log(mnemonic);

### 2. Generate 15 mnemonics

var bip39 = require('bip39')
var mnemonic = bip39.generateMnemonic(160)
console.log(mnemonic);

### 3. Generate 18 mnemonics

var bip39 = require('bip39')
var mnemonic = bip39.generateMnemonic(192)
console.log(mnemonic);

### 4. Generate 21 mnemonics

var bip39 = require('bip39')
var mnemonic = bip39.generateMnemonic(224)
console.log(mnemonic);

### 5. Generate 24 mnemonics

var bip39 = require('bip39')
var mnemonic = bip39.generateMnemonic(256)
console.log(mnemonic);

### 6. Generate Chinese mnemonics

var mnemonicS = bip39.generateMnemonic(128, null, bip39.wordlists.chinese_simplified);
console.log(mnemonicS);
The above limits the number of mnemonics according to the size of the passed value.

Currently, the mnemonic database can generate "Simplified Chinese", "Traditional Chinese", "English", "French", "Italian", "Japanese", "Korean" and Spanish.

## 3. Mnemonic word encoding and decoding

### 1. Encoding mnemonic phrase

var bip39 = require('bip39')
var mnemonic = bip39.generateMnemonic()
var encrytMnemonic = bip39.mnemonicToEntropy(mnemonic)
console.log(encrytMnemonic);

### 2. Decode mnemonic words

var bip39 = require('bip39')
var mnemonic = bip39.generateMnemonic()
console.log(mnemonic);
var encrytMnemonic = bip39.mnemonicToEntropy(mnemonic)
var word = bip39.entropyToMnemonic(encrytMnemonic)
console.log(word);

## 4. Generate random number seeds

var mnemonicE = bip39.generateMnemonic()
console.log(mnemonicE);
var Seed= bip39.mnemonicToSeed(mnemonicE)
var seedHex= bip39.mnemonicToSeedHex(mnemonicE)
console.log(Seed);
console.log(seedHex);

## 5. Verify mnemonic phrase
	
var mnemonic = bip39.generateMnemonic()
console.log(mnemonic);
var bool = bip39.validateMnemonic(mnemonic)

## 6. Mnemonic phrase open source library

     https://github.com/bitcoinjs/bip39
