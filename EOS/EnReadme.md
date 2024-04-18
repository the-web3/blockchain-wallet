# Chapter 7: EOS wallet development

## 1. What is EOS

EOSIO is an open-source blockchain project designed to enable vertical and horizontal scaling of decentralized applications (the "EOSIO Software") and can be used to launch private and public blockchain networks. This is achieved through an operating system-like construct on which applications can be built. The software provides accounts, authentication, databases, asynchronous communications, and application scheduling across multiple CPU cores and/or clusters. The resulting technology is a blockchain architecture that has the potential to scale to millions of transactions per second, mind you, just the potential to scale to millions of things. Eliminates user fees and allows for quick and easy deployment of decentralized applications.

Before installing the wallet node, we should first understand the architecture of EOS. The following is a module architecture of EOS.


.：
     ![.：
](https://github.com/guoshijiang/blockchain-wallet/blob/master/img/EOS1.png)

* nodeos (node ​​+ eos = nodeos) - The core EOSIO node daemon that can be configured using plugins to run node. Example usage is block production, private API endpoints and local development.

* cleos (cli + eos = cleos) - Command line interface to interact with the blockchain and manage wallets

* keosd (key + eos = keosd) - Component that securely stores EOSIO keys in a wallet.

* eosio-cpp - part of eosio.cdt, which compiles C++ code to WASM and can generate ABI


## 2. Install EOS wallet node

Next we use binary files to build the project. If we build the project from the source code, this operation method is more complicated and a waste of time. This method is not recommended here.

### 1. Install EOS on various platforms

#### 1.1. Install EOS on mac platform

     brew tap eosio/eosio
     brew install eosio

#### 1.2.Ubuntu18.04

     wget https://github.com/eosio/eos/releases/download/v1.5.0/eosio_1.5.0-1-ubuntu-18.04_amd64.deb
     sudo apt install ./eosio_1.5.0-1-ubuntu-18.04_amd64.deb

#### 1.3.Ubuntu16.04

     wget https://github.com/eosio/eos/releases/download/v1.5.0/eosio_1.5.0-1-ubuntu-16.04_amd64.deb
     sudo apt install ./eosio_1.5.0-1-ubuntu-16.04_amd64.deb

#### 1.4.CentOS

     wget https://github.com/eosio/eos/releases/download/v1.5.0/eosio-1.5.0-1.el7.x86_64.rpm
     sudo yum install ./eosio-1.5.0-1.el7.x86_64.rpm

#### 1.5.Fedora

     wget https://github.com/eosio/eos/releases/download/v1.5.0/eosio-1.5.0-1.fc27.x86_64.rpm
     sudo yum install ./eosio-1.5.0-1.fc27.x86_64.rpm


### 2. Start the node and configure the node

#### 2.1. Start Keosd

     keosd&

After executing the above command, you should see output similar to the following. Press Enter to exit.

     info 2018-11-26T06:54:24.789 thread-0 wallet_plugin.cpp:42 plugin_initialize ] initializing wallet plugin
     info 2018-11-26T06:54:24.795 thread-0 http_plugin.cpp:554 add_handler ] add api url: /v1/keosd/stop
     info 2018-11-26T06:54:24.796 thread-0 wallet_api_plugin.cpp:73 plugin_startup ] starting wallet_api_plugin
     info 2018-11-26T06:54:24.796 thread-0 http_plugin.cpp:554 add_handler ] add api url: /v1/wallet/create
     info 2018-11-26T06:54:24.796 thread-0 http_plugin.cpp:554 add_handler ] add api url: /v1/wallet/create_key
     info 2018-11-26T06:54:24.796 thread-0 http_plugin.cpp:554 add_handler ] add api url: /v1/wallet/get_public_keys


### 2.2. Start nodeos

     nodeos -e -p eosio \
     --plugin eosio::producer_plugin \
     --plugin eosio::chain_api_plugin \
     --plugin eosio::http_plugin \
     --plugin eosio::history_plugin \
     --plugin eosio::history_api_plugin \
     --access-control-allow-origin='*' \
     --contracts-console \
     --http-validate-host=false \
     --verbose-http-errors \
     --filter-on='*' >> nodeos.log 2>&1 &

These settings enable the following functionality:

* Use the working directory in the eosio directory under the development directory for blockchain data and configuration. Here we use eosio/data and eosio/config respectively

* Run Nodeos. This command loads all the base plugins, sets the server address, enables CORS and adds some contract debugging and logging.

* Enable CORS without restrictions (*)

In the configuration above, CORS is for development purposes only*, you should never enable CORS on a publicly accessible node!

### 3. Check the node and wallet status

#### 3.1. Check the status of nodes

Run the following command

     tail -f nodeos.log

You can see these inputs below the terminal

     1929001ms thread-0 producer_plugin.cpp:585 block_production_loo ] Produced block 0000366974ce4e2a... #13929 @ 2018-05-23T16:32:09.000 signed by eosio [trxs: 0, lib: 13928, confirmed: 0]
     1929502ms thread-0 producer_plugin.cpp:585 block_production_loo ] Produced block 0000366aea085023... #13930 @ 2018-05-23T16:32:09.500 signed by eosio [trxs: 0, lib: 13929, confirmed: 0]
     1930002ms thread-0 producer_plugin.cpp:585 block_production_loo ] Produced block 0000366b7f074fdd... #13931 @ 2018-05-23T16:32:10.000 signed by eosio [trxs: 0, lib: 13930, confirmed: 0]
     1930501ms thread-0 producer_plugin.cpp:585 block_production_loo ] Produced block 0000366cd8222adb... #13932 @ 2018-05-23T16:32:10.500 signed by eosio [trxs: 0, lib: 13931, confirmed: 0]
     1931002ms thread-0 producer_plugin.cpp:585 block_production_loo ] Produced block 0000366d5c1ec38d... #13933 @ 2018-05-23T16:32:11.000 signed by eosio [trxs: 0, lib: 13932, confirmed: 0]
     1931501ms thread-0 producer_plugin.cpp:585 block_production_loo ] Produced block 0000366e45c1f235... #13934 @ 2018-05-23T16:32:11.500 signed by eosio [trxs: 0, lib: 13933, confirmed: 0]
     1932001ms thread-0 producer_plugin.cpp:585 block_production_loo ] Produced block 0000366f98adb324... #13935 @ 2018-05-23T16:32:12.000 signed by eosio [trxs: 0, lib: 13934, confirmed: 0]
     1932501ms thread-0 producer_plugin.cpp:585 block_production_loo ] Produced block 00003670a0f01daa... #13936 @ 2018-05-23T16:32:12.500 signed by eosio [trxs: 0, lib: 13935, confirmed: 0]
     1933001ms thread-0 producer_plugin.cpp:585 block_production_loo ] Produced block 00003671e8b36e1e... #13937 @ 2018-05-23T16:32:13.000 signed by eosio [trxs: 0, lib: 13936, confirmed: 0]
     1933501ms thread-0 producer_plugin.cpp:585 block_production_loo ] Produced block 0000367257fe1623... #13938 @ 2018-05-23T16:32:13.500 signed by eosio [trxs: 0, lib: 13937, confirmed: 0]

Press CNTL + C to exit the log.

#### 3.2. Check wallet status

Open a shell and run the cleos command to list available wallets

     cleos wallet list

You should see output like the following

     Wallets:
     []

#### 3.3. Check Nodeos end nodes

This will check if the RPC API is working properly, select one.

Check the get_info endpoint provided by chain_api_plugin in your browser:

     http://localhost:8888/v1/chain/get_info
    
