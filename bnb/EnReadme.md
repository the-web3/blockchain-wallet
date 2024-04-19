## BNB wallet development

### 1. Introduction to Binance Main Chain

Binance Chain is a blockchain software system developed by Binance and its community. Binance DEX refers to the decentralized exchange developed on Binance Chain. Binance aims to create an alternative marketplace for issuing and exchanging digital assets in a decentralized manner. The underlying technology of Binance Chain is developed based on the currently popular project COSMOS. We will introduce COSMOS in detail in the following courses. The Binance Chain network is composed of decentralized nodes. Transactions can be completed almost instantly on Binance Chain, and the block generation time only takes 1 second. Its speed is higher than other blockchains. Regarding the introduction of Binance Chain, I won’t go into details here. There is a lot of information on the Internet.

### 2. Binance main chain node construction

#### 1. Build a full node

Binance Chain's full nodes are witnesses, which observe consensus messages, download blocks from data seed nodes and execute business logic to achieve a consistent state as a validator node (and other full nodes). Full nodes also help the network by accepting transactions from other nodes and then relaying them to the core Binance network.

Supported platforms: Mac OS X, Windows and Linux.

System requirements, computer hardware must meet the following node requirements:

* Desktop or laptop hardware running the latest version of Mac OS X, Windows or Linux.
*500 GB of available disk space, minimum read/write speed of 100 MB/s.
* 4-core CPU and 8 gigabytes of memory (RAM).
* Broadband Internet connection with upload/download speeds of at least 1 megabyte per second
* A full node must be running for at least 4 hours every 24 hours to catch up with Binance Chain. More time will be better, run your node continuously for best results.

##### Build a complete node

1.Install git lfs

Git Large File Storage (LFS) replaces large files, such as audio samples, videos, datasets, and graphics, with text pointers in Git while storing the file contents on a remote server such as GitHub.com or GitHub Enterprise.

Please go to this interface to download and install lfs: `https://git-lfs.github.com/`

2. Use lfs to download binary files

     git lfs clone https://github.com/binance-chain/node-binary.git
    
Latest full node version information:

     https://github.com/binance-chain/node-binary/blob/master/fullnode/Changelog.md

Go to the directory based on the network you want to join.
Replace the network variable with testnet or prod in the following command:

     cd node-binary/fullnode/{network}/{version}
    
3.Initial home directory

First, you need to choose a main folder `$BNCHOME` for `Binance Chain` (i.e. `~/.bnbchaind`). You can set this via:

Place `app.toml, config.toml` and `genesis.json` from `node-binary/fullnode/{network}/{version}/config/` into `$BNCHOME/config`

4. Add seed node

For an entire node to function, it must be connected to one or more known nodes to join the Binance Chain.

There are several well-known seed nodes that provide known node addresses to newly joined full nodes in the network.

They are already in the `node-binary/fullnode/{network}/{version}/config/config.toml` file.

You can also get the torrent information via a simple python script (replace the domain name according to the network you are using):

     import requests, json
     d = requests.get('https://dex.binance.org/api/v1/peers').text # replace dex.binance.org with testnet-dex.binance.org for testnet
     l = json.loads(d)
     seeds = ",".join([ (seed["id"]+"@"+seed["original_listen_addr"]) for seed in l if seed["accelerated"] == False])
     print(seeds)
    
If you would like to add a seed node, feel free to edit the field seed of `$BNCHOME/config/config.yaml` with the seed node information returned in the previous request.

5. Additional configuration

* Log: The log file is located in `home` - the directory specified when starting `bnbchaind`.
* The latest log file is `bnc.log`. This process will create a new log file every hour.
* To ensure that you have enough disk space to retain the log files, we strongly recommend that you change the log location by changing the `logFileRoot` option in `$BNCHOME/config/app.toml`.
* Service port: `RPC` service listening port `27147`, `P2P` service default listening port `27146`.
* Unless the full node must listen on other ports, make sure to open these two ports before starting the full node.
* Storage: All state and block data will be stored under `$BNCHOME/data`, do not delete or edit any of these files.

6. Start the node

Start the entire node according to the platform. Replace the platform variable with mac windows or linux in the following command:

     ./{{platform}}/bnbchaind start --home $BNCHOME --pruning breathe &

Only after catching up with Binance Chain can the entire node handle the request correctly.

7. Synchronize data

