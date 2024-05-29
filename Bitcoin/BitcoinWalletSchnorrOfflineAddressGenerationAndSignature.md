# Bitcoin 钱包 Schnorr 离线地址生成与签名

# **1. Schnorr 签名**

Schnorr 签名是一种数字签名方案，以其简洁性、高效性和安全性著称，并作为比特币的 Taproot 升级的一部分被采用。以下是 Schnorr 签名的详细流程和特点。

## 1.1. 签名流程

Schnorr 签名算法由以下步骤组成：

**密钥生成**

- 私钥 𝑘*k* 是一个随机选择的整数，范围为 [1,𝑛−1]，其中 𝑛 是椭圆曲线的阶。
- 公钥 𝑃*P* 是通过将私钥乘以椭圆曲线的基点 𝐺*G* 得到的，即 𝑃=𝑘⋅𝐺。



**签名生成**

假设消息为 𝑚。

- 生成随机数 𝑟：选择一个随机数 𝑟*r* 作为临时私钥，范围为 [1,𝑛−1]。
- 计算非交互式挑战：计算 𝑅=𝑟⋅𝐺，其中 𝐺 是椭圆曲线的基点。
- 计算哈希值：计算哈希值 𝑒：

𝑒=H(𝑅∥𝑃∥𝑚)

- 其中 H 是一个哈希函数，如 SHA-256，𝑅∥𝑃∥𝑚 表示 𝑅、公钥 𝑃 和消息 𝑚 的连接。
- 计算签名：计算签名 𝑠：

𝑠=𝑟+𝑒⋅𝑘(mod𝑛)

- 其中 𝑘 是私钥，𝑒 是哈希值，𝑛*n*是椭圆曲线的阶。

签名由 (𝑅,𝑠)组成。



**签名验证**

- 计算哈希值：重新计算哈希值 𝑒：

𝑒=H(𝑅∥𝑃∥𝑚)

- 验证等式：验证以下等式是否成立：

𝑠⋅𝐺=𝑅+𝑒⋅𝑃

- 如果等式成立，签名是有效的；否则无效。

  

## 1.2.签名特点

**简洁性**

- Schnorr 签名算法结构简单，签名过程和验证过程都较为直观。这种简洁性有助于实现和分析安全性。



**安全性**

- 不可伪造：基于离散对数问题的困难性，Schnorr 签名在当前已知的计算能力下被认为是安全的。
- 欧氏证明：Schnorr 签名有严格的欧氏安全性证明。



**高效性**

- 计算效率：签名生成和验证的计算效率较高。尤其在批量签名验证中，Schnorr 签名具有显著的性能优势。
- 签名长度：Schnorr 签名的长度较短，相比于 ECDSA 签名，Schnorr 签名占用的存储空间更少。



**扩展性和兼容性**

Schnorr 签名支持多种扩展功能，如多重签名（MuSig）和聚合签名。这些扩展功能在增强隐私性和可扩展性方面表现出色。

- 多重签名（MuSig）：Schnorr 签名允许多个签名者的公钥和签名聚合为单一的公钥和签名，这显著提高了多重签名的效率和隐私性。
- 兼容性：Schnorr 签名与现有的椭圆曲线加密标准兼容，便于在现有系统中实现和部署。



## 1.3. 签名算法 Python 代码实现

**SchnorrSignObj 类**

```python
import hashlib
from ecdsa import SECP256k1, SigningKey, VerifyingKey
from ecdsa.curves import Curve


class SchnorrSignObj:
    curve: Curve
    def __init__(self):
        # 椭圆曲线参数
        self.curve = SECP256k1

    def schnorr_sign(self, private_key, message):
        # 生成私钥和公钥
        signing_key = SigningKey.from_string(private_key, curve=self.curve)
        verifying_key = signing_key.get_verifying_key()

        # 生成随机数 r
        r = SigningKey.generate(curve=self.curve).privkey.secret_multiplier
        R = VerifyingKey.from_public_point(r * self.curve.generator, curve=self.curve)

        # 计算哈希 e
        e = hashlib.sha256(R.to_string() + verifying_key.to_string() + message).digest()
        e = int.from_bytes(e, 'big')

        # 计算签名 s
        s = (r + e * signing_key.privkey.secret_multiplier) % self.curve.order
        return R.to_string(), s.to_bytes(32, 'big')

    def schnorr_verify(self, public_key, message, signature):
        # 解析签名
        R = VerifyingKey.from_string(signature[0], curve=self.curve)
        s = int.from_bytes(signature[1], 'big')

        # 计算哈希 e
        e = hashlib.sha256(signature[0] + public_key.to_string() + message).digest()
        e = int.from_bytes(e, 'big')

        # 验证签名
        sG = VerifyingKey.from_public_point(s * self.curve.generator, curve=self.curve)
        ReP = VerifyingKey.from_public_point(R.pubkey.point + e * public_key.pubkey.point, curve=self.curve)
        return sG.to_string() == ReP.to_string()
```