Check the same thing but in the host's console

     curl http://localhost:8888/v1/chain/get_info


## 3. Create a development wallet using the command line

A wallet is a repository of public-private key pairs. Private keys are required to sign operations performed on the blockchain. The wallet can be accessed using cleos.

### 1. Create wallet

The first step is to create a wallet. Use cleos wallet create to create a new "default" wallet, using option --to-console for simplicity. If using cleo in production, it is best to use --to-file so that your wallet password is not in your bash history. For development purposes, as these are development and not production keys - the console does not pose a security threat.

     cleos wallet create --to-console
    
cleos will return the password, which needs to be stored elsewhere for use when needed later.

     Creating wallet: default
     Save password to use in the future to unlock this wallet.
     Without password imported keys will not be retrievable.
     "PW5Kewn9L76X8Fpd.............t42S9XCw2"

### 2. Open wallet

By default, the wallet is closed when starting the keosd service. To enable the wallet, please run the following command.

     cleos wallet open

To get the list of wallets, please execute the following command

     cleos wallet list
    
Get the data structure returned by the wallet list

     Wallets:
     [
       "default"
     ]

### 3. Unlock wallet

The keosd wallet is open but still locked. When you created the wallet, you were given the password, and now you need the password. , execute the following command to unlock the wallet

     cleos wallet unlock

The above command will prompt you to enter a password. You can just copy and paste the password generated above.

### 4. Check whether the wallet is unlocked

The wallet returned after executing the `cleos wallet list` command is followed by an asterisk (`*`), which means that the wallet is currently unlocked, as follows:

     Wallets:
     [
       "default *"
     ]

### 5. Import the key to the wallet

Generate a private key, cleos has a function to generate a private key, just run the following command.

     cleos wallet create_key

Executing the above command will return the following results

     Created new private key with a public key of: "EOS8PEJ5FM42xLpHK...X6PymQu97KrGDJQY5Y"


### 6.Introduce the development key

Every new EOSIO chain has a default "system" user named "eosio". This account is used to set up the chain by loading the system contract, which specifies the governance and consensus of the EOSIO chain. Every new EOSIO chain comes with a development key, which is the same. Load this key to sign transactions on behalf of the system user (eosio).

     cleos wallet import

You will be prompted for your private key, enter the eosio development key provided below

     5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3

*Note: The key above is the key I generated. What you should fill in here is the key you generated yourself. Never use development keys with production accounts! Doing so will definitely result in you losing access to your account, this private key is public.

### 7. Create a test account

#### 7.1. What is an EOS account?

An account is a set of authorizations, stored in the blockchain, that identify the sender/recipient. It has a flexible authorization structure that allows it to be owned by an individual or a group of individuals, depending on how permissions are configured. An account is required to send or receive valid transactions to the blockchain.

#### 7.2. Create a test account

Use bob and alice as accounts, and use cleos create account to create two accounts.

     cleos create account eosio bob EOS6ia9oVJpCvUzMrgAyMdbhrYu5YBfbuohvfUJxwvqh8e4JaBtJH
     cleos create account eosio alice EOS6ia9oVJpCvUzMrgAyMdbhrYu5YBfbuohvfUJxwvqh8e4JaBtJH

You should then see a confirmation message similar to the following for each command that confirms the message has been broadcast.

     executed transaction: 40c605006de... 200 bytes 153 us
     # eosio <= eosio::newaccount {"creator":"eosio","name":"alice","owner":{"threshold":1,"keys":[{"key":"EOS5rti4LTL53xptjgQBXv9HxyU.. .
     warning: transaction executed locally, but may not be confirmed by the network yet ]

#### 7.3.Public key

Note that in the cleos command, the public key is associated with the account alice. Every EOSIO account is associated with a public key.

Note that the account name is a unique identifier of ownership. You can change the public key, but it does not change the ownership of your EOSIO account.

Use cleos get account to check which public key is associated with alice

     cleos get account alice

You will see this return below

     permissions:
          owner 1: 1 EOS6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV
             active 1: 1 EOS6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV
     memory:
          quota: unlimited used: 2.66 KiB

     net bandwidth:
          used: unlimited
          available: unlimited
          limit: unlimited

     cpu bandwidth:
          used: unlimited
          available: unlimited
          limit: unlimited


*Note that alice actually has both the owner and active public keys. EOSIO has a unique authorization structure that adds security to your account. You can minimize risk to your account by keeping owner keys cool when using keys associated with your active permissions. This way, if your valid key is compromised, you can use the owner key to regain control of your account.


## 4. The process of developing transfer using contract method

### 1. Introduction to EOS contract development

Create a new directory named "hello" in the contract directory created earlier

     cd /root/eosio/contracts
     mkdir hello
     cd hello

Create a `hello.cpp` C++ code file and open it with your favorite compiler

The following contains two header files, eosio.hpp and print.hpp files. The eosio.hpp file contains the libraries needed to write contracts. Here are descriptions of these required libraries:

* action.hpp: Defines type-safe C++ wrappers for query and send operations.
* contract.hpp: The base class of EOSIO contract.
* print.hpp: C++ wrapper for logging/printing text messages.
* multi_index.hpp: EOSIO Multi-Index API provides a C++ interface for the EOSIO database.
* dispatcher.hpp: Defines C++ functions to dispatch operations to the correct operation handler in the contract.

Using eosio namespaces can reduce clutter in your code. For example, by setting the namespace eosio;, you can write eosio::print("foo") print("foo")

eosiolib/eosio.hpp loads the EOSIO C and C++ API into contract scope, which is your new version.

Create a standard C++11 class. The contract class needs to extend eosio::contract.

     #include <eosiolib/eosio.hpp>

     using namespace eosio;

     class hello : public contract {};

Empty contracts don't help much. Add public access specifiers and using statements. The using statement will allow us to write cleaner code.

     #include <eosiolib/eosio.hpp>

     using namespace eosio;

     class [[eosio::contract("hello")]] hello : public contract {
       public:
           using contract::contract;
     };

This contract needs to do something. In the spirit of hello world, write an action that accepts a "name" parameter and then outputs that parameter.

Actions implement the behavior of a contract. Communication between contract operations comes in two forms, inline operations (will be executed in the same transaction as the calling operation) and deferred operations (will be executed later).

     #include <eosiolib/eosio.hpp>

     using namespace eosio;

     class [[eosio::contract("hello")]] hello : public contract {
       public:
           using contract::contract;

           [[eosio::action]]
           void hi( name user ) {
              print( "Hello, ", name{user});
           }
     };

The above operation accepts a parameter named user, which is a name type. EOSIO comes with many typedefs, one of the most common typedefs you will encounter is name. Using the previously included eosio::print library, concatenate strings and print user parameters. Make user parameters printable using a support initialization with name {user}.

Therefore, the abi generator in eosio.cdt will not know about the hi() action without attributes. Add C++11 style attributes above actions so the abi generator can produce more reliable output.

     #include <eosiolib/eosio.hpp>using namespace eosio;

     class [[eosio::contract("hello")]] hello : public contract {
       public:
           using contract::contract;

           [[eosio::action]]
           void hi( name user ) {
              print( "Hello, ", user);
           }
     };

     EOSIO_DISPATCH( hello, (hi))

Finally, add the EOSIO_DISPATCH macro to handle scheduling hello contract operations.

     EOSIO_DISPATCH( hello, (hi))