You can synchronize with other peers in the blockchain network in two ways: Quick Sync State Sync.

Both methods can be used together.

Quickly sync data:

* The default way to synchronize with other data seed nodes is quick sync.
* In fast sync, you need to download all chunks from peers and perform all transactions in each chunk.
* The synchronization speed is about 20 blocks/second, which is slower than status synchronization.

The configuration is located in `$BNCHOME/config/config.toml`:

     fast_sync must be set to true
     state_sync_reactor can be set to false or true
     state_sync can be set to false or true

Status synchronization:

State synchronization will keep the entire node's application state up to date without downloading all blocks. Sync is faster than Quick Sync. However, you will need to allocate more than 16 GB of memory to the entire node for this feature to work properly.

The configuration is located in `$BNCHOME/config/config.toml`:

      state_sync_reactor must be set to true
      state_sync must be set to true
      recv_rate must be set to 102428800
      It is recommended to set ping_interval to 10m30s
      It is recommended to set pong_timeout to 450s

State synchronization can help fullnode be in the same state as other peers in a short period of time (according to our testing, a month ~800M database snapshot in the binance chain testnet can be synchronized in about 45 minutes) so that you can receive the latest blocks /Transactions and queries for the latest status of order books, account balances, etc. But state sync will not download history blocks before state sync height, if you start a node with state sync and it syncs at height 10000, your local database will only have blocks after height 10000.

If a full node has been started, the recommended approach is to delete (after backup) the `$BNCHOME/data` directory and `$BNCHOME/config/priv_validator_key.json` before enabling state synchronization.

8. Monitor the synchronization process

You can verify multiple times that `curl localhost:27147/status` completes status synchronization and see if `latest_block_height` responds by increasing.

      "sync_info": {
        ...
        "latest_block_height": "878092",
        "latest_block_time": "2019-04-15T00:01:22.610803768Z",
        ...
      }

If status synchronization is unsuccessful, please repeatedly delete the `$BNCHOME/data` directory and `$BNCHOME/config/priv_validator_key.json` before starting the full node next time to prevent data inconsistency.

Once the state synchronization is successful, subsequent full node restarts will no longer perform state synchronization (if the local blocks are not contiguous).

But if you do want to do a state sync again (never mind that there were lost blocks between the last stop and the latest state sync snapshot) and you want to keep the already synced blocks, you can delete `$BNCHOME/data/STATESYNC.LOCK` .

For example, you start your full node on January 1st with a status sync of height 10000 and shut it down some time later on February 10th at height 22000.

Now its March 1st, the latest syncable block height is 50000, you don't care about the blocks between 22000 and 50000, you can delete `$BNCHOME/data/STATESYNC.LOCK` before starting your node.

The entire node will then continue state synchronization from height 50000.

Turning off state_sync_reactor and state_sync can save memory after successful state synchronization.

8. Update full node

In most cases, download the new binary and replace it, then restart the entire node. In special cases, you may have to perform additional steps for incompatible versions (hard forks).

9.Monitoring

By default, Prometheus is enabled on port 28660 with the endpoint `/metrics`.


#### 2. Build light nodes

The Light client is a program that connects to a full node to help users access and interact with the Binance chain in a secure and decentralized manner without the need to synchronize the full blockchain.

##### Light client and full node

* The Light client does not store blocks or state, so it requires less disk space (50MB is enough).
*Light client does not join the p2p network and does not incur any network costs when idle. Network overhead depends on how many requests the light client handles simultaneously.
* Light clients do not replay the state of the chain, so there is no CPU cost when idle. The CPU cost also depends on the number of requests the light client handles simultaneously.
*Light client is faster than full node even though it is months behind the core network. It only takes a few seconds to catch up with the core network.

##### Platform and system requirements

platform:

* Supports running lightweight client nodes on Mac OS X, Windows and Linux.
* The light client will be open source soon, after which you can cross-compile the light client binary and run it on other platforms.

Require:

* 50 megabytes of available disk space.
* 2 CPU cores, 50 megabytes of memory (RAM).

##### Run a light client

1. Download node

     git clone https://github.com/binance-chain/node-binary.git

Go to the directory based on the network you want to join. Replace the network variable with testnet or prod in the following command:

     cd node-binary/lightd/{network}/{version}

