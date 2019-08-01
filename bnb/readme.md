## BNB 钱包开发

### 一.币安主链介绍

Binance Chain是Binance及其社区开发的区块链软件系统。 Binance DEX指的是在Binance Chain之上开发的去中心化交易所。币安的目标是创建一个以去中心化的方式发布和交换数字资产的替代市场。币安链的底层技术是基于目前比较火的项目 COSMOS 开发的，关于 COSMOS，在后面的课程中我们将会详细介绍。币安链网络由去中心化节点组成，在币安链上几乎可以即时完成交易，出块时间只需1秒。其速度高于其他区块链。关于币安链的介绍，这里不多说了，网上有很多资料。

### 二.币安主链节点搭建

#### 1. 搭建全节点

Binance Chain的全节点是见证，它观察到共识消息，从数据种子节点下载块并执行业务逻辑以实现作为验证器节点（和其他完整节点）的一致状态。完整节点还通过接受来自其他节点的事务，然后将它们中继到核心Binance网络来帮助网络。

支持的平台：Mac OS X, Windows 和 Linux.

系统需求，计算机硬件必须满足以下节点要求：

* 运行最新版本的Mac OS X，Windows或Linux的台式机或笔记本电脑硬件。
* 500 GB的可用磁盘空间，最低读/写速度为100 MB / s。
* 4核CPU和8千兆字节的内存（RAM）。
* 宽带互联网连接，上传/下载速度至少为每秒1兆字节
* 完整节点必须每24小时运行至少4小时才能赶上Binance Chain更多时间会更好，连续运行您的节点以获得最佳效果。

##### 搭建完整节点

1.安装 git lfs

Git大文件存储（LFS）用Git中的文本指针替换大型文件，如音频样本，视频，数据集和图形，同时将文件内容存储在远程服务器（如GitHub.com或GitHub Enterprise）上。

请到这个界面去下载安装lfs：`https://git-lfs.github.com/`

2.使用 lfs 下载二进制文件

    git lfs clone https://github.com/binance-chain/node-binary.git
    
最新版本的完整节点版本的信息:

    https://github.com/binance-chain/node-binary/blob/master/fullnode/Changelog.md

根据您要加入的网络转到目录。
在以下命令中使用testnet或prod替换网络变量：

    cd node-binary/fullnode/{network}/{version}
    
3.初始 home 目录

首先，您需要为`Binance Chain`选择一个主文件夹`$BNCHOME`（即`〜/.bnbchaind`）。您可以通过以下方式设置：

将`app.toml，config.toml`和`genesis.json`从`node-binary/fullnode/{network}/{version}/config /`放入`$ BNCHOME/config`

4.添加种子节点

要使整个节点起作用，它必须连接到一个或多个已知节点才能加入Binance Chain。

有几个着名的种子节点在网络中向新加入的完整节点提供已知节点地址。

它们已经在`node-binary/fullnode/{network}/{version}/config/config.toml`文件中。

您还可以通过一个简单的python脚本获取种子信息（根据您使用的网络替换域名）：

    import requests, json
    d = requests.get('https://dex.binance.org/api/v1/peers').text # replace dex.binance.org with testnet-dex.binance.org for testnet
    l = json.loads(d)
    seeds = ",".join([ (seed["id"]+"@"+seed["original_listen_addr"]) for seed in l if seed["accelerated"] == False])
    print (seeds)
    
如果您想添加种子节点，请随时使用之前请求中返回的种子节点信息编辑`$BNCHOME/config/config.yaml`的字段种子。

5. 额外的配置

* 日志：日志文件位于`home`-启动`bnbchaind`时指定的目录。
* 最新的日志文件是`bnc.log`。该过程将每隔一小时创建一个新的日志文件。
* 为确保您有足够的磁盘空间来保留日志文件，我们强烈建议您通过更改`$BNCHOME/config/app.toml`中的`logFileRoot`选项来更改日志位置。
* 服务端口：`RPC`服务侦听端口`27147`，`P2P`服务默认侦听端口`27146`。
* 除非完整节点必须侦听其他端口，否则请确保在启动完整节点之前打开这两个端口。
* 存储：所有状态和块数据将存储在`$BNCHOME/data`下，不要删除或编辑任何这些文件。

6.启动节点