Everything comes together, here is the completed hello world contract

     #include <eosiolib/eosio.hpp>

     using namespace eosio;

     class [[eosio::contract("hello")]] hello : public contract {
       public:
           using contract::contract;

           [[eosio::action]]
           void hi( name user ) {
              print( "Hello, ", user);
           }
     };

     EOSIO_DISPATCH( hello, (hi))

You can compile the code into a web assembly (.wasm) like this:

     eosio-cpp -o hello.wasm hello.cpp --abigen

When a contract is deployed, it is deployed to an account, which becomes the contract's interface. As mentioned before, these tutorials use the same public key for all accounts to keep it simple.

     cleos wallet keys

Create a contract account using cleos create account using the command provided below.

     cleos create account eosio hello EOS6ia9oVJpCvUzMrgAyMdbhrYu5YBfbuohvfUJxwvqh8e4JaBtJH -p eosio@active

Use the cleos set contract to broadcast the compiled wasm to the blockchain.

In the previous steps you should create a `contracts` directory and get the absolute path and then save it to a cookie. Replace "CONTRACTS_DIR" in the following command with the absolute path to the `contracts` directory.

     cleos set contract hello /root/eosio/contracts/hello -p hello@active

Perfect, now the contract has been set, execute the following command

     cleos push action hello hi '["bob"]' -p bob@active

The execution results are as follows:

     executed transaction: 4c10c1426c16b1656e802f3302677594731b380b18a44851d38e8b5275072857 244 bytes 1000 cycles
     # hello.code <= hello.code::hi {"user":"bob"}
     >> Hello, bob

As mentioned above, the contract will allow any account to greet any user

     cleos push action hello hi '["bob"]' -p alice@active

Results of the:

     executed transaction: 28d92256c8ffd8b0255be324e4596b7c745f50f85722d0c4400471bc184b9a16 244 bytes 1000 cycles
     # hello.code <= hello.code::hi {"user":"bob"}
     >> Hello, bob

As expected, the console output is "Hello, bob"

In this case "alice" is the person who authorized it, user is just a parameter. Modify the contract so that the authorized user in this case "alice" must be the same user as the contract responds to "hi". Use require_auth method. This method takes a name as a parameter and will check that the user performing the action matches the provided parameters and has appropriate authorization.

     void hi( name user ) {
        require_auth( user );
        print( "Hello, ", name{user} );
     }

Recompile contract code

     eosio-cpp -o hello.wasm hello.cpp --abigen

then update it

     cleos set contract hello /root/eosio/contracts/hello -p hello@active

Tried the operation again, but this time the authorizations did not match.

     cleos push action hello hi '["bob"]' -p alice@active

As expected, require_auth paused the transaction and threw an error.

     Error 3090004: Missing required authority
     Ensure that you have the related authority inside your transaction!;
     If you are currently using 'cleos push action' command, try to add the [relevant](**http://google.com**) authority using -p option.

Now, with our change, the contract will verify that the provided name user is the same as the authorized user. Try again, but this time, have permissions for the "alice" account.

     cleos push action hello hi '["alice"]' -p alice@active

Results of the

     executed transaction: 235bd766c2097f4a698cfb948eb2e709532df8d18458b92c9c6aae74ed8e4518 244 bytes 1000 cycles
     # hello <= hello::hi {"user":"alice"}
     >> Hello, alice

### 2. Deployment, release and token transfer

EOS has officially open sourced a contract code for transfers. We only need to understand the contract code, and then we can modify the contract code according to our own needs based on this contract code.

#### 2.1. Pull contract code

Enter the contract directory that has been created previously

     cd /root/eosio/contracts

Pull source code

     git clone https://github.com/EOSIO/eosio.contracts --branch v1.4.0 --single-branch

This repository contains several contracts, but it is the important eosio.token contract right now. Go to this directory.

     cd eosio.contracts/eosio.token

#### 2.2. Create a user for the contract

Before we deploy the token contract, we must create an account to deploy it, and we will use that account's eosio development key. Of course, you should unlock the wallet before using it

     cleos create account eosio eosio.token EOS6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV

#### 2.3. Compile contract code

     eosio-cpp -I include -o eosio.token.wasm src/eosio.token.cpp --abigen

#### 2.4. Deploy token contract

     cleos set contract eosio.token /root/eosio/contracts/eosio.contracts/eosio.token --abi eosio.token.abi -p eosio.token@active

The following is the execution result

     Reading WASM from ...
     Publishing contract...
     executed transaction: 69c68b1bd5d61a0cc146b11e89e11f02527f24e4b240731c4003ad1dc0c87c2c 9696 bytes 6290 us
     # eosio <= eosio::setcode {"account":"eosio.token","vmtype":0,"vmversion":0,"code":"0061736d0100000001aa011c60037f7e7f0060047f...
     # eosio <= eosio::setabi {"account":"eosio.token","abi":"0e656f73696f3a3a6162692f312e30000605636c6f73650002056f776e6572046e61...
     warning: transaction executed locally, but may not be confirmed by the network yet ]

#### 2.5. Create a token

To create a new Token, call the create(...) operation. This operation accepts 1 parameter, which is a symbol_name type consisting of two data, the maximum supply float and the symbol_name with only uppercase alphabetic characters, such as "1.0000 SYM". The issuer will have the right to issue an issue or perform other actions such as freezing, recalling and owner whitelisting.

Here's a concise way of calling this method with positional parameters:

     cleos push action eosio.token create '[ "eosio", "1000000000.0000 SYS"]' -p eosio.token@active

Results of the

     executed transaction: 0e49a421f6e75f4c5e09dd738a02d3f51bd18a0cf31894f68d335cd70d9c0e12 120 bytes 1000 cycles
     # eosio.token <= eosio.token::create {"issuer":"eosio","maximum_supply":"1000000000.0000 SYS"}

Another approach uses named parameters:

     cleos push action eosio.token create '{"issuer":"eosio", "maximum_supply":"1000000000.0000 SYS"}' -p eosio.token@active

Results of the:

     executed transaction: 0e49a421f6e75f4c5e09dd738a02d3f51bd18a0cf31894f68d335cd70d9c0e12 120 bytes 1000 cycles
     # eosio.token <= eosio.token::create {"issuer":"eosio","maximum_supply":"1000000000.0000 SYS"}

This command creates a new token SYS with 4 decimal places of precision and a maximum supply of 1000000000.0000 SYS. To create this token, permission is required to the eosio.token contract. Therefore, -p eosio.token@active is passed to authorize the request.

#### 2.6. Release token

Issuers can issue new tokens to the previously created "alice" account.

     cleos push action eosio.token issue '[ "alice", "100.0000 SYS", "memo" ]' -p eosio@active

The execution results are as follows:

     executed transaction: 822a607a9196112831ecc2dc14ffb1722634f1749f3ac18b73ffacd41160b019 268 bytes 1000 cycles
     # eosio.token <= eosio.token::issue {"to":"user","quantity":"100.0000 SYS","memo":"memo"}
     >> issue
     # eosio.token <= eosio.token::transfer {"from":"eosio","to":"user","quantity":"100.0000 SYS","memo":"memo"}
     >> transfer
     # eosio <= eosio.token::transfer {"from":"eosio","to":"user","quantity":"100.0000 SYS","memo":"memo"}
     # user <= eosio.token::transfer {"from":"eosio","to":"user","quantity":"100.0000 SYS","memo":"memo"}


This time the output contains several different operations: a question and three transfers. Although the only action signed is the issue, the issue action performs an "inline transfer" which notifies the sender and recipient accounts. Output indicates all operation handlers called, the order in which they were called, and whether the operation produced any output.

