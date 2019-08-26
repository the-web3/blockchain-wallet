# 第十九章：BNB python-sdk 源码分析

## 一. binance-chain 和  Binance DEX

Binance Chain是Binance及其社区开发的区块链软件系统。 Binance DEX指的是在Binance Chain之上开发的去中心化交易所。

## 二. binance-chain 的 python-sdk 安装与使用

### 1.安装

要求 python 版本为 3.6 以上，安装命令很简单，

    pip install python-binance-chain

如果安装过程中出现问题，请按照 sec256k1-py, 此处提供一种安装 sec256k1-py 失败的解决方案​。

#### 错误1：pkg-config包安装不成功

这个错误是由于pkg-config缺失引起的，手动安装这个包

#### 错误2：secp256k1包安装不成功

安装这个包之前必须安装好这几个东西个，automake，pkg-config，libtool，libffi，gmp。

### 2.使用

#### 2.1 生成钱包私钥，公钥和地址

    from binance_chain.wallet import Wallet
    from binance_chain.environment import BinanceEnvironment

    product_env = BinanceEnvironment.get_production_env()
    wallet = Wallet.create_random_wallet(env=product_env)
    print(wallet.address)
    print(wallet.private_key)
    print(wallet.public_key)

代码比较简单，这里就不再多做解释了，下面看一下执行结果：

    bnb1fx64c76dpew4pqqsrmvdg0l8xe8ud04h44yxsy
    b88d4cc333e02d44a2e2426425d3ec4956e3793ef00a0d77a8c3338f127aa58d
    b'\x02d\r\x84\x1f|\xd9J\x90\x85\xc7\x01P\xb8\xcc0\xca\xb2\x9d\xd3\x11?#\xec\xca\x19\\\xd8@\x8d\x91\xc9q'

上面的结果依次是地址，私钥和经过处理的公钥，申明一下原始的公钥不是这样的

#### 2.2 HttpApiClient 的使用

    from binance_chain.http import HttpApiClient
    from binance_chain.environment import BinanceEnvironment
    testnet_env = BinanceEnvironment.get_production_env()
    client = HttpApiClient(env=testnet_env)
    prod_client = HttpApiClient()

    account = client.get_account('bnb1acecavtwz6s4fat6540a5dz3vcq25lrsccxapp')
    print(account)

    account_seq = client.get_account_sequence('bnb1acecavtwz6s4fat6540a5dz3vcq25lrsccxapp')
    print(account_seq)

    fees = client.get_fees()
    print(fees)

代码中第一个是获取 `bnb1acecavtwz6s4fat6540a5dz3vcq25lrsccxapp`的账户信息，第二个获取`bnb1acecavtwz6s4fat6540a5dz3vcq25lrsccxapp`交易序号，第三个获取费用。

执行结果如下：

    {'account_number': 198155, 'address': 'bnb1acecavtwz6s4fat6540a5dz3vcq25lrsccxapp', 'balances': [{'free': '0.46438600', 'frozen': '0.00000000', 'locked': '0.00000000', 'symbol': 'BNB'}], 'flags': 0, 'public_key': [3, 142, 211, 241, 139, 162, 23, 200, 94, 195, 109, 117, 254, 214, 166, 254, 154, 61, 151, 18, 33, 59, 253, 59, 34, 38, 241, 210, 157, 129, 125, 240, 25], 'sequence': 19}
    
    
    {'sequence': 19}
    
    
    [{'msg_type': 'submit_proposal', 'fee': 500000000, 'fee_for': 1}, {'msg_type': 'deposit', 'fee': 62500, 'fee_for': 1}, {'msg_type': 'vote', 'fee': 0, 'fee_for': 3}, {'msg_type': 'create_validator', 'fee': 1000000000, 'fee_for': 1}, {'msg_type': 'remove_validator', 'fee': 100000000, 'fee_for': 1}, {'msg_type': 'dexList', 'fee': 100000000000, 'fee_for': 2}, {'msg_type': 'orderNew', 'fee': 0, 'fee_for': 3}, {'msg_type': 'orderCancel', 'fee': 0, 'fee_for': 3}, {'msg_type': 'issueMsg', 'fee': 50000000000, 'fee_for': 2}, {'msg_type': 'mintMsg', 'fee': 500000000, 'fee_for': 2}, {'msg_type': 'tokensBurn', 'fee': 50000000, 'fee_for': 1}, {'msg_type': 'tokensFreeze', 'fee': 500000, 'fee_for': 1}, {'fixed_fee_params': {'msg_type': 'send', 'fee': 37500, 'fee_for': 1}, 'multi_transfer_fee': 30000, 'lower_limit_as_multi': 2}, {'dex_fee_fields': [{'fee_name': 'ExpireFee', 'fee_value': 25000}, {'fee_name': 'ExpireFeeNative', 'fee_value': 5000}, {'fee_name': 'CancelFee', 'fee_value': 25000}, {'fee_name': 'CancelFeeNative', 'fee_value': 5000}, {'fee_name': 'FeeRate', 'fee_value': 1000}, {'fee_name': 'FeeRateNative', 'fee_value': 400}, {'fee_name': 'IOCExpireFee', 'fee_value': 10000}, {'fee_name': 'IOCExpireFeeNative', 'fee_value': 2500}]}, {'msg_type': 'timeLock', 'fee': 1000000, 'fee_for': 1}, {'msg_type': 'timeUnlock', 'fee': 1000000, 'fee_for': 1}, {'msg_type': 'timeRelock', 'fee': 1000000, 'fee_for': 1}, {'msg_type': 'setAccountFlags', 'fee': 100000000, 'fee_for': 1}]
    
 上面这些我们不做过多的解释，关于 BNB 钱包开发详细情况请查看作者的另一篇文章
 
 #### 2.3 交易签名的使用

    from binance_chain.messages import TransferMsg, StdTxMsg, Signature
    from binance_chain.wallet import Wallet

    wallet = Wallet('私钥')
    wallet._account_number=198155
    wallet._sequence=18
    wallet._chain_id='Binance-Chain-Tigris'
    transfer_msg = TransferMsg(
        wallet=wallet,
        symbol='BNB',
        amount=0.01,
        to_address='bnb1k3ekzgy2ana55s52ckj5pf02yypj6kcr6rme6f',
        memo='57891114'
    )
    data = transfer_msg.to_hex_data().decode()
    print(data)
 