根据平台启动整个节点。在以下命令中用mac windows或linux替换platform变量：

    ./{{platform}}/bnbchaind start --home $BNCHOME --pruning breathe &

只有在赶上Binance Chain之后，整个节点才能正确处理请求。

7.同步数据

您可以通过两种方式与区块链网络中的其他对等方同步：快速同步状态同步。

这两种方法可以一起使用。

快速同步数据：

* 与其他数据种子节点同步的默认方式是快速同步。
* 在快速同步中，您需要从同级中下载所有块并在每个块中执行所有事务。
* 同步速度约为20块/秒，比状态同步慢。

配置位于`$BNCHOME/config/config.toml`中：

    fast_sync必须设置true
    state_sync_reactor 可以设置为 false或者true
    state_sync 可以设置为 false或者true

状态同步：

状态同步将使整个节点的应用程序状态保持最新而不下载所有块。同步速度比快速同步快。但是，您需要为整个节点分配超过16 GB的内存才能使此功能正常工作。

配置位于 `$BNCHOME/config/config.toml`中：

     state_sync_reactor必须设置为true
     state_sync 必须设置为true
     recv_rate必须设置到102428800
     ping_interval 建议设置到10m30s
     pong_timeout建议设置到450s

状态同步可以在短时间内帮助fullnode与其他对等体处于相同状态（根据我们的测试，binance链testnet中的一个月~800M数据库快照可以在大约45分钟内同步），以便您可以接收最新的块/事务和查询 订单簿，帐户余额等的最新状态。但状态同步不会在状态同步高度之前下载历史块，如果您启动具有状态同步的节点并且它在高度10000处同步，则您的本地数据库将仅在高度10000之后具有块。

如果已经启动了完整节点，建议的方法是在启用状态同步之前删除（备份后）`$BNCHOME/data`目录和`$BNCHOME/config/priv_validator_key.json`。

8.监控同步过程

您可以多次验证`curl localhost：27147/status`是否完成状态同步，并查看`latest_block_height`是否响应增加。

     "sync_info": {
       ...
       "latest_block_height": "878092",
       "latest_block_time": "2019-04-15T00:01:22.610803768Z",
       ...
     }

如果状态同步未成功，请在下次启动完整节点之前重复删除`$BNCHOME/data`目录和`$BNCHOME/config/priv_validator_key.json`，以防数据不一致。

一旦状态同步成功，稍后的全节点重启将不再进行状态同步（如果本地块不连续）。

但是如果你确实想要再次进行状态同步（不要在意上一次停止和最新状态同步快照之间有丢失的块）并且你想保留已经同步的块，你可以删除`$BNCHOME/data/STATESYNC.LOCK`。

例如，您在1月1日开始您的完整节点，状态同步在高度10000，并在一段时间后关闭它在2月10日的高度22000。

现在它的3月1日，最新的可同步块高度为50000，你不关心22000和50000之间的块，你可以在启动你的节点之前删除`$BNCHOME/data/STATESYNC.LOCK`。

然后，整个节点将从高度50000继续状态同步。

关闭state_sync_reactor和state_sync可以在成功状态同步后保存内存。

8.更新全节点

在大多数情况下，请下载新的二进制文件并替换它，然后重新启动整个节点。在特殊情况下，您可能必须为不兼容的版本（硬分叉）执行额外的步骤。

9.监控

默认情况下，Prometheus在端口28660上启用，端点为`/metrics`。


#### 2.搭建轻节点

Light客户端是一个程序，它连接到一个完整的节点，以帮助用户以安全和分散的方式访问Binance链并与之交互，而无需同步完整的区块链。

##### 轻客户端与完整节点

* Light客户端不存储块或状态，这样它需要更少的磁盘空间（50兆就足够了）。
* Light客户端不加入p2p网络，并且在空闲时不会产生任何网络成本。网络开销取决于轻客户端同时处理多少请求。
* 轻客户端不重放链的状态，因此空闲时没有CPU成本。 CPU成本还取决于轻客户端同时处理的请求数量。
* Light客户端比完整节点更快，即使它落后于核心网络几个月。只需几秒钟即可赶上核心网络。

##### 平台和系统需求

平台：

* 支持在Mac OS X，Windows和Linux上运行轻量级客户端节点。
* 轻客户端很快就会开源，之后您可以交叉编译轻客户端二进制文件并在其他平台上运行它。

要求：