Technically, the eosio.token contract may skip the inline transfer and choose to modify the balance directly. However, in this case, the eosio.token contract follows our Token convention, which requires that all account balances can be derived by the sum of transfer operations that reference them. It also requires that senders and recipients of funds be notified so that they can automatically process deposits and withdrawals.

To check transactions, try using the -d -j options, they mean "do not broadcast" and "return transactions as json", you may find these options useful during development.

     cleos push action eosio.token issue '["alice", "100.0000 SYS", "memo"]' -p eosio@active -d -j

#### 2.7. Transfer token

Now that the account has issued tokens, transfer some of them to account bob. It was previously stated that alice authorizes this operation using the parameter -p alice@active.

     cleos push action eosio.token transfer '[ "alice", "bob", "25.0000 SYS", "m" ]' -p alice@active

The execution results are as follows:

     executed transaction: 06d0a99652c11637230d08a207520bf38066b8817ef7cafaab2f0344aafd7018 268 bytes 1000 cycles
     # eosio.token <= eosio.token::transfer {"from":"alice","to":"bob","quantity":"25.0000 SYS","memo":"Here you go bob!"}
     >> transfer
     # user <= eosio.token::transfer {"from":"alice","to":"bob","quantity":"25.0000 SYS","memo":"Here you go bob!"}
     # tester <= eosio.token::transfer {"from":"alice","to":"bob","quantity":"25.0000 SYS","memo":"Here you go bob!"}

Now check if "bob" gets Token using cleos to get currency balance

     cleos get currency balance eosio.token bob SYS

The result is as follows:

     25.00 SYS
    
Check the balance of "alice" and note that the Token has been deducted from the account

     cleos get currency balance eosio.token alice SYS

The result is as follows:

     75.00 SYS

## 5. Wallet related APIs

### 1. Create a wallet interface based on the given name

* Request method: POST
* Interface name: `http://127.0.0.1:8888/v1/wallet/create`

#### 1.1.Curl method call

     curl --request POST \
       --url http://127.0.0.1:8888/v1/wallet/create \
       --header 'content-type: application/x-www-form-urlencoded; charset=UTF-8'
    
#### 1.2.NodeJs method calling

     var request = require("request");

     var options = { method: 'POST',
       url: 'http://127.0.0.1:8888/v1/wallet/create',
       headers: { 'content-type': 'application/x-www-form-urlencoded; charset=UTF-8' } };

     request(options, function (error, response, body) {
       if (error) throw new Error(error);

       console.log(body);
     });


#### 1.3.Ruby mode call

     require 'uri'
     require 'net/http'

     url = URI("http://127.0.0.1:8888/v1/wallet/create")

     http = Net::HTTP.new(url.host, url.port)

     request = Net::HTTP::Post.new(url)
     request["content-type"] = 'application/x-www-form-urlencoded; charset=UTF-8'

     response = http.request(request)
     puts response.read_body


#### 1.4.javaScript method call

     var data = null;

     var xhr = new XMLHttpRequest();

     xhr.addEventListener("readystatechange", function () {
       if (this.readyState === this.DONE) {
         console.log(this.responseText);
       }
     });

     xhr.open("POST", "http://127.0.0.1:8888/v1/wallet/create");
     xhr.setRequestHeader("content-type", "application/x-www-form-urlencoded; charset=UTF-8");

     xhr.send(data);

#### 1.5.Calling in python mode

     import requests

     url = "http://127.0.0.1:8888/v1/wallet/create"

     headers = {'content-type': 'application/x-www-form-urlencoded; charset=UTF-8'}

     response = requests.request("POST", url, headers=headers)

     print(response.text)

The return results are the same, and the return results are as follows

     "PW5KdHAJNyHGvVQfZMebawZrCxUrXoG8wrz55R5EHcfmvjSASuDay"


### 2. Open an existing wallet according to the given name

* Request method: POST
* Interface name: `http://127.0.0.1:8888/v1/wallet/open`

#### 2.1.Curl method call

        curl --request POST \
       --url http://127.0.0.1:8888/v1/wallet/open \
       --header 'content-type: application/x-www-form-urlencoded; charset=UTF-8'
  
#### 1.2.NodeJs method calling

     var request = require("request");

     var options = { method: 'POST',
       url: 'http://127.0.0.1:8888/v1/wallet/open',
       headers: { 'content-type': 'application/x-www-form-urlencoded; charset=UTF-8' } };

     request(options, function (error, response, body) {
       if (error) throw new Error(error);

       console.log(body);
     });

#### 1.3.Ruby mode call

     require 'uri'
     require 'net/http'

     url = URI("http://127.0.0.1:8888/v1/wallet/open")

     http = Net::HTTP.new(url.host, url.port)

     request = Net::HTTP::Post.new(url)
     request["cintent-type"] = 'application/x-www-form-urlencoded; charset=UTF-8'

     response = http.request(request)
     puts response.read_body


#### 1.4.javaScript method call

     var data = null;

     var xhr = new XMLHttpRequest();

     xhr.addEventListener("readystatechange", function () {
       if (this.readyState === this.DONE) {
         console.log(this.responseText);
       }
     });

     xhr.open("POST", "http://127.0.0.1:8888/v1/wallet/open");
     xhr.setRequestHeader("content-type", "application/x-www-form-urlencoded; charset=UTF-8");

     xhr.send(data);

#### 1.5.Calling in python mode

     import requests

     url = "http://127.0.0.1:8888/v1/wallet/open"

     headers = {'content-type': 'application/x-www-form-urlencoded; charset=UTF-8'}

     response = requests.request("POST", url, headers=headers)

     print(response.text)

The return results are the same, and the return results are as follows



### 3. Lock an existing wallet based on the given name

* Request method: POST
* Interface name: `http://127.0.0.1:8888/v1/wallet/lock`

#### 3.1.Curl method call

        curl --request POST \
       --url http://127.0.0.1:8888/v1/wallet/lock \
       --header 'content-type: application/x-www-form-urlencoded; charset=UTF-8'

#### 3.2.NodeJs method calling

      var request = require("request");

     var options = { method: 'POST',
       url: 'http://127.0.0.1:8888/v1/wallet/lock',
       headers: { 'content-type': 'application/x-www-form-urlencoded; charset=UTF-8' } };

     request(options, function (error, response, body) {
       if (error) throw new Error(error);

       console.log(body);
     });

#### 3.3.Ruby mode call

     require 'uri'
     require 'net/http'

     url = URI("http://127.0.0.1:8888/v1/wallet/lock")

     http = Net::HTTP.new(url.host, url.port)

     request = Net::HTTP::Post.new(url)
     request["content-type"] = 'application/x-www-form-urlencoded; charset=UTF-8'

     response = http.request(request)
     puts response.read_body
    

#### 3.4.javaScript method call

     var data = null;

     var xhr = new XMLHttpRequest();

     xhr.addEventListener("readystatechange", function () {
       if (this.readyState === this.DONE) {
         console.log(this.responseText);
       }
     });

     xhr.open("POST", "http://127.0.0.1:8888/v1/wallet/lock");
     xhr.setRequestHeader("content-type", "application/x-www-form-urlencoded; charset=UTF-8");

     xhr.send(data);

#### 3.5.Calling in python mode


     import requests

     url = "http://127.0.0.1:8888/v1/wallet/lock"

     headers = {'content-type': 'application/x-www-form-urlencoded; charset=UTF-8'}

     response = requests.request("POST", url, headers=headers)

     print(response.text)

   
The return results are the same, and the return results are as follows