**测试代码**

```python
import schnorr
from ecdsa import SECP256k1, SigningKey, VerifyingKey

# 初始化类
schnorr_test = schnorr.SchnorrSignObj()

# 产生密钥并对交易签名
private_key = SigningKey.generate(curve=SECP256k1).to_string()
message = b"Hello, Schnorr!"
signature = schnorr_test.schnorr_sign(private_key, message)
print("Signature:", signature)

# 验证交易签名
public_key = VerifyingKey.from_string(SigningKey.from_string(private_key, curve=SECP256k1).get_verifying_key().to_string(), curve=SECP256k1)
is_valid = schnorr_test.schnorr_verify(public_key, message, signature)

print("Signature valid:", is_valid)
```

## 2. BIP86 协议

**2.1 结构路径** BIP86 是一种专门为生成 Taproot 地址的路径标准，简化并优化了生成单密钥 Taproot 地址的过程。BIP86 的路径结构如下：

```
m / 86' / coin_type' / account' / change / address_index
```

- Purpose: 固定为 86'，表示遵循 BIP86 标准。
- Coin Type: 定义特定加密货币的类型，如 0' 表示比特币。
- Account: 账户索引，允许钱包管理多个独立账户。
- Change: 0 表示外部链（收款地址），1 表示内部链（找零地址）。
- Address Index: 用于生成具体地址的索引。



**2.2.地址生成方法**

BIP86 使用新的路径结构生成 Taproot 地址，通过椭圆曲线算法派生公钥，并将其编码为 Bech32 格式的地址。生成的 Taproot 地址具有更高的隐私性、安全性和扩展性。



**2.3.BIP86 的优势**

- 简洁性：BIP86 提供了一种简洁而直观的路径结构，用于生成 Taproot 地址。
- 安全性：BIP86 使用椭圆曲线算法生成公钥，具有与比特币的现有地址生成方法相当的安全性。
- 可扩展性：BIP86 支持不同的币种和账户，具有良好的可扩展性。

## 3. BIP44 与 BIP86 的区别与联系

**3.1.用途上的区别**

- BIP44: 通用的多币种、多账户管理，适用于生成多种类型的地址（P2PKH, P2SH, P2WPKH）。
- BIP86: 专门为生成单密钥 Taproot 地址设计，简化了路径和使用场景。



**3.2. 路径结构区别**

- BIP44: m/44'/coin_type'/account'/change/address_index，包含多币种、多账户和多地址类型。
- BIP86: m/86'/coin_type'/account'/change/address_index，专注于单密钥 Taproot 地址生成。



**3.3.复杂性区别**

- BIP44: 适用于更复杂的多币种和多账户需求，路径层次更丰富。
- BIP86: 专注于单一用途（Taproot 地址），路径层次简化。



**3.4.标准化区别**

- BIP44: 广泛应用于各种钱包和加密货币管理系统，已经成为生成确定性钱包路径的标准。
- BIP86: 主要用于比特币的 Taproot 地址生成，随着 Taproot 的采用而变得更加流行。



**3.5.总结**

- BIP44 适用于多种类型地址生成，适合更复杂的钱包管理需求。
- BIP86 专注于生成比特币的 Taproot 地址，路径简化，专门为 Taproot 设计。

## 4.Taproot 为什么需要 BIP86

**4.1. 专注于单密钥 Taproot 地址** BIP86 专门设计用于生成单密钥 Taproot 地址。Taproot 是比特币的一项重要升级，旨在提高隐私性、可扩展性和智能合约功能。



**4.2.符合新标准和优化** BIP86 引入了一些新的优化和改进，以支持比特币协议的新特性，如 Taproot 和 Schnorr 签名。Taproot 和 Schnorr 签名提供了更高的安全性和更好的隐私特性。BIP86 的设计是为了最大限度地利用这些新特性。