* 50兆字节的可用磁盘空间。
* 2个CPU核心，50兆字节的内存（RAM）。

##### 运行一个轻客户端

1.下载节点

    git clone https://github.com/binance-chain/node-binary.git

根据您要加入的网络转到目录。在以下命令中使用testnet或prod替换网络变量：

    cd node-binary/lightd/{network}/{version}

帮助信息

        ./lightd --help
        This node will run a secure proxy to a binance rpc server.

        All calls that can be tracked back to a block header by a proof
        will be verified before passing them back to the caller. Other that
        that it will present the same interface as a full binance node,
        just with added trust and running locally.

        Usage:
          lite [flags]

        Flags:
              --cache-size int             Specify the memory trust store cache size (default 10)
              --chain-id string            Specify the binance chain ID (default "bnbchain")
          -h, --help                       help for lite
              --home-dir string            Specify the home directory (default ".binance-lite")
              --laddr string               Serve the proxy on the given address (default "tcp://localhost:27147")
              --max-open-connections int   Maximum number of simultaneous connections (including WebSocket). (default 900)
              --node string                Connect to a binance node at this address (default "tcp://localhost:27147")
您可以指定上面的所有参数。

根据平台启动轻客户端节点。 在以下命令中用mac，windows或linux替换platform变量：

        ./{{platform}}/lightd --chain-id "{chain-id}" --node tcp://{full node addr}:80 > node.log  &

* 启动轻客户端节点有两个必需参数：chain id和full node addr。
* 要加入的网络的链ID。
* 您可以在测试网络中的genesis文件或prod网络中的genesis文件中找到链ID。
* 完整节点地址字段可以是您已部署的任何完整节点的地址。
* 您可以参考运行Binance Chain完整节点以获取更多详细信息。

我们提供了一堆完整的节点，您可以连接到mainnet和testnet。你可以通过一个简单的python脚本获取完整的节点信息（根据不同的网络注意替换域）：

    import requests, json
    d = requests.get('https://dex.binance.org/api/v1/peers').text # replace dex.binance.org with testnet-dex.binance.org for testnet
    l = json.loads(d)
    seeds = ",".join([ (seed["id"]+"@"+seed["original_listen_addr"]) for seed in l if seed["accelerated"] == False])
    print (seeds)
    
主网示例：

    ./lightd --chain-id "Binance-Chain-Tigris" --node tcp://dataseed1.binance.org:80 > node.log  &


测试网示例：
    
    ./lightd --chain-id "Binance-Chain-Nile" --node tcp://data-seed-pre-0-s1.binance.org:80 > node.log  &


### 三.币安 go-sdk详细介绍

go-sdk是币安开源的可以和币安节点通信的代码库，除了创建和提交不同的事务外，Binance Chain GO SDK还为BNC Chain API提供了一个瘦包装，用于只读端点。 它包括以下核心组件：

* 客户端-Binance Chain事务类型和查询的实现，例如传输和交易。
* 通用-核心加密函数，uuid函数和其他有用的函数。
* e2e-go-sdk开发人员的端到端测试包。对于普通用户，它也是使用go-sdk的一个很好的参考。
* 密钥-实施KeyManage来管理私钥和帐户。
* types-Binance Chain的核心类型，例如coin，account，tx和msg。

#### 1.安装

要求：go 11 以上版本

推荐使用 go mod

添加 `github.com/binance-chain/go-sdk` 到 go mod 文件

    require (
	    github.com/binance-chain/go-sdk latest
    )
    replace github.com/tendermint/go-amino => github.com/binance-chain/bnc-go-amino v0.14.1-binance.1

#### 2.go-sdk 的使用

##### Keymanager

在开始使用API之前，您应该构建一个密钥管理器来帮助签署事务消息或验证签名。 密钥管理器是一个身份管理器，用于定义您在bnbchain中的身份。 它提供以下接口：

    type KeyManager interface {
        Sign(tx.StdSignMsg) ([]byte, error)
        GetPrivKey() crypto.PrivKey
        GetAddr() txmsg.AccAddress

        ExportAsMnemonic() (string, error)
        ExportAsPrivateKey() (string, error)
        ExportAsKeyStore(password string) (*EncryptedKeyJSON, error)
    }
    