### 4. Lock all wallets

* Request method: POST
* Request interface name: http://127.0.0.1:8888/v1/wallet/lock_all

#### 4.1.Curl method call

     curl --request POST \
       --url http://127.0.0.1:8888/v1/wallet/lock_all \
       --header 'content-type: application/x-www-form-urlencoded; charset=UTF-8'

#### 4.2.NodeJs method calling

     var request = require("request");

     var options = { method: 'POST',
       url: 'http://127.0.0.1:8888/v1/wallet/lock_all',
       headers: { 'content-type': 'application/x-www-form-urlencoded; charset=UTF-8' } };

     request(options, function (error, response, body) {
       if (error) throw new Error(error);

       console.log(body);
     });

#### 4.3.Ruby mode call

     require 'uri'
     require 'net/http'

     url = URI("http://127.0.0.1:8888/v1/wallet/lock_all")

     http = Net::HTTP.new(url.host, url.port)

     request = Net::HTTP::Post.new(url)
     request["content-type"] = 'application/x-www-form-urlencoded; charset=UTF-8'

     response = http.request(request)
     puts response.read_body

#### 4.4.javaScript method call


     var data = null;

     var xhr = new XMLHttpRequest();

     xhr.addEventListener("readystatechange", function () {
       if (this.readyState === this.DONE) {
         console.log(this.responseText);
       }
     });

     xhr.open("POST", "http://127.0.0.1:8888/v1/wallet/lock_all");
     xhr.setRequestHeader("content-type", "application/x-www-form-urlencoded; charset=UTF-8");

     xhr.send(data);

#### 4.5.Calling in python mode

     import requests

     url = "http://127.0.0.1:8888/v1/wallet/lock_all"

     headers = {'content-type': 'application/x-www-form-urlencoded; charset=UTF-8'}

     response = requests.request("POST", url, headers=headers)

     print(response.text)
   
The return results are the same, and the return results are as follows


### 5. Unlock a wallet based on its name or password

* Request method: POST
* Interface name: `http://127.0.0.1:8888/v1/wallet/unlock`

#### 5.1.Curl method call

     curl --request POST \
       --url http://127.0.0.1:8888/v1/wallet/unlock \
       --header 'content-type: application/x-www-form-urlencoded; charset=UTF-8'

#### 5.2.NodeJs method calling

     var request = require("request");

     var options = { method: 'POST',
       url: 'http://127.0.0.1:8888/v1/wallet/unlock',
       headers: { 'content-type': 'application/x-www-form-urlencoded; charset=UTF-8' } };

     request(options, function (error, response, body) {
       if (error) throw new Error(error);

       console.log(body);
     });

#### 5.3.Ruby mode call

     require 'uri'
     require 'net/http'

     url = URI("http://127.0.0.1:8888/v1/wallet/unlock")

     http= Net::HTTP.new(url.host, url.port)

     request = Net::HTTP::Post.new(url)
     request["content-type"] = 'application/x-www-form-urlencoded; charset=UTF-8'

     response = http.request(request)
     puts response.read_body

#### 5.4.javaScript method call

     var data = null;

     var xhr = new XMLHttpRequest();

     xhr.addEventListener("readystatechange", function () {
       if (this.readyState === this.DONE) {
         console.log(this.responseText);
       }
     });

     xhr.open("POST", "http://127.0.0.1:8888/v1/wallet/unlock");
     xhr.setRequestHeader("content-type", "application/x-www-form-urlencoded; charset=UTF-8");

     xhr.send(data);

#### 5.5.Calling via python

     import requests

     url = "http://127.0.0.1:8888/v1/wallet/unlock"

     headers = {'content-type': 'application/x-www-form-urlencoded; charset=UTF-8'}

     response = requests.request("POST", url, headers=headers)

     print(response.text)

   
The return results are the same, and the return results are as follows

### 6.Introduce the private key to the given wallet

* Interface request method: POST
* Interface name: `http://127.0.0.1:8888/v1/wallet/import_key`

#### 6.1.Curl method call

     curl --request POST \
       --url http://127.0.0.1:8888/v1/wallet/import_key \
       --header 'content-type: application/x-www-form-urlencoded; charset=UTF-8'

#### 6.2. NodeJs method calling

     var request = require("request");

     var options = { method: 'POST',
       url: 'http://127.0.0.1:8888/v1/wallet/import_key',
       headers: { 'content-type': 'application/x-www-form-urlencoded; charset=UTF-8' } };

     request(options, function (error, response, body) {
       if (error) throw new Error(error);

       console.log(body);
     });

#### 6.3.Ruby mode call

     require 'uri'
     require 'net/http'

     url = URI("http://127.0.0.1:8888/v1/wallet/import_key")

     http = Net::HTTP.new(url.host, url.port)

     request = Net::HTTP::Post.new(url)
     request["content-type"] = 'application/x-www-form-urlencoded; charset=UTF-8'

     response = http.request(request)
     puts response.read_body


#### 6.4.javaScript method call

     var data = null;

     var xhr = new XMLHttpRequest();

     xhr.addEventListener("readystatechange", function () {
       if (this.readyState === this.DONE) {
         console.log(this.responseText);
       }
     });

     xhr.open("POST", "http://127.0.0.1:8888/v1/wallet/import_key");
     xhr.setRequestHeader("content-type", "application/x-www-form-urlencoded; charset=UTF-8");

     xhr.send(data);

#### 6.5.Calling in python mode

     import requests

     url = "http://127.0.0.1:8888/v1/wallet/import_key"

     headers = {'content-type': 'application/x-www-form-urlencoded; charset=UTF-8'}

     response = requests.request("POST", url, headers=headers)

     print(response.text)

The return results are the same, and the return results are as follows


### 7. List all wallets

* Interface request method: POST
* Interface name: `http://127.0.0.1:8888/v1/wallet/list_wallets`

#### 7.1.Curl method call

     curl --request POST \
       --url http://127.0.0.1:8888/v1/wallet/list_wallets \
       --header 'content-type: application/x-www-form-urlencoded; charset=UTF-8'

#### 7.2.NodeJs method calling

     var request = require("request");

     var options = { method: 'POST',
       url: 'http://127.0.0.1:8888/v1/wallet/list_wallets',
       headers: { 'content-type': 'application/x-www-form-urlencoded; charset=UTF-8' } };

     request(options, function (error, response, body) {
       if (error) throw new Error(error);

       console.log(body);
     });

#### 7.3.Ruby mode call

     require 'uri'
     require 'net/http'

     url = URI("http://127.0.0.1:8888/v1/wallet/list_wallets")

     http = Net::HTTP.new(url.host, url.port)

     request = Net::HTTP::Post.new(url)
     request["content-type"] = 'application/x-www-form-urlencoded; charset=UTF-8'

     response = http.request(request)
     puts response.read_body

#### 7.4.javaScript method call

     var data = null;

     var xhr = new XMLHttpRequest();

     xhr.addEventListener("readystatechange", function () {
       if (this.readyState === this.DONE) {
         console.log(this.responseText);
       }
     });

     xhr.open("POST", "http://127.0.0.1:8888/v1/wallet/list_wallets");
     xhr.setRequestHeader("content-type", "application/x-www-form-urlencoded; charset=UTF-8");

     xhr.send(data);

#### 7.5.Calling in python mode

     import requests

     url = "http://127.0.0.1:8888/v1/wallet/list_wallets"

     headers = {'content-type': 'application/x-www-form-urlencoded; charset=UTF-8'}

     response = requests.request("POST", url, headers=headers)

     print(response.text)
   