上面的私钥替换成自己的就行，这是 BNB 转账交易签名，这里简单说一下 BNB 的交易特征，首先签名的时候 account_number 和 sequence 一个是账户号一个是交易序号，BNB 交易是带 memo, 他的交易可以一对一，一对多，多对一，多对多转账，BNB 交易有 Input 和 Output, 但他上层调用没有找零的概念。

 #### 2.4 导入私钥
 
    from binance_chain.wallet import Wallet
    from binance_chain.environment import BinanceEnvironment

    testnet_env = BinanceEnvironment.get_production_env()
    wallet = Wallet('私钥', env=testnet_env)
    print(wallet.address)
    print(wallet.private_key)
    print(wallet.public_key_hex)

代码比较简单，直接看执行结果

    bnb1k3ekzgy2ana55s52ckj5pf02yypj6kcr6rme6f
    882ddb71dc3d2edb54472ecf60d55b8585257639a47b425fdadb223f6698a203
    b'023fce18eda7b4599367b9af64771693ebdc9a2897ab63d50dd4f77586830cdd42'
 
#### 2.5 获取交易并解析交易
 
    import binascii
    from io import BytesIO
    import typing
    import base64
    from binance_chain import messages
    from binance_chain.protobuf import dex_pb2
    from binance_chain.utils import segwit_addr
    from binance_chain.http import HttpApiClient
    from binance_chain.node_rpc.http import HttpRpcClient
    from binance_chain.environment import BinanceEnvironment


    def extract_send_msg_addresses(send_msg: dex_pb2.Send) -> typing.Tuple[str]:
        from_address = segwit_addr.encode('bnb', send_msg.inputs[0].address)
        to_address = segwit_addr.encode('bnb', send_msg.outputs[0].address)
        return from_address, to_address

    def is_bnb_transaction(send_msg: dex_pb2.Send):
        for inp in send_msg.inputs:
            for coin in inp.coins:
                if coin.denom != 'BNB':
                    return False
        for out in send_msg.outputs:
            for coin in out.coins:
                if coin.denom != 'BNB':
                    return False
        return True

    def parse_msg( msg_bytes: bytes) -> dex_pb2.StdTx:
        msg_type = msg_bytes[:4]
        msg_class = amino_msg_type_to_class(msg_type)
        msg_pb2 = msg_class().FromString(msg_bytes[4:])
        return msg_pb2

    def amino_msg_type_to_class(msg_type: bytes):
        return {
                messages.NewOrderMsg.AMINO_MESSAGE_TYPE: dex_pb2.NewOrder,
                messages.CancelOrderMsg.AMINO_MESSAGE_TYPE: dex_pb2.CancelOrder,
                messages.FreezeMsg.AMINO_MESSAGE_TYPE: dex_pb2.TokenFreeze,
                messages.UnFreezeMsg.AMINO_MESSAGE_TYPE: dex_pb2.TokenUnfreeze,
                messages.TransferMsg.AMINO_MESSAGE_TYPE: dex_pb2.Send,
                messages.VoteMsg.AMINO_MESSAGE_TYPE: dex_pb2.Vote,
        }.get(binascii.hexlify(msg_type).upper())


    def parse_stdtx(tx_bytes: bytes) -> dex_pb2.StdTx:
        varint_length = decode_bytes(tx_bytes[:2])
        msg_type = tx_bytes[2:6]
        assert varint_length == len(tx_bytes) - 2
        assert (binascii.hexlify(msg_type).decode('utf-8').lower() ==
                messages.StdTxMsg.AMINO_MESSAGE_TYPE.decode('utf-8').lower())
        stdtx_pb2 = dex_pb2.StdTx().FromString(tx_bytes[6:])
        return stdtx_pb2


    def decode_bytes(buf):
        return decode_stream(BytesIO(buf))

    def decode_stream(stream):
        shift = 0
        result = 0
        while True:
            i = read_one(stream)
            result |= (i & 0x7f) << shift
            shift += 7
            if not (i & 0x80):
                break
        return result

    def read_one(stream):
        c = stream.read(1)
        if c == b'':
            raise EOFError("Unexpected EOF while reading bytes")
        return ord(c)


    httpapiclient = HttpApiClient()
    listen_addr ='https://dataseed3.ninicoin.io/'
    rpc_client = HttpRpcClient(listen_addr)

    h = 30060722
    block = rpc_client.get_block(30060722)
    txs  = block['block']['data']['txs']
    print(txs)
    if txs != None:
        for tx_str in txs:
            tx_bytes = base64.b64decode(tx_str)
            stdtx_pb2 = parse_stdtx(tx_bytes)

            msg = stdtx_pb2.msgs[0]
            msg_pb2 = parse_msg(msg)

            # 只支持转账类型，其他类型直接抛弃
            if type(msg_pb2) != dex_pb2.Send:
                continue

            # 只支持 BNB 转账类型, 代币直接抛弃
            if not is_bnb_transaction(msg_pb2):
                continue

            from_address, to_address = extract_send_msg_addresses(msg_pb2)

            product_env = BinanceEnvironment.get_production_env()
            client = HttpApiClient(env=product_env)

            queried_txs = client.get_transactions(address=from_address, height=h)['tx']
            print(queried_txs)
            tx_dict = list(filter(lambda t: t['toAddr'] == to_address, queried_txs))[0]
            print(tx_dict)