四个构造函数来生成密钥管理器：

    NewKeyManager() (KeyManager, error)

    NewMnemonicKeyManager(mnemonic string) (KeyManager, error)

    NewKeyStoreKeyManager(file string, auth string) (KeyManager, error)

    NewPrivateKeyManager(priKey string) (KeyManager, error) 

    NewLedgerKeyManager(path ledger.DerivationPath) (KeyManager, error)

* NewKeyManager。您将获得一个新的私钥而不提供任何东西，您可以导出并保存此KeyManager。
* NewMnemonicKeyManager。你应该提供你的助记符，通常是一个24字的字符串。
* NewKeyStoreKeyManager。您应该提供密钥库json文件和密码，您可以在创建钱包帐户时下载密钥库json文件。
* NewPrivateKeyManager。您应该提供私钥的十六进制编码字符串。
* NewLedgerKeyManager。您必须拥有带有binance分类帐应用程序的分类帐设备并将其连接到您的机器。

示例代码：

通过助记词：

	mnemonic := "lock globe panda armed mandate fabric couple dove climb step stove price recall decrease fire sail ring media enhance excite deny valid ceiling arm"
	keyManager, _ := keys.NewMnemonicKeyManager(mnemonic)

通过keystore文件：

	file := "testkeystore.json"
	keyManager, err := NewKeyStoreKeyManager(file, "your password")
	
通过私钥产生：

	priv := "9579fff0cab07a4379e845a890105004ba4c8276f8ad9d22082b2acbf02d884b"
	keyManager, err := NewPrivateKeyManager(priv)
	
通过 ledger 设备：

	bip44Params := keys.NewBinanceBIP44Params(0, 0)
	keyManager, err := NewLedgerKeyManager(bip44Params.DerivationPath())

我们为持久的密钥管理器提供了三个导出功能：

	ExportAsMnemonic() (string, error)
	ExportAsPrivateKey() (string, error)
	ExportAsKeyStore(password string) (*EncryptedKeyJSON, error)

示例代码：

	km, _ := NewKeyManager()
	encryPlain1, _ := km.GetPrivKey().Sign([]byte("test plain"))
	keyJSONV1, err := km.ExportAsKeyStore("testpassword")
	bz, _ := json.Marshal(keyJSONV1)
	ioutil.WriteFile("TestGenerateKeyStoreNoError.json", bz, 0660)
	newkm, _ := NewKeyStoreKeyManager("TestGenerateKeyStoreNoError.json", "testpassword")
	encryPlain2, _ := newkm.GetPrivKey().Sign([]byte("test plain"))
	assert.True(t, bytes.Equal(encryPlain1, encryPlain2))
	
对于 Ledger 密钥，它无法导出。 因为它的私钥保存在 Ledger 设备上，没有人可以直接在外面访问它。	

##### 初始化客户端

	import sdk "github.com/binance-chain/go-sdk/client"

	mnemonic := "lock globe panda armed mandate fabric couple dove climb step stove price recall decrease fire sail ring media enhance excite deny valid ceiling arm"
	//-----   Init KeyManager  -------------
	keyManager, _ := keys.NewMnemonicKeyManager(mnemonic)

	//-----   Init sdk  -------------
	client, err := sdk.NewDexClient("testnet-dex.binance.org", types.TestNetwork, keyManager)

对于sdk init，你应该知api地址。 此外，您应该知道api网关所处的网络类型，测试网络和生产网络有不同的配置。

测试网络：testnet-dex.binance.org
生产网络：dex.binance.org

如果您想广播某些交易，例如发送硬币，创建订单或取消订单，您应该构建一个密钥管理器。

创建一个购买订单示例：

	createOrderResult, err := client.CreateOrder(tradeSymbol, nativeSymbol, txmsg.OrderSide.BUY, 100000000, 100000000, true)

##### RPC 客户端

RPC端点可用于直接通过HTTP或websockets与节点交互。 使用RPC，您可以执行低级操作，例如执行ABCI查询，查看网络/一致状态或针对完整节点或轻客户端广播事务。

示例代码：

	nodeAddr := "tcp://127.0.0.1:27147"
	testClientInstance := rpc.NewRPCClient(nodeAddr,types.TestNetwork)
	status, err := c.Status()

#### 3.go-sdk的 API 

##### API

对于读取选项，每个API都需要一个Query参数。 每个Query参数我们提供一个构造函数：NewXXXQuery。 我们建议您在新建Query参数时使用此构造函数，因为构造只需要必需的参数，对于可选参数，您可以使用WithXXX来添加它。