help information

         ./lightd --help
         This node will run a secure proxy to a binance rpc server.

         All calls that can be tracked back to a block header by a proof
         will be verified before passing them back to the caller. Other that
         that it will present the same interface as a full binance node,
         just with added trust and running locally.

         Usage:
           lite [flags]

         Flags:
               --cache-size int Specify the memory trust store cache size (default 10)
               --chain-id string Specify the binance chain ID (default "bnbchain")
           -h, --help help for lite
               --home-dir string Specify the home directory (default ".binance-lite")
               --laddr string Serve the proxy on the given address (default "tcp://localhost:27147")
               --max-open-connections int Maximum number of simultaneous connections (including WebSocket). (default 900)
               --node string Connect to a binance node at this address (default "tcp://localhost:27147")
You can specify all parameters above.

Start light client nodes based on the platform. Replace the platform variable with mac, windows, or linux in the following command:

         ./{{platform}}/lightd --chain-id "{chain-id}" --node tcp://{full node addr}:80 > node.log &

* There are two required parameters to start the light client node: chain id and full node addr.
* The chain ID of the network to join.
* You can find the chain ID in the genesis file in the test network or in the genesis file in the prod network.
* The full node address field can be the address of any full node you have deployed.
* You can refer to Running a Binance Chain Full Node for more details.

We provide a bunch of complete nodes that you can connect to mainnet and testnet. You can get complete node information through a simple python script (pay attention to replacing fields according to different networks):

     import requests, json
     d = requests.get('https://dex.binance.org/api/v1/peers').text # replace dex.binance.org with testnet-dex.binance.org for testnet
     l = json.loads(d)
     seeds = ",".join([ (seed["id"]+"@"+seed["original_listen_addr"]) for seed in l if seed["accelerated"] == False])
     print(seeds)
    
Mainnet example:

     ./lightd --chain-id "Binance-Chain-Tigris" --node tcp://dataseed1.binance.org:80 > node.log &


Testnet example:
    
     ./lightd --chain-id "Binance-Chain-Nile" --node tcp://data-seed-pre-0-s1.binance.org:80 > node.log &


### 3. Detailed introduction of Binance go-sdk

go-sdk is Binance's open source code library that can communicate with Binance nodes. In addition to creating and submitting different transactions, Binance Chain GO SDK also provides a thin wrapper for the BNC Chain API for read-only endpoints. It includes the following core components:

* Client - Implementation of Binance Chain transaction types and queries, such as transfers and trades.
* General - core encryption functions, uuid functions and other useful functions.
* End-to-end testing package for e2e-go-sdk developers. For ordinary users, it is also a good reference for using go-sdk.
* Keys - Implement KeyManage to manage private keys and accounts.
* types - core types of Binance Chain, such as coin, account, tx and msg.

#### 1. Installation

Requirements: go 11 or above

It is recommended to use go mod

Add `github.com/binance-chain/go-sdk` to go mod file

     require (
github.com/binance-chain/go-sdk latest
     )
     replace github.com/tendermint/go-amino => github.com/binance-chain/bnc-go-amino v0.14.1-binance.1

#### 2.Use of go-sdk

##### Keymanager

Before you start using the API, you should build a key manager to help sign transaction messages or verify signatures. Key Manager is an identity manager that defines your identity within bnbchain. It provides the following interfaces:

     typeKeyManager interface {
         Sign(tx.StdSignMsg) ([]byte, error)
         GetPrivKey() crypto.PrivKey
         GetAddr() txmsg.AccAddress

         ExportAsMnemonic() (string, error)
         ExportAsPrivateKey() (string, error)
         ExportAsKeyStore(password string) (*EncryptedKeyJSON, error)
     }
    
Four constructors to generate key managers:

     NewKeyManager() (KeyManager, error)

     NewMnemonicKeyManager(mnemonic string) (KeyManager, error)

     NewKeyStoreKeyManager(file string, auth string) (KeyManager, error)

     NewPrivateKeyManager(priKey string) (KeyManager, error)

     NewLedgerKeyManager(path ledger.DerivationPath) (KeyManager, error)

* NewKeyManager. You will get a new private key without providing anything, you can export and save this KeyManager.
* NewMnemonicKeyManager. You should provide your mnemonic, usually a 24-word string.
* NewKeyStoreKeyManager. You should provide the keystore json file and password, you can download the keystore json file when creating the wallet account.
* NewPrivateKeyManager. You should provide a hex-encoded string of your private key.
* NewLedgerKeyManager. You must have a ledger device with the binance ledger application and connect it to your machine.