**4.3.专注于比特币和未来扩展**

BIP44 是一个通用的多币种和多账户路径标准，设计用于管理各种加密货币的地址。这种通用性虽然提供了很大的灵活性，但在特定的优化和新特性支持上可能不如专门设计的标准有效。BIP86 专门为比特币设计，更好地支持比特币的未来扩展和改进。



**4.4.简化路径和提高效率**

BIP86 路径简化，专注于单一用途，使其更适合于生成和管理比特币的 Taproot 地址。这种简化可以减少实现和使用中的复杂性，提高效率和安全性。



**4.5.避免混淆和错误**

BIP86 的专用路径减少了使用过程中可能出现的混淆和错误。由于它专门设计用于比特币的 Taproot 地址，开发者和用户不必在多个标准之间切换，减少了出错的可能性。



**4.6.代码比较**

BIP44 代码示范

```python
def create_address(mnemonic):
    seed = bip39.phrase_to_seed(mnemonic)
    bip32_root_key = BIP32Key.fromEntropy(seed)
    path = "m/44'/0'/0'/0/0"
    key = bip32_root_key
    for level in path.split('/')[1:]:
        if level.endswith("'"):
            index = BIP32_HARDEN + int(level[:-1])
        else:
            index = int(level)
        key = key.ChildKey(index)
    address = key.Address()
    return address
```



BIP86 代码示例

```python
import bip39
import bech32
from bip32 import BIP32, HARDENED_INDEX
from hashlib import sha256
from ecdsa import SECP256k1, SigningKey

def generate_taproot_address(mnemonic):
    seed = bip39.phrase_to_seed(mnemonic)
    bip32 = BIP32.from_seed(seed)
    path = "m/86'/0'/0'/0/0"
    child_key = bip32.get_privkey_from_path(path)
    private_key = SigningKey.from_string(child_key, curve=SECP256k1)
    public_key = private_key.get_verifying_key().to_string("compressed")
    tweak = sha256(b'TapTweak' + public_key[1:]).digest()
    tweaked_pubkey = bytearray(public_key)
    for i in range(32):
        tweaked_pubkey[1 + i] ^= tweak[i]
    witver = 1  # witness version for Taproot
    witprog = tweaked_pubkey[1:33]
    address = bech32.encode('bc', witver, witprog)
    return address
```



# 5. Taproot 格式地址离线生成

```python
import bip39
import bech32
from bip32 import BIP32, HARDENED_INDEX
from hashlib import sha256
from ecdsa import SECP256k1, SigningKey

def generate_taproot_address(mnemonic):
    seed = bip39.phrase_to_seed(mnemonic)
    bip32 = BIP32.from_seed(seed)
    path = "m/86'/0'/0'/0/0"
    child_key = bip32.get_privkey_from_path(path)
    private_key = SigningKey.from_string(child_key, curve=SECP256k1)
    public_key = private_key.get_verifying_key().to_string("compressed")
    tweak = sha256(b'TapTweak' + public_key[1:]).digest()
    tweaked_pubkey = bytearray(public_key)
    for i in range(32):
        tweaked_pubkey[1 + i] ^= tweak[i]
    witver = 1  # witness version for Taproot
    witprog = tweaked_pubkey[1:33]
    address = bech32.encode('bc', witver, witprog)
    return address
```



# 6. 离线签名

- 构建交易: 创建并填充交易输入和输出。
- 生成交易摘要: 使用交易数据生成 Taproot 交易摘要。
- 签名交易: 使用私钥对交易摘要进行签名。
- 构建完整交易: 将签名附加到交易中并生成最终的交易。

```python
def buildAndsignTx(input_list, output_list, private_key, taproot_pubkey):
    tx = Transaction()
    for input in input_list:
        tx.add_input(input['previous_txid'], input['index'])
    for output in output_list:
        tx.add_output(output["address"], output["value"])
    tx.version = 2
    tx.locktime = 0
    sighash = tx.signature_hash()
    
    tweaked_privkey = taproot_tweak_seckey(private_key, taproot_pubkey)
    signature = schnorr.sign(tweaked_privkey, sighash)
    
    witness = TaprootWitness(taproot_pubkey, signature)
    tx.inputs[0].witness = witness

    signed_tx = tx.serialize()   
     
    return signed_tx
```