这里我们也不多解释，如果看不懂这个代码，请联系作者买视频教程，下面是执行结果：

第一次打印
        
        ['0QHwYl3uClYWbmgbChS7ldSfPDJKi4D9MXmzzQ49BOWlARILRE9TLTEyMF9CTkIaLUJCOTVENDlGM0MzMjRBOEI4MEZEMzE3OUIzQ0QwRTNEMDRFNUE1MDEtODc5MhJxCibrWumHIQMOKyrvTAm/lmfD6MTtDOY/YOR5N6UWii88fzT7RbikOxJA8PlOxc73ShBQ0tXv6pXTomEDyYGYzXZIvNRF5G0+YxRalvnMurFpaJ3oUJoGFK5Dkcej+o/uIXy3ftG6LUzfkxjnqw8g2EQgAQ==', 'ygHwYl3uCkgqLIf6CiAKFLRzYSCK7PtKQorFpUCl6iEDLVsDEggKA0JOQhDEXhIgChTuM46xbhahVPV6pV/aNFFmAKp8cBIICgNCTkIQxF4ScAom61rphyECP84Y7ae0WZNnua9kdxaT69yaKJerY9UN1Pd1hoMM3UISQCmg1tAoOQPkktIOZ36QuZD5zTrVUbd0FYo/SW7c3NxADqeRhgO46xixNprsW+Fhzf+6/yuc8NmyuLE7gtw679MYubwNIBwaCDQ5NDA5Njgz']

第二次打印


      [{'txHash': 'FEEDE276687D8B98315711A0ACD019241C4BA5F8B6101E496F4B8A0DA52B3FD8', 'blockHeight': 30060722, 'txType': 'TRANSFER', 'timeStamp': '2019-08-26T11:53:26.752Z', 'fromAddr': 'bnb1k3ekzgy2ana55s52ckj5pf02yypj6kcr6rme6f', 'toAddr': 'bnb1acecavtwz6s4fat6540a5dz3vcq25lrsccxapp', 'value': '0.00012100', 'txAsset': 'BNB', 'txFee': '0.00037500', 'proposalId': None, 'txAge': 39626, 'orderId': None, 'code': 0, 'data': None, 'confirmBlocks': 0, 'memo': '49409683', 'source': 0, 'sequence': 28}]


      {'txHash': 'FEEDE276687D8B98315711A0ACD019241C4BA5F8B6101E496F4B8A0DA52B3FD8', 'blockHeight': 30060722, 'txType': 'TRANSFER', 'timeStamp': '2019-08-26T11:53:26.752Z', 'fromAddr': 'bnb1k3ekzgy2ana55s52ckj5pf02yypj6kcr6rme6f', 'toAddr': 'bnb1acecavtwz6s4fat6540a5dz3vcq25lrsccxapp', 'value': '0.00012100', 'txAsset': 'BNB', 'txFee': '0.00037500', 'proposalId': None, 'txAge': 39626, 'orderId': None, 'code': 0, 'data': None, 'confirmBlocks': 0, 'memo': '49409683', 'source': 0, 'sequence': 28}

第二次打印的结果是解析出来的交易

好了，sdk 的使用我们就先讲到这里，接下来我们解释一下源码

### 3.源码解析

#### 3.1 SDK 目录解析

