Sample code:

By mnemonic:

mnemonic := "lock globe panda armed mandate fabric couple dove climb step stove price recall decrease fire sail ring media enhance excite deny valid ceiling arm"
keyManager, _ := keys.NewMnemonicKeyManager(mnemonic)

Through keystore file:

file := "testkeystore.json"
keyManager, err := NewKeyStoreKeyManager(file, "your password")
	
Generated via private key:

priv := "9579fff0cab07a4379e845a890105004ba4c8276f8ad9d22082b2acbf02d884b"
keyManager, err := NewPrivateKeyManager(priv)
	
Via ledger device:

bip44Params := keys.NewBinanceBIP44Params(0, 0)
keyManager, err := NewLedgerKeyManager(bip44Params.DerivationPath())

We provide three export functions for persistent key managers:

ExportAsMnemonic() (string, error)
ExportAsPrivateKey() (string, error)
ExportAsKeyStore(password string) (*EncryptedKeyJSON, error)

Sample code:

km, _ := NewKeyManager()
encryPlain1, _ := km.GetPrivKey().Sign([]byte("test plain"))
keyJSONV1, err := km.ExportAsKeyStore("testpassword")
bz, _ := json.Marshal(keyJSONV1)
ioutil.WriteFile("TestGenerateKeyStoreNoError.json", bz, 0660)
newkm, _ := NewKeyStoreKeyManager("TestGenerateKeyStoreNoError.json", "testpassword")
encryPlain2, _ := newkm.GetPrivKey().Sign([]byte("test plain"))
assert.True(t, bytes.Equal(encryPlain1, encryPlain2))
	
For Ledger keys, it cannot be exported. Because its private key is stored on the Ledger device, no one can directly access it from the outside.

##### Initialize client

import sdk "github.com/binance-chain/go-sdk/client"

mnemonic := "lock globe panda armed mandate fabric couple dove climb step stove price recall decrease fire sail ring media enhance excite deny valid ceiling arm"
//-----Init KeyManager-------------
keyManager, _ := keys.NewMnemonicKeyManager(mnemonic)

//-----Init sdk-------------
client, err := sdk.NewDexClient("testnet-dex.binance.org", types.TestNetwork, keyManager)

For sdk init, you should know the api address. In addition, you should know the type of network the api gateway is on, test network and production network have different configurations.

Test network: testnet-dex.binance.org
Production network: dex.binance.org

If you want to broadcast certain transactions, such as sending coins, creating an order or canceling an order, you should build a key manager.

Create a purchase order example:

createOrderResult, err := client.CreateOrder(tradeSymbol, nativeSymbol, txmsg.OrderSide.BUY, 100000000, 100000000, true)

##### RPC client

RPC endpoints can be used to interact with nodes directly via HTTP or websockets. Using RPC, you can perform low-level operations such as performing ABCI queries, viewing network/consistency status, or broadcasting transactions against full nodes or light clients.

Sample code:

nodeAddr := "tcp://127.0.0.1:27147"
testClientInstance := rpc.NewRPCClient(nodeAddr,types.TestNetwork)
status, err := c.Status()

#### 3.go-sdk API

##### API

For read options, each API requires a Query parameter. We provide a constructor for each Query parameter: NewXXXQuery. We recommend that you use this constructor when creating new Query parameters, because the construction only requires required parameters, for optional parameters, you can use WithXXX to add it.


The most important thing you should notice is that we use int64 to represent decimals. The decimal length is fixed at 8, which means: 100000000 is equal to 1, 150000000 is equal to 1.5, 1050000000 is equal to 10.5

For common transactions, the response is:

TxCommitResult

Ok bool, if the transaction is accepted by the link.
Log string, the error message of the transaction.
Hash String
Code int32, result code. Zero represents a penalty.
Data string, different types of transactions return different data messages.

### 4. Binance main chain wallet development

#### 1. Address and private key generation

The following is the code for go-sdk to generate addresses and private keys

package main

import (
"fmt"
"github.com/binance-chain/go-sdk/keys"
)
	
