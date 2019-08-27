# COSMOS 主链钱包开发

## 一. 什么是 COSMOS

严格来说，COSMOS 是一个独立并行区块链的分散网络，每个区块链都由BFT共识算法（如Tendermint共识）提供支持。

换句话说，COSMOS 是一个新的区块链生态系统，可以相互扩展和互操作。 在 COSMOS 之前，区块链是孤立的，无法相互沟通。 它们很难构建，每秒只能处理少量事务。 COSMOS 通过新的技术愿景解决了这些问题。

## 二. COSMOS 相关资料

## 三. COSMOS 节点搭建

## 四. COSMOS 钱包开发

### 1.生成地址和私钥

#### 1.1. python 生成地址和私钥

    import hashlib
    from secp256k1 import PrivateKey
    import bech32

    def generate_wallet():
        privkey = PrivateKey().serialize()
        return {
            "private_key": privkey,
            "public_key": privkey_to_pubkey(privkey),
            "address": privkey_to_address(privkey),
        }

    def privkey_to_pubkey(privkey: str) -> str:
        privkey_obj = PrivateKey(bytes.fromhex(privkey))
        return privkey_obj.pubkey.serialize().hex()

    def pubkey_to_address(pubkey: str) -> str:
        pubkey_bytes = bytes.fromhex(pubkey)
        s = hashlib.new("sha256", pubkey_bytes).digest()
        r = hashlib.new("ripemd160", s).digest()
        return bech32.bech32_encode("cosmos", bech32.convertbits(r, 8, 5))

    def privkey_to_address(privkey: str) -> str:
        pubkey = privkey_to_pubkey(privkey)
        return pubkey_to_address(pubkey)

    wallet = generate_wallet()
    print(wallet)
    
执行结果如下：

      {'private_key': 'bfbc2e98d50325b8783bb8c2188d1a92aa7d0fbec9feec304a9eb887115c354f', 'public_key': '020a6be4ed72a3317bc8d148a2604a2b31c2d2c07405cacfcd175b68b9445ce42e', 'address': 'cosmos1zmja29jn8cqcf59yy968ze3tdet2zyl6gxh4e8'}


#### 1.2. python 交易签名

    import base64
    import json
    from secp256k1 import PrivateKey
    from address import privkey_to_address, privkey_to_pubkey

    class Transaction:
        def __init__(self, *, privkey: str, account_num:int, sequence:int, fee:int, gas:int, memo:str = "", chain_id: str = "cosmoshub-2",sync_mode = "sync"):
            self.privkey = privkey
            self.account_num = account_num
            self.sequence = sequence
            self.fee = fee
            self.gas = gas
            self.memo = memo
            self.chain_id = chain_id
            self.sync_mode = sync_mode
            self.msgs = []
        def add_atom_transfer(self, recipient: str, amount: int) -> None:
            self.msgs.append(
                {
                    "type": "cosmos-sdk/MsgSend",
                    "value": {
                        "from_address": privkey_to_address(self.privkey),
                        "to_address": recipient,
                        "amount": [{"denom": "uatom", "amount": str(amount)}],
                    },
                }
            )


        def _get_sign_message(self):
            return {
                "chain_id": self.chain_id,
                "account_number": str(self.account_num),
                "fee": {"gas": str(self.gas), "amount": [{"amount": str(self.fee), "denom": "uatom"}]},
                "memo": self.memo,
                "sequence": str(self.sequence),
                "msgs": self.msgs,
            }

        def _sign(self) -> str:
            message_str = json.dumps(self._get_sign_message(), separators=(",", ":"), sort_keys=True)
            message_bytes = message_str.encode("utf-8")
            privkey = PrivateKey(bytes.fromhex(self.privkey))
            signature = privkey.ecdsa_sign(message_bytes)
            signature_compact = privkey.ecdsa_serialize_compact(signature)
            signature_base64_str = base64.b64encode(signature_compact).decode("utf-8")
            return signature_base64_str

        def get_pushable_tx(self) -> str:
            pubkey = privkey_to_pubkey(self.privkey)
            base64_pubkey = base64.b64encode(bytes.fromhex(pubkey)).decode("utf-8")
            pushable_tx = {
                "tx": {
                    "msg": self.msgs,
                    "fee": {
                        "gas": str(self.gas),
                        "amount": [{"denom": "uatom", "amount": str(self.fee)}],
                    },
                    "memo": self.memo,
                    "signatures": [
                        {
                            "signature": self._sign(),
                            "pub_key": {"type": "tendermint/PubKeySecp256k1", "value": base64_pubkey},
                            "account_number": str(self.account_num),
                            "sequence": str(self.sequence),
                        }
                    ],
                },
                "mode": self.sync_mode,
            }
            return json.dumps(pushable_tx, separators=(",", ":"))


    tx = Transaction(
        privkey="26d167d549a4b2b66f766b0d3f2bdbe1cd92708818c338ff453abde316a2bd59",
        account_num=11335,
        sequence=0,
        fee=1000,
        gas=37000,
        memo="",
        chain_id="cosmoshub-2",
        sync_mode="sync",
    )

    tx.add_atom_transfer(recipient="cosmos103l758ps7403sd9c0y8j6hrfw4xyl70j4mmwkf", amount=387000)
    pushable_tx = tx.get_pushable_tx()
    print(pushable_tx)
    
执行结果如下：

    {"tx":{"msg":[{"type":"cosmos-sdk/MsgSend","value":{"from_address":"cosmos1lgharzgds89lpshr7q8kcmd2esnxkfpwvuz5tr","to_address":"cosmos103l758ps7403sd9c0y8j6hrfw4xyl70j4mmwkf","amount":[{"denom":"uatom","amount":"387000"}]}}],"fee":{"gas":"37000","amount":[{"denom":"uatom","amount":"1000"}]},"memo":"","signatures":[{"signature":"chbQMmrg18ZQSt3q3HzW8S8pMyGs/TP/WIbbCyKFd5IiReUY/xJB2yRDEtF92yYBjxEU02z9JNE7VCQmmxWdQw==","pub_key":{"type":"tendermint/PubKeySecp256k1","value":"A49sjCd3Eul+ZXyof7qO460UaO73otrmySHyTNSLW+Xn"},"account_number":"11335","sequence":"0"}]},"mode":"sync"}


#### 1.3. Node 生成地址和私钥

#### 1.4. NodeJs 交易签名