The return results are the same, and the return results are as follows

### 8. List all keys by all wallets

* Interface request method: POST
* Interface name: `http://127.0.0.1:8888/v1/wallet/list_keys`

#### 8.1.Curl method call

     curl --request POST \
       --url http://127.0.0.1:8888/v1/wallet/list_keys \
       --header 'content-type: application/x-www-form-urlencoded; charset=UTF-8'

#### 8.2.NodeJs method calling

     var request = require("request");

     var options = { method: 'POST',
       url: 'http://127.0.0.1:8888/v1/wallet/list_keys',
       headers: { 'content-type': 'application/x-www-form-urlencoded; charset=UTF-8' } };

     request(options, function (error, response, body) {
       if (error) throw new Error(error);

       console.log(body);
     });

#### 8.3.Ruby mode call

     require 'uri'
     require 'net/http'

     url = URI("http://127.0.0.1:8888/v1/wallet/list_keys")

     http = Net::HTTP.new(url.host, url.port)

     request = Net::HTTP::Post.new(url)
     request["content-type"] = 'application/x-www-form-urlencoded; charset=UTF-8'

     response = http.request(request)
     puts response.read_body

#### 8.4.javaScript method call

     var data = null;

     var xhr = new XMLHttpRequest();

     xhr.addEventListener("readystatechange", function () {
       if (this.readyState === this.DONE) {
         console.log(this.responseText);
       }
     });

     xhr.open("POST", "http://127.0.0.1:8888/v1/wallet/list_keys");
     xhr.setRequestHeader("content-type", "application/x-www-form-urlencoded; charset=UTF-8");

     xhr.send(data);

#### 8.5.Calling in python mode

     import requests

     url = "http://127.0.0.1:8888/v1/wallet/list_keys"

     headers = {'content-type': 'application/x-www-form-urlencoded; charset=UTF-8'}

     response = requests.request("POST", url, headers=headers)

     print(response.text)
   
The return results are the same, and the return results are as follows

### 9. Obtain the public key from the wallet

* Interface request method: POST
* Interface name: `http://127.0.0.1:8888/v1/wallet/get_public_keys`

#### 9.1.Curl method call

     curl --request POST \
       --url http://127.0.0.1:8888/v1/wallet/get_public_keys \
       --header 'content-type: application/x-www-form-urlencoded; charset=UTF-8'

#### 9.2. NodeJs method calling

     var request = require("request");

     var options = { method: 'POST',
       url: 'http://127.0.0.1:8888/v1/wallet/get_public_keys',
       headers: { 'content-type': 'application/x-www-form-urlencoded; charset=UTF-8' } };

     request(options, function (error, response, body) {
       if (error) throw new Error(error);

       console.log(body);
     });

#### 9.3.Ruby mode call

     require 'uri'
     require 'net/http'

     url = URI("http://127.0.0.1:8888/v1/wallet/get_public_keys")

     http = Net::HTTP.new(url.host, url.port)

     request = Net::HTTP::Post.new(url)
     request["content-type"] = 'application/x-www-form-urlencoded; charset=UTF-8'

     response = http.request(request)
     puts response.read_body

#### 9.4.javaScript method call

     var data = null;

     var xhr = new XMLHttpRequest();

     xhr.addEventListener("readystatechange", function () {
       if (this.readyState === this.DONE) {
         console.log(this.responseText);
       }
     });

     xhr.open("POST", "http://127.0.0.1:8888/v1/wallet/get_public_keys");
     xhr.setRequestHeader("content-type", "application/x-www-form-urlencoded; charset=UTF-8");

     xhr.send(data);

#### 9.5.Calling in python mode

     import requests

     url = "http://127.0.0.1:8888/v1/wallet/get_public_keys"

     headers = {'content-type': 'application/x-www-form-urlencoded; charset=UTF-8'}

     response = requests.request("POST", url, headers=headers)

     print(response.text)

   
The return results are the same, and the return results are as follows


### 10. Set wallet automatic lock timeout

* Interface request method: POST
* Interface name: `http://127.0.0.1:8888/v1/wallet/set_timeout`

#### 10.1.Curl method call

     curl --request POST \
       --url http://127.0.0.1:8888/v1/wallet/set_timeout \
       --header 'content-type: application/x-www-form-urlencoded; charset=UTF-8'

#### 10.2.NodeJs method calling

     var request = require("request");

     var options = { method: 'POST',
       url: 'http://127.0.0.1:8888/v1/wallet/set_timeout',
       headers: { 'content-type': 'application/x-www-form-urlencoded; charset=UTF-8' } };

     request(options, function (error, response, body) {
       if (error) throw new Error(error);

       console.log(body);
     });

#### 10.3.Ruby method call

     require 'uri'
     require 'net/http'

     url = URI("http://127.0.0.1:8888/v1/wallet/set_timeout")

     http = Net::HTTP.new(url.host, url.port)

     request = Net::HTTP::Post.new(url)
     request["content-type"] = 'application/x-www-form-urlencoded; charset=UTF-8'

     response = http.request(request)
     puts response.read_body

#### 10.4.javaScript method call

     var data = null;

     var xhr = new XMLHttpRequest();

     xhr.addEventListener("readystatechange", function () {
       if (this.readyState === this.DONE) {
         console.log(this.responseText);
       }
     });

     xhr.open("POST", "http://127.0.0.1:8888/v1/wallet/set_timeout");
     xhr.setRequestHeader("content-type", "application/x-www-form-urlencoded; charset=UTF-8");

     xhr.send(data);

#### 10.5.Calling in python mode

     import requests

     url = "http://127.0.0.1:8888/v1/wallet/set_timeout"

     headers = {'content-type': 'application/x-www-form-urlencoded; charset=UTF-8'}

     response = requests.request("POST", url, headers=headers)

     print(response.text)

   
The return results are the same, and the return results are as follows


### 11. Transaction signature

* Interface request method: POST
* Interface name: `http://127.0.0.1:8888/v1/wallet/sign_transaction`

#### 11.1.Curl method call

     curl --request POST \
       --url http://127.0.0.1:8888/v1/wallet/sign_transaction \
       --header 'content-type: application/x-www-form-urlencoded; charset=UTF-8'

#### 11.2.NodeJs method calling

     var request = require("request");

     var options = { method: 'POST',
       url: 'http://127.0.0.1:8888/v1/wallet/sign_transaction',headers: { 'content-type': 'application/x-www-form-urlencoded; charset=UTF-8' } };

     request(options, function (error, response, body) {
       if (error) throw new Error(error);

       console.log(body);
     });

#### 11.3.Ruby mode call

     require 'uri'
     require 'net/http'

     url = URI("http://127.0.0.1:8888/v1/wallet/sign_transaction")

     http = Net::HTTP.new(url.host, url.port)

     request = Net::HTTP::Post.new(url)
     request["content-type"] = 'application/x-www-form-urlencoded; charset=UTF-8'

     response = http.request(request)
     puts response.read_body

#### 11.4.javaScript method call

     var data = null;

     var xhr = new XMLHttpRequest();

     xhr.addEventListener("readystatechange", function () {
       if (this.readyState === this.DONE) {
         console.log(this.responseText);
       }
     });

     xhr.open("POST", "http://127.0.0.1:8888/v1/wallet/sign_transaction");
     xhr.setRequestHeader("content-type", "application/x-www-form-urlencoded; charset=UTF-8");

     xhr.send(data);