func main() {
mnemonic := "bottom quick strong ranch section decide pepper broken oven demand coin run jacket curious business achieve mule bamboo remain vote kid rigid bench rubber"
keyManger, _ := keys.NewMnemonicKeyManager(mnemonic)
fmt.Println("Address", keyManger.GetAddr())
priKey, _ := keyManger.ExportAsPrivateKey()
fmt.Println("privateKey",priKey)

fmt.Println("==========keystore=========")
aa, _ := keyManger.ExportAsKeyStore("111111")
fmt.Println("keystore ===", aa)
fmt.Println("==========keystore=========")
}


#### 2. Recharge

The following is the code for analyzing blocks using go-sdk:

package main

import (
"encoding/json"
"fmt"
"github.com/binance-chain/go-sdk/client/rpc"
ctypes "github.com/binance-chain/go-sdk/common/types"
"github.com/binance-chain/go-sdk/types"
"github.com/binance-chain/go-sdk/types/tx"
)

func main() {
nodeAddr := "tcp://127.0.0.1:27147"
client := rpc.NewRPCClient(nodeAddr, ctypes.ProdNetwork)

codec := types.NewCodec()

number := int64(8095579)
//number := int64(8643229)
res, _ := client.Block(&number)
txs := res.Block.Data.Txs

parsedTxs := make([]tx.StdTx, len(txs))
for i := range txs {
err := codec.UnmarshalBinaryLengthPrefixed(txs[i], &parsedTxs[i])
if err != nil {
fmt.Println("Error - codec unmarshal")
return
}
}

bz, err := json.Marshal(parsedTxs)

if err != nil {
fmt.Println("Error - json marshal")
return
}

fmt.Println(string(bz))
}

#### 3. Withdraw cash

The main thing to mention is signature and transfer, use the following code:

package transaction

import (
"fmt"
"time"

"github.com/binance-chain/go-sdk/client/basic"
"github.com/binance-chain/go-sdk/client/query"
"github.com/binance-chain/go-sdk/common/types"
"github.com/binance-chain/go-sdk/keys"
"github.com/binance-chain/go-sdk/types/msg"
"github.com/binance-chain/go-sdk/types/tx"
)

type TransactionClient interface {
CreateOrder(baseAssetSymbol, quoteAssetSymbol string, op int8, price, quantity int64, sync bool, options ...Option) (*CreateOrderResult, error)
CancelOrder(baseAssetSymbol, quoteAssetSymbol, refId string, sync bool, options ...Option) (*CancelOrderResult, error)
BurnToken(symbol string, amount int64, sync bool, options ...Option) (*BurnTokenResult, error)
ListPair(proposalId int64, baseAssetSymbol string, quoteAssetSymbol string, initPrice int64, sync bool, options ...Option) (*ListPairResult, error)
FreezeToken(symbol string, amount int64, sync bool, options ...Option) (*FreezeTokenResult, error)
UnfreezeToken(symbol string, amount int64, sync bool, options ...Option) (*UnfreezeTokenResult, error)
IssueToken(name, symbol string, supply int64, sync bool, mintable bool, options ...Option) (*IssueTokenResult, error)
SendToken(transfers []msg.Transfer, sync bool, options ...Option) (*SendTokenResult, error)
MintToken(symbol string, amount int64, sync bool, options ...Option) (*MintTokenResult, error)
TimeLock(description string, amount types.Coins, lockTime int64, sync bool, options ...Option) (*TimeLockResult, error)
TimeUnLock(id int64, sync bool, options ...Option) (*TimeUnLockResult, error)
TimeReLock(id int64, description string, amount types.Coins, lockTime int64, sync bool, options ...Option) (*TimeReLockResult, error)
SetAccountFlags(flags uint64, sync bool, options ...Option) (*SetAccountFlagsResult, error)
AddAccountFlags(flagOptions []types.FlagOption, sync bool, options ...Option) (*SetAccountFlagsResult, error)

SubmitListPairProposal(title string, param msg.ListTradingPairParams, initialDeposit int64, votingPeriod time.Duration, sync bool, options ...Option) (*SubmitProposalResult, error)
SubmitProposal(title string, description string, proposalType msg.ProposalKind, initialDeposit int64, votingPeriod time.Duration, sync bool, options ...Option) (*SubmitProposalResult, error)
DepositProposal(proposalID int64, amount int64, sync bool, options ...Option) (*DepositProposalResult, error)
VoteProposal(proposalID int64, option msg.VoteOption, sync bool, options ...Option) (*VoteProposalResult, error)

GetKeyManager() keys.KeyManager
}