您应该注意到一个最重要的一点，我们使用int64来表示小数。 小数长度固定为8，表示：100000000等于1 150000000等于1.5 1050000000等于10.5

对于常见事务，响应是：

TxCommitResult

	Ok bool，如果交易被链接受。
	Log string，事务的错误消息。
	Hash String
	Code int32，结果代码。零代表罚款。
	Data string，不同类型的事务返回不同的数据消息。

### 四.币安主链钱包开发

#### 1.地址与私钥生成

以下是 go-sdk 生成地址和私钥的代码

	package main

	import (
	   "fmt"
	   "github.com/binance-chain/go-sdk/keys"
	）
	
	func main()  {
	   mnemonic := "bottom quick strong ranch section decide pepper broken oven demand coin run jacket curious business achieve mule bamboo remain vote kid rigid bench rubber"
	   keyManger, _ := keys.NewMnemonicKeyManager(mnemonic)
	   fmt.Println("Address", keyManger.GetAddr())
	   priKey, _ := keyManger.ExportAsPrivateKey()
	   fmt.Println("privateKey",priKey)

	   fmt.Println("=========keystore=========")
	   aa, _ := keyManger.ExportAsKeyStore("111111")
	   fmt.Println("keystore ===", aa)
	   fmt.Println("=========keystore=========")
	}


#### 2.充值

下面是使用 go-sdk 分析区块的代码:

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

#### 3.提现

提下主要就是签名和转账，使用下面代码：

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
		keyManager  keys.KeyManager
		chainId     string
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
			return txMsg
		}
	}

	func WithMemo(memo string) Option {
		return func(txMsg *tx.StdSignMsg) *tx.StdSignMsg {
			txMsg.Memo = memo
			return txMsg
		}
	}

	func WithAcNumAndSequence(accountNum, seq int64) Option {
		return func(txMsg *tx.StdSignMsg) *tx.StdSignMsg {
			txMsg.Sequence = seq
			txMsg.AccountNumber = accountNum
			return txMsg
		}
	}

	func (c *client) broadcastMsg(m msg.Msg, sync bool, options ...Option) (*tx.TxCommitResult, error) {
		// prepare message to sign
		signMsg := &tx.StdSignMsg{
			ChainID:       c.chainId,
			AccountNumber: -1,
			Sequence:      -1,
			Memo:          "",
			Msgs:          []msg.Msg{m},
			Source:        tx.Source,
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

或者：

	func signAndSend()  {
		 test1Mnemonic := "bottom quick strong ranch section decide pepper broken oven demand coin run jacket curious business achieve mule bamboo remain vote kid rigid bench rubber"
		 test1KeyManger, _ := keys.NewMnemonicKeyManager(test1Mnemonic)
		 keyManagerP1, _ := keys.NewPrivateKeyManager("091cf0dfe62c369b27be5ca5ceeecd2f80fa14d5bd04d27dc759ed6ac5f78d55")
		 fromAddr := test1KeyManger.GetAddr()
		 signMsg := []struct {
		  msg         msg.Msg
		  keyManager  keys.KeyManager
		  AccountNumber  int64
		  sequence    int64
		 } {
		  {msg.CreateSendMsg(fromAddr, ctypes.Coins{ctypes.Coin{Denom:"BNB", Amount:600000000000000000}},
		   []msg.Transfer{{keyManagerP1.GetAddr(), ctypes.Coins{ctypes.Coin{ Denom:"BNB", Amount:600000000000000000}}}}  ),
		   test1KeyManger, 27718, 1,
		  },
		 }

		 for _, c := range signMsg {
		  signMsg := tx.StdSignMsg{
		   ChainID:       "Binance-Chain-Tigris",
		   AccountNumber: c.AccountNumber,
		   Sequence:      c.sequence,
		   Memo:          "memo",
		   Msgs:          []msg.Msg{c.msg},
		   Source:        0,
		  }
		  fmt.Println("signMsg  = ", signMsg)
		  signResult, _ := test1KeyManger.Sign(signMsg)

		  fmt.Println("signResult = ", signResult)
	}

### 附：

币安文档：https://docs.binance.org
github地址：https://github.com/binance-chain
浏览器地址：https://explorer.binance.org/