#### 11.5.Calling in python mode

     import requests

     url = "http://127.0.0.1:8888/v1/wallet/sign_transaction"

     headers = {'content-type': 'application/x-www-form-urlencoded; charset=UTF-8'}

     response = requests.request("POST", url, headers=headers)

     print(response.text)
   
The return results are the same, and the return results are as follows

### 12. Set up wallet

* Interface request method: POST
* Interface name: `http://127.0.0.1:8888/v1/wallet/set_dir`

#### 12.1.Curl method call

     curl --request POST \
       --url http://127.0.0.1:8888/v1/wallet/set_dir \
       --header 'content-type: application/x-www-form-urlencoded; charset=UTF-8'

#### 12.2.NodeJs method calling

     var request = require("request");

     var options = { method: 'POST',
       url: 'http://127.0.0.1:8888/v1/wallet/set_dir',
       headers: { 'content-type': 'application/x-www-form-urlencoded; charset=UTF-8' } };

     request(options, function (error, response, body) {
       if (error) throw new Error(error);

       console.log(body);
     });

#### 12.3.Ruby mode call

     require 'uri'
     require 'net/http'

     url = URI("http://127.0.0.1:8888/v1/wallet/set_dir")

     http = Net::HTTP.new(url.host, url.port)

     request = Net::HTTP::Post.new(url)
     request["content-type"] = 'application/x-www-form-urlencoded; charset=UTF-8'

     response = http.request(request)
     puts response.read_body

#### 12.4.javaScript method call

     var data = null;

     var xhr = new XMLHttpRequest();

     xhr.addEventListener("readystatechange", function () {
       if (this.readyState === this.DONE) {
         console.log(this.responseText);
       }
     });

     xhr.open("POST", "http://127.0.0.1:8888/v1/wallet/set_dir");
     xhr.setRequestHeader("content-type", "application/x-www-form-urlencoded; charset=UTF-8");

     xhr.send(data);
    
#### 12.5.Calling in python mode

     import requests

     url = "http://127.0.0.1:8888/v1/wallet/set_dir"

     headers = {'content-type': 'application/x-www-form-urlencoded; charset=UTF-8'}

     response = requests.request("POST", url, headers=headers)

     print(response.text)

   
The return results are the same, and the return results are as follows

### 13. Set the key

* Interface request method: POST
* Interface name: `http://127.0.0.1:8888/v1/wallet/set_eosio_key`

#### 13.1.Curl method call

     curl --request POST \
       --url http://127.0.0.1:8888/v1/wallet/set_eosio_key \
       --header 'content-type: application/x-www-form-urlencoded; charset=UTF-8'

#### 13.2.NodeJs method calling

     var request = require("request");

     var options = { method: 'POST',
       url: 'http://127.0.0.1:8888/v1/wallet/set_eosio_key',
       headers: { 'content-type': 'application/x-www-form-urlencoded; charset=UTF-8' } };

     request(options, function (error, response, body) {
       if (error) throw new Error(error);

       console.log(body);
     });

#### 13.3.Ruby mode call

     require 'uri'
     require 'net/http'

     url = URI("http://127.0.0.1:8888/v1/wallet/set_eosio_key")

     http = Net::HTTP.new(url.host, url.port)

     request = Net::HTTP::Post.new(url)
     request["content-type"] = 'application/x-www-form-urlencoded; charset=UTF-8'

     response = http.request(request)
     puts response.read_body

#### 13.4.javaScript method call

     var data = null;

     var xhr = new XMLHttpRequest();

     xhr.addEventListener("readystatechange", function () {
       if (this.readyState === this.DONE) {
         console.log(this.responseText);
       }
     });

     xhr.open("POST", "http://127.0.0.1:8888/v1/wallet/set_eosio_key");
     xhr.setRequestHeader("content-type", "application/x-www-form-urlencoded; charset=UTF-8");

     xhr.send(data);

#### 13.5.Calling in python mode

     import requests

     url = "http://127.0.0.1:8888/v1/wallet/set_eosio_key"

     headers = {'content-type': 'application/x-www-form-urlencoded; charset=UTF-8'}

     response = requests.request("POST", url, headers=headers)

     print(response.text)

   
The return results are the same, and the return results are as follows

### 14. Create a key

* Interface request method: POST
* Interface name: `http://127.0.0.1:8888/v1/wallet/create_key`

#### 14.1.Curl method call

     curl --request POST \
       --url http://127.0.0.1:8888/v1/wallet/create_key \
       --header 'content-type: application/x-www-form-urlencoded; charset=UTF-8'

#### 14.2.NodeJs method calling

     var request = require("request");

     var options = { method: 'POST',
       url: 'http://127.0.0.1:8888/v1/wallet/create_key',
       headers: { 'content-type': 'application/x-www-form-urlencoded; charset=UTF-8' } };

     request(options, function (error, response, body) {
       if (error) throw new Error(error);

       console.log(body);
     });

#### 14.3.Ruby mode call

     require 'uri'
     require 'net/http'

     url = URI("http://127.0.0.1:8888/v1/wallet/create_key")

     http = Net::HTTP.new(url.host, url.port)

     request = Net::HTTP::Post.new(url)
     request["content-type"] = 'application/x-www-form-urlencoded; charset=UTF-8'

     response = http.request(request)
     puts response.read_body

#### 14.4.javaScript method call

     var data = null;

     var xhr = new XMLHttpRequest();

     xhr.addEventListener("readystatechange", function () {
       if (this.readyState === this.DONE) {
         console.log(this.responseText);
       }
     });

     xhr.open("POST", "http://127.0.0.1:8888/v1/wallet/create_key");
     xhr.setRequestHeader("content-type", "application/x-www-form-urlencoded; charset=UTF-8");

     xhr.send(data);

#### 14.5.Python method calling

     import requests

     url = "http://127.0.0.1:8888/v1/wallet/create_key"

     headers = {'content-type': 'application/x-www-form-urlencoded; charset=UTF-8'}

     response = requests.request("POST", url, headers=headers)

     print(response.text)

The return results are the same, and the return results are as follows


### 15.sign_digest

* Interface request method: POST
* Interface name: `http://127.0.0.1:8888/v1/wallet/sign_digest`

#### 15.1.Curl method call

     curl --request POST \
       --url http://127.0.0.1:8888/v1/wallet/sign_digest \
       --header 'content-type: application/x-www-form-urlencoded; charset=UTF-8'

#### 15.2.NodeJs method calling

     var request = require("request");

     var options = { method: 'POST',
       url: 'http://127.0.0.1:8888/v1/wallet/sign_digest',
       headers: { 'content-type': 'application/x-www-form-urlencoded; charset=UTF-8' } };

     request(options, function (error, response, body) {
       if (error) throw new Error(error);

       console.log(body);
     });

#### 15.3.Ruby mode call

     require 'uri'
     require 'net/http'

     url = URI("http://127.0.0.1:8888/v1/wallet/sign_digest")

     http = Net::HTTP.new(url.host, url.port)

     request = Net::HTTP::Post.new(url)
     request["content-type"] = 'application/x-www-form-urlencoded; charset=UTF-8'

     response = http.request(request)
     puts response.read_body

#### 15.4.javaScript method call

     var data = null;

     var xhr = new XMLHttpRequest();

     xhr.addEventListener("readystatechange", function () {
       if (this.readyState === this.DONE) {
         console.log(this.responseText);
       }
     });

     xhr.open("POST", "http://127.0.0.1:8888/v1/wallet/sign_digest");
     xhr.setRequestHeader("content-type", "application/x-www-form-urlencoded; charset=UTF-8");

     xhr.send(data);