type client struct {
basicClient basic.BasicClient
queryClient query.QueryClient
keyManager keys.KeyManager
chainId string
}

func NewClient(chainId string, keyManager keys.KeyManager, queryClient query.QueryClient, basicClient basic.BasicClient) TransactionClient {
return &client{basicClient, queryClient, keyManager, chainId}
}

func (c *client) GetKeyManager() keys.KeyManager {
return c.keyManager
}

type Option func(*tx.StdSignMsg) *tx.StdSignMsg

func WithSource(source int64) Option {
return func(txMsg *tx.StdSignMsg) *tx.StdSignMsg {
txMsg.Source = source
returntxMsg
}
}

func WithMemo(memo string) Option {
return func(txMsg *tx.StdSignMsg) *tx.StdSignMsg {
txMsg.Memo = memo
returntxMsg
}
}

func WithAcNumAndSequence(accountNum, seq int64) Option {
return func(txMsg *tx.StdSignMsg) *tx.StdSignMsg {
txMsg.Sequence = seq
txMsg.AccountNumber = accountNum
returntxMsg
}
}

func (c *client) broadcastMsg(m msg.Msg, sync bool, options ...Option) (*tx.TxCommitResult, error) {
// prepare message to sign
signMsg := &tx.StdSignMsg{
ChainID: c.chainId,
AccountNumber: -1,
Sequence: -1,
Memo: "",
Msgs: []msg.Msg{m},
Source: tx.Source,
}

for _, op := range options {
signMsg = op(signMsg)
}

if signMsg.Sequence == -1 || signMsg.AccountNumber == -1 {
fromAddr := c.keyManager.GetAddr()
acc, err := c.queryClient.GetAccount(fromAddr.String())
if err != nil {
return nil, err
}
signMsg.Sequence = acc.Sequence
signMsg.AccountNumber = acc.Number
}

// special logic for createOrder, to save account query
if orderMsg, ok := m.(msg.CreateOrderMsg); ok {
orderMsg.ID = msg.GenerateOrderID(signMsg.Sequence+1, c.keyManager.GetAddr())
signMsg.Msgs[0] = orderMsg
}

for _, m := range signMsg.Msgs {
if err := m.ValidateBasic(); err != nil {
return nil, err
}
}

// Hex encoded signed transaction, ready to be posted to BncChain API
hexTx, err := c.keyManager.Sign(*signMsg)
if err != nil {
return nil, err
}
param := map[string]string{}
if sync {
param["sync"] = "true"
}
commits, err := c.basicClient.PostTx(hexTx, param)
if err != nil {
return nil, err
}
if len(commits) < 1 {
return nil, fmt.Errorf("Len of tx Commit result is less than 1 ")
}
return &commits[0], nil
}

or:

func signAndSend() {
test1Mnemonic := "bottom quick strong ranch section decide pepper broken oven demand coin run jacket curious business achieve mule bamboo remain vote kid rigid bench rubber"
test1KeyManger, _ := keys.NewMnemonicKeyManager(test1Mnemonic)
keyManagerP1, _ := keys.NewPrivateKeyManager("091cf0dfe62c369b27be5ca5ceeecd2f80fa14d5bd04d27dc759ed6ac5f78d55")
fromAddr := test1KeyManger.GetAddr()
signMsg := []struct {
msg msg.Msg
keyManager keys.KeyManager
AccountNumber int64
sequence int64
} {
{msg.CreateSendMsg(fromAddr, ctypes.Coins{ctypes.Coin{Denom:"BNB", Amount:600000000000000000}},
[]msg.Transfer{{keyManagerP1.GetAddr(), ctypes.Coins{ctypes.Coin{ Denom:"BNB", Amount:600000000000000000}}}} ),
test1KeyManger, 27718, 1,
},
}

for _, c := range signMsg {
signMsg := tx.StdSignMsg{
ChainID: "Binance-Chain-Tigris",
AccountNumber: c.AccountNumber,
Sequence: c.sequence,
Memo: "memo",
Msgs: []msg.Msg{c.msg},
Source: 0,
}
fmt.Println("signMsg = ", signMsg)
signResult, _ := test1KeyManger.Sign(signMsg)

fmt.Println("signResult = ", signResult)
}

### Attachment:

Binance documentation: https://docs.binance.org
Github address: https://github.com/binance-chain
Browser address: https://explorer.binance.org/