#### 15.5.Calling in python mode

     import requests

     url = "http://127.0.0.1:8888/v1/wallet/sign_digest"

     headers = {'content-type': 'application/x-www-form-urlencoded; charset=UTF-8'}

     response = requests.request("POST", url, headers=headers)

     print(response.text)
   
The return results are the same, and the return results are as follows

## 6. NodeJs development of EOS wallet

### 1. Use NodeJs to generate a wallet account

#### 1.1. Install JS library

     npm install eosjs --save
    
     npm install eosjs-ecc --save

#### 1.2. Generate wallet key pair

Below is the mnemonic phrase generated using BIP39 to generate the key pair

     var eosEcc = require('eosjs-ecc');

     var eosPrivate = eosEcc.seedPrivate("Late Qian destroyed Xingji Pu Mu Mi Spear and Su Jie Sheng squeezed words");
     console.log("EOS private key:",eosPrivate)
     const eosPubkey = eosEcc.privateToPublic(eosPrivate);
     console.log("EOS public key:",eosPubkey)
 
The code above uses Chinese mnemonics. For the generation of mnemonics, please refer to the chapter on mnemonics in this book.

The execution results are as follows:

     EOS private key: 5KdZWbAFRXhWshbX8TG6DKbTmo9E7VNm5s7onotvE2kbS285yXc
     EOS public key: EOS7qvrWb75xXY4dNWLBZSUTZkb6EMmwXhS5T9kxfMeWLggiV8XXE

 
#### 1.3. Randomly generate a new private key and public key

     eosEcc.randomKey().then(privateKey => {
         console.log('private key:\t', privateKey)
         console.log('Public key:\t', eosEcc.privateToPublic(privateKey))
     })
    
The execution results are as follows:

     Private key: 5Jzk3WKeY8TkLVSUm89GVMPV2XGeSunGZwNL8JioRvUpoiUfrfN
     Public key: EOS6PisEYXq32dzKP24ZE7JBXnTsiKNyDKXWn5g6xaS9xbr59WRWw
 
#### 1.4.eos environment configuration

keyProvider: fill in the private key you generated above; httpEndpoint: development chain url and port; chainId: indicates the ID of that chain. This can be a private chain you built yourself, or it can be the public chain ID of EOS; I will follow All information filled in is the main network information.

     var Eos = require('eosjs')
     var eosConfig = {
         keyProvider: ['privateKey'],
         httpEndpoint: 'https://nodes.get-scatter.com',
         chainId: "aca376f206b8fc25a6ed44dbdc66547c36c6c33e3a119ffbeaef943642f0e906",
         broadcast: true,
     }
    
#### 1.5. Generate EOS account

Here we use the good environment configured in the previous step; creatoraccount represents the main account, newaccount represents the new account; newaccount_pubkey represents the public key of the new account.

The code below represents a new account

     tr.newaccount({
         creator: creatoraccount,
         name: newaccount,
         owner: newaccount_pubkey,
         active: newaccount_pubkey
      })

This step is to recharge RAM for the new account

     tr.buyrambytes({
         payer: creatoraccount,
         receiver: newaccount,
         bytes: 3072
     })

Mortgage CPU and NET resources for new accounts

     tr.delegatebw({
         from: creatoraccount,
         receiver: newaccount,
         stake_net_quantity: '1.0000 EOS',
         stake_cpu_quantity: '1.0000 EOS',
         transfer: 0
     })

The code below is the complete code:

     var eos = Eos(eosConfig)
     var creatoraccount = "guosj";
     var newaccount = "newaccount";
     var newaccount_pubkey = pubkey;
     eos.transaction(tr => {
         tr.newaccount({
             creator: creatoraccount,
             name: newaccount,
             owner: newaccount_pubkey,
             active: newaccount_pubkey
         })

         tr.buyrambytes({
             payer: creatoraccount,
             receiver: newaccount,
             bytes: 3072
         })

         tr.delegatebw({
             from: creatoraccount,
             receiver: newaccount,
             stake_net_quantity: '2.0000 EOS',
             stake_cpu_quantity: '2.0000 EOS',
             transfer: 0
         })
     }).then(r => {
         console.log(r);
     }).catch(e => {
         console.log(e)
     });

     }catch (e){

     }

### 2. Use NodeJs to complete the wallet transfer process

Developed using EOS's official library eosjs, users need to enter the private key themselves. This method of operation is unsafe and can easily cause the private key to be leaked. Transfer to account: accountName; transfer amount: quantity.

     const { Api, JsonRpc, RpcError, JsSignatureProvider } = require('eosjs');
     const ecc = require('eosjs-ecc');
     const fetch = require('node-fetch');
     const { TextDecoder, TextEncoder } = require('text-encoding');
     const rpcUrl = 'rpc interface calling address'
     const rpc = new JsonRpc(rpcUrl, { fetch });
    
     async function transfer(accountName,quantity) {
         let signatureProvider = new JsSignatureProvider([pkeys[0].privateKey]);
         let api = new Api({ rpc, signatureProvider, textDecoder: new TextDecoder(), textEncoder: new TextEncoder() });
         let result = await api.transact({
             actions: [{
                 account: 'eosio.token',
                 name: 'transfer',
                 authorization: [{
                     actor: pkeys[0].actor,
                     permission: 'active',
                 }],
                 data: {
                     from: pkeys[0].actor,
                     to: accountName,
                     quantity: quantity,
                     memo: '',
                 },
             }]
         }, {
             blocksBehind: 3,
             expireSeconds: 30,
         });
         console.dir(result);
     };
 

Use eosjs API + wallet API for development. Regarding the wallet API, please refer to the introduction above. This method is suitable for public accounts to transfer funds to different users. It is equivalent to using the wallet service for key management, hiding the private key, and only needs to provide the public key and wallet service address in the code.

     async function transfer() {
         try {
             let actor = "account"
             let transferTo = "toAccount"
             let quantity = "10 EOS"
             let memo = "baoyangni"
             let blocksBehind = 3
             let expireSeconds = 100
             let info = await rpc.get_info();
             if (info != null && info.chain_id != null && info.head_block_num != null) {
                 let chain_id = info.chain_id;
                 let head_block_num = info.head_block_num - blocksBehind;
                 let block = await get_block(head_block_num);
                 if (block != null && block.ref_block_prefix != null && block.timestamp != null) {
                     let data = await abi_json_to_bin(actor, transferTo, quantity, memo)
                     if (data != null) {
                         let transactions = {
                             "max_net_usage_words": 0,
                             "max_cpu_usage_ms": 0,
                             "delay_sec": 0,
                             "context_free_actions": [],
     "actions": [{
                                 "account": "eosio.token",
                                 "name": "transfer",
                                 "authorization": [{
                                     "actor": actor,
                                     "permission": "active"
                                 }],
                                 "data": data
                             }],
                             "transaction_extensions": [],
                             "expiration": ser.timePointSecToDate(ser.dateToTimePointSec(block.timestamp) + expireSeconds),
                             "ref_block_num": block.block_num & 0xffff,
                             "ref_block_prefix": block.ref_block_prefix
                         };

                         let signTransaction = await sign_transaction(transactions, ["key"], chain_id);

                         if (signTransaction != null && signTransaction.signatures != null) {
                             var transaction_detail = await push_transaction(transactions, signTransaction.signatures);
                             console.log('push_transaction=transaction_id==' + transaction_detail.transaction_id);
                         }

                     }
                 }
             }
         } catch (e) {
             console.log(e)
         }
     }
