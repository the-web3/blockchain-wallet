# HD 钱包助记词生成实战

助记词（Mnemonic Phrase）在加密货币领域，尤其是钱包中，被广泛用于生成和管理私钥。它们是基于 BIP-39 标准生成的。

# 一. 助记词生成

## 1.助记词生成原理

**1.1.随机熵生成** 

首先生成一段随机熵（Entropy）。熵的长度可以是 128 到 256 位，并且是 32 的倍数。常见的熵长度有 128 位（12 个助记词）和 256 位（24 个助记词）。

**1.2.计算校验和** 

对熵进行 SHA-256 哈希计算，并取哈希值的前几位作为校验和。校验和的长度取决于熵的长度。例如，128 位熵需要 4 位校验和（因为 128 / 32 = 4），256 位熵需要 8 位校验和。

**1.3.组合熵和校验** 

将校验和附加到熵的末尾，形成一个新的二进制序列。这个序列的总长度为 (熵的长度 + 校验和的长度)。

**1.4.分割为助记词索引** 

将组合后的二进制序列分割成每组 11 位的片段，每个片段转换为一个数字，这个数字作为助记词列表中的索引。

**1.5.映射为助记词** 

使用这些索引从预定义的 2048 个助记词列表（BIP-39 词库）中提取相应的助记词。这些助记词就是最终的助记词短语。

## 2. 生成过程代码实战

**2.1.NodeJs**

```javascript
const bip39 = require('bip39');
const crypto_ts = require('crypto');

// 1. 生成 128 位随机熵
const entropy = crypto_ts.randomBytes(16); // 128 位是 16 字节

// 2. 计算校验和 (SHA-256)
const hash = crypto_ts.createHash('sha256').update(entropy).digest();
const checksum = hash[0] >> 4; // 取前 4 位

// 3. 组合熵和校验和
let bits = '';
for (let i = 0; i < entropy.length; i++) {
    bits += entropy[i].toString(2).padStart(8, '0');
}
bits += checksum.toString(2).padStart(4, '0');

// 4. 分割为助记词索引
const indices = [];
for (let i = 0; i < bits.length; i += 11) {
    const index = parseInt(bits.slice(i, i + 11), 2);
    indices.push(index);
}

// 5. 映射为助记词
const wordlist = bip39.wordlists.english;
const mnemonic = indices.map(index => wordlist[index]).join(' ');
```

**2.2.python**

```python
def generate_mnemonic():
    # 1. 生成 128 位随机熵 (16 字节)
    entropy = os.urandom(16)

    # 2. 计算校验和 (SHA-256)
    hash_bytes = hashlib.sha256(entropy).digest()
    checksum_bits = bin(hash_bytes[0])[2:].zfill(8)[:4]  # 取前 4 位

    # 3. 组合熵和校验和
    entropy_bits = ''.join([bin(byte)[2:].zfill(8) for byte in entropy])
    combined_bits = entropy_bits + checksum_bits

    # 4. 分割为助记词索引
    indices = [int(combined_bits[i:i + 11], 2) for i in range(0, len(combined_bits), 11)]

    # 5. 映射为助记词
    wordlist = bip39.INDEX_TO_WORD_TABLE
    mnemonic = ' '.join([wordlist[index] for index in indices])

    return mnemonic


# 生成并打印助记词
mnemonic_phrase = generate_mnemonic()
print(f"Generated mnemonic phrase: {mnemonic_phrase}")
```

**2.3.go**

```go
import (
	"crypto/rand"
	"crypto/sha256"
	"errors"
	"fmt"
	"github.com/tyler-smith/go-bip39"
	"strconv"
	"strings"
)

func GenerateEntropy(bitSize int) ([]byte, error) {
	validBitSizes := map[int]bool{128: true, 160: true, 192: true, 224: true, 256: true}
	if !validBitSizes[bitSize] {
		return nil, errors.New("Invalid entropy bit size, should be one of 128, 160, 192, 224, or 256")
	}

	byteSize := bitSize / 8

	entropy := make([]byte, byteSize)

	_, err := rand.Read(entropy)

	if err != nil {
		return nil, err
	}
	return entropy, nil
}

func CalculateCheckBits(entropy []byte) uint8 {
	// 计算熵的长度（bit数）
	ENT := len(entropy) * 8
	// 计算校验和位数（checksum length）。这里等于总熵长度除以32
	CS := ENT / 32

	// 使用 SHA-256 哈希算法对熵进行哈希，得到一个hash值
	hash := sha256.Sum256(entropy)

	// 获取哈希的第一个字节
	firstByte := hash[0]
	shiftAmount := 8 - uint(CS) // 计算需要右移的位数

	// 无符号右移操作
	var result uint8
	if shiftAmount == 0 {
		result = firstByte
	} else {
		result = firstByte >> shiftAmount
	}
	return result
}

// CombineEntropyAndCheckBitsToBinary 将熵和校验位组合成二进制字符串
func CombineEntropyAndCheckBitsToBinary(entropy []byte, checkBits uint8) string {
	// 初始化一个空的二进制字符串
	binaryString := ""

	// 将熵中的每个字节转换为二进制字符串，并连接起来
	for _, byteIndex := range entropy {
		// 将字节转换为8位二进制字符串，不足8位的用0填充
		binaryString += fmt.Sprintf("%08b", byteIndex)
	}

	// 计算校验和位数（checksum length）
	CS := (len(entropy) * 8) / 32

	// 将校验位转换为二进制字符串，不足CS位的用0填充
	binaryString += fmt.Sprintf("%0*b", CS, checkBits)
	return binaryString
}

func SplitIntoIndices(bits string) ([]int, error) {
	var indices []int
	totalBits := len(bits)
	wordCount := totalBits / 11

	for i := 0; i < totalBits; i += 11 {
		index, err := strconv.ParseInt(bits[i:i+11], 2, 64)
		if err != nil {
			return nil, fmt.Errorf("failed to parse bits: %v", err)
		}
		indices = append(indices, int(index))
	}

	if len(indices) != wordCount {
		return nil, fmt.Errorf("invalid number of indices generated. Expected %d, but got %d", wordCount, len(indices))
	}

	return indices, nil
}

func IndicesToMnemonic(indices []int) (string, error) {
	// 获取 BIP-39 英文单词列表
	wordlist := bip39.GetWordList()
	if len(wordlist) == 0 {
		return "", fmt.Errorf("failed to get wordlist")
	}

	var mnemonicWords []string

	for _, index := range indices {
		if index < 0 || index >= len(wordlist) {
			return "", fmt.Errorf("index out of bounds: %d", index)
		}
		mnemonicWords = append(mnemonicWords, wordlist[index])
	}

	return strings.Join(mnemonicWords, " "), nil
}

func GenerateMnemonic(entropyBitSize int) (string, error) {
	entropy, err := GenerateEntropy(entropyBitSize)
	if err != nil {
		return "", err
	}
	checkBits := CalculateCheckBits(entropy)
	combinedBits := CombineEntropyAndCheckBitsToBinary(entropy, checkBits)
	indices, err := SplitIntoIndices(combinedBits)
	return IndicesToMnemonic(indices)
}
```

# 二. 助记词验证过程

## 1. 助记词验证原理

**1.1.检查单词数量**

助记词的单词数量通常为 12、15、18、21 或 24 个单词。如果给定的助记词的单词数量不在这些范围内，则助记词无效。

**1.2.检查单词是否在词汇表中**

每个助记词单词必须存在于 BIP-39 标准的 2048 个单词的词汇表中。如果有任何一个单词不在词汇表中，则助记词无效。

**1.3.将助记词转化成位串**

将每个助记词单词转换为它在词汇表中的索引。每个索引表示一个 11 位的二进制数。将所有的二进制数连接起来形成一个位串。

**1.4.提取种子和校验和**

位串的长度应该是助记词单词数乘以 11。例如，12 个单词的助记词对应的位串长度为 132 位。位串的前 128 位是种子，后 4 位是校验和。

**1.5.计算校验和**

将种子通过 SHA-256 哈希函数计算出一个哈希值，然后取哈希值的前 4 位作为计算得到的校验和。

**1.6. 验证校验和**

比较提取的校验和和计算得到的校验和。如果两者匹配，则助记词有效，否则无效。

## 2. 验证过程代码实现

```python
def validate_mnemonic(mnemonic, wordlist):
    words = mnemonic.split()
    
    # 检查单词数量
    if len(words) not in [12, 15, 18, 21, 24]:
        return False
    
    # 检查单词是否在词汇表中
    for word in words:
        if word not in wordlist:
            return False
    
    # 将助记词转化成位串
    binary_string = ''
    for word in words:
        index = wordlist.index(word)
        binary_string += format(index, '011b')
    
    # 提取种子和校验和
    seed_bits_length = (len(words) * 11) - (len(words) // 3)
    seed_bits = binary_string[:seed_bits_length]
    checksum_bits = binary_string[seed_bits_length:]
    
    # 计算校验和
    import hashlib
    seed_bytes = int(seed_bits, 2).to_bytes(len(seed_bits) // 8, byteorder='big')
    hash_value = hashlib.sha256(seed_bytes).hexdigest()
    hash_bits = bin(int(hash_value, 16))[2:].zfill(256)
    calculated_checksum = hash_bits[:len(words) // 3]
    
    # 验证校验和
    return checksum_bits == calculated_checksum

# Example usage:
mnemonic = "legal winner thank year wave sausage worth useful legal winner thank yellow"
wordlist = [...]  # BIP-39 wordlist
is_valid = validate_mnemonic(mnemonic, wordlist)
print("Is valid mnemonic:", is_valid)
```

# 三. 编码解码过程

和生成验证过程类似，这里不做过多的赘述

# 四. 调用 BIP-39 词库生成助记词

## 1.Node 代码实现

**1.1. 代码封装**

```javascript
const bip39  = require('bip39');
const bip32  = require('bip32');

export function createHelpWord(number: number, language: string) {
    switch (language) {
        case 'chinese_simplified':
            if(number === 12) {
                return bip39.generateMnemonic(128, null, bip39.wordlists.chinese_simplified);
            } else if(number === 15) {
                return bip39.generateMnemonic(160, null, bip39.wordlists.chinese_simplified);
            } else if(number === 18) {
                return bip39.generateMnemonic(192, null, bip39.wordlists.chinese_simplified);
            } else if(number === 21) {
                return bip39.generateMnemonic(224, null, bip39.wordlists.chinese_simplified);
            } else if(number === 24) {
                return bip39.generateMnemonic(256, null, bip39.wordlists.chinese_simplified);
            } else {
                return "unsupported"
            }
        case 'chinese_traditional':
            if(number === 12) {
                return bip39.generateMnemonic(128, null, bip39.wordlists.chinese_traditional);
            } else if(number === 15) {
                return bip39.generateMnemonic(160, null, bip39.wordlists.chinese_traditional);
            } else if(number === 18) {
                return bip39.generateMnemonic(192, null, bip39.wordlists.chinese_traditional);
            } else if(number === 21) {
                return bip39.generateMnemonic(224, null, bip39.wordlists.chinese_traditional);
            } else if(number === 24) {
                return bip39.generateMnemonic(256, null, bip39.wordlists.chinese_traditional);
            } else {
                return "unsupported"
            }
        case 'english':
            if(number === 12) {
                return bip39.generateMnemonic(128, null, bip39.wordlists.english);
            } else if(number === 15) {
                return bip39.generateMnemonic(160, null, bip39.wordlists.english);
            } else if(number === 18) {
                return bip39.generateMnemonic(192, null, bip39.wordlists.english);
            } else if(number === 21) {
                return bip39.generateMnemonic(224, null, bip39.wordlists.english);
            } else if(number === 24) {
                return bip39.generateMnemonic(256, null, bip39.wordlists.english);
            } else {
                return "unsupported"
            }
        case 'french':
            if(number === 12) {
                return bip39.generateMnemonic(128, null, bip39.wordlists.french);
            } else if(number === 15) {
                return bip39.generateMnemonic(160, null, bip39.wordlists.french);
            } else if(number === 18) {
                return bip39.generateMnemonic(192, null, bip39.wordlists.french);
            } else if(number === 21) {
                return bip39.generateMnemonic(224, null, bip39.wordlists.french);
            } else if(number === 24) {
                return bip39.generateMnemonic(256, null, bip39.wordlists.french);
            } else {
                return "unsupported"
            }
        case 'italian':
            if(number === 12) {
                return bip39.generateMnemonic(128, null, bip39.wordlists.italian);
            } else if(number === 15) {
                return bip39.generateMnemonic(160, null, bip39.wordlists.italian);
            } else if(number === 18) {
                return bip39.generateMnemonic(192, null, bip39.wordlists.italian);
            } else if(number === 21) {
                return bip39.generateMnemonic(224, null, bip39.wordlists.italian);
            } else if(number === 24) {
                return bip39.generateMnemonic(256, null, bip39.wordlists.italian);
            } else {
                return "unsupported"
            }
        case 'japanese':
            if(number === 12) {
                return bip39.generateMnemonic(128, null, bip39.wordlists.japanese);
            } else if(number === 15) {
                return bip39.generateMnemonic(160, null, bip39.wordlists.japanese);
            } else if(number === 18) {
                return bip39.generateMnemonic(192, null, bip39.wordlists.japanese);
            } else if(number === 21) {
                return bip39.generateMnemonic(224, null, bip39.wordlists.japanese);
            } else if(number === 24) {
                return bip39.generateMnemonic(256, null, bip39.wordlists.japanese);
            } else {
                return "unsupported"
            }
        case 'korean':
            if(number === 12) {
                return bip39.generateMnemonic(128, null, bip39.wordlists.korean);
            } else if(number === 15) {
                return bip39.generateMnemonic(160, null, bip39.wordlists.korean);
            } else if(number === 18) {
                return bip39.generateMnemonic(192, null, bip39.wordlists.korean);
            } else if(number === 21) {
                return bip39.generateMnemonic(224, null, bip39.wordlists.korean);
            } else if(number === 24) {
                return bip39.generateMnemonic(256, null, bip39.wordlists.korean);
            } else {
                return "unsupported"
            }
        case 'spanish':
            if(number === 12) {
                return bip39.generateMnemonic(128, null, bip39.wordlists.spanish);
            } else if(number === 15) {
                return bip39.generateMnemonic(160, null, bip39.wordlists.spanish);
            } else if(number === 18) {
                return bip39.generateMnemonic(192, null, bip39.wordlists.spanish);
            } else if(number === 21) {
                return bip39.generateMnemonic(224, null, bip39.wordlists.spanish);
            } else if(number === 24) {
                return bip39.generateMnemonic(256, null, bip39.wordlists.spanish);
            } else {
                return "unsupported"
            }
        default:
           return "unsupported"
    }
}

export function wordsToEntropy(mnemonic: string, language: string) {
    switch (language) {
        case 'chinese_simplified':
            return bip39.mnemonicToEntropy(mnemonic, bip39.wordlists.chinese_simplified);
        case 'chinese_traditional':
            return bip39.mnemonicToEntropy(mnemonic, bip39.wordlists.chinese_traditional);
        case 'english':
            return bip39.mnemonicToEntropy(mnemonic, bip39.wordlists.english);
        case 'french':
            return bip39.mnemonicToEntropy(mnemonic, bip39.wordlists.french);
        case 'italian':
            return bip39.mnemonicToEntropy(mnemonic, bip39.wordlists.italian);
        case 'japanese':
            return bip39.mnemonicToEntropy(mnemonic, bip39.wordlists.japanese);
        case 'korean':
            return bip39.mnemonicToEntropy(mnemonic, bip39.wordlists.korean);
        case 'spanish':
            return bip39.mnemonicToEntropy(mnemonic, bip39.wordlists.spanish);
        default:
            return "unsupported"
    }
}

export function entropyToWords(encrytMnemonic:string, language:string) {
    switch (language) {
        case 'chinese_simplified':
            return bip39.entropyToMnemonic(encrytMnemonic, bip39.wordlists.chinese_simplified);
        case 'chinese_traditional':
            return bip39.entropyToMnemonic(encrytMnemonic, bip39.wordlists.chinese_traditional);
        case 'english':
            return bip39.entropyToMnemonic(encrytMnemonic, bip39.wordlists.english);
        case 'french':
            return bip39.entropyToMnemonic(encrytMnemonic, bip39.wordlists.french);
        case 'italian':
            return bip39.entropyToMnemonic(encrytMnemonic, bip39.wordlists.italian);
        case 'japanese':
            return bip39.entropyToMnemonic(encrytMnemonic, bip39.wordlists.japanese);
        case 'korean':
            return bip39.entropyToMnemonic(encrytMnemonic, bip39.wordlists.korean);
        case 'spanish':
            return bip39.entropyToMnemonic(encrytMnemonic, bip39.wordlists.spanish);
        default:
            return "unsupported"
    }
}

export function mnemonicToSeed(mnemonic, password){
    return bip39.mnemonicToSeed(mnemonic, password)
}

export function mnemonicToSeedHex(mnemonic, password){
    return bip39.mnemonicToSeedHex(mnemonic, password);
}

export function validateMnemonic(mnemonic, language) {
    switch (language) {
        case 'chinese_simplified':
            return bip39.validateMnemonic(mnemonic, bip39.wordlists.chinese_simplified);
        case 'chinese_traditional':
            return bip39.validateMnemonic(mnemonic, bip39.wordlists.chinese_traditional);
        case 'english':
            return bip39.validateMnemonic(mnemonic, bip39.wordlists.english);
        case 'french':
            return bip39.validateMnemonic(mnemonic, bip39.wordlists.french);
        case 'italian':
            return bip39.validateMnemonic(mnemonic, bip39.wordlists.italian);
        case 'japanese':
            return bip39.validateMnemonic(mnemonic, bip39.wordlists.japanese);
        case 'korean':
            return bip39.validateMnemonic(mnemonic, bip39.wordlists.korean);
        case 'spanish':
            return bip39.validateMnemonic(mnemonic, bip39.wordlists.spanish);
        default:
            return "unsupported"
    }
}
```

**1.2.码测试用例**

```javascript
test('test create mnemonic', async () => {
    const mnemonic_12_english = createMnemonic(12, "english")
    const mnemonic_12_chinese = createMnemonic(12, "chinese_simplified")
    console.log(mnemonic_12_chinese)
    console.log(mnemonic_12_chinese)

    const mnemonic_to_entropy = mnemonicToEntropy(mnemonic_12_chinese, "chinese_simplified")
    console.log(mnemonic_to_entropy)


    const entropy_to_mnemonic = entropyToMnemonic(mnemonic_to_entropy, "chinese_simplified")
    console.log(entropy_to_mnemonic)

    const mnemonic_to_seed = mnemonicToSeed(mnemonic_to_entropy, "")
    console.log(mnemonic_to_seed)

    const mnemonic_to_seed_sync = mnemonicToSeedSync(mnemonic_to_entropy, "")
    console.log(mnemonic_to_seed_sync)

    const vm = validateMnemonic(mnemonic_to_entropy, "")
    console.log(vm)
});
```

# 2. Python 代码实现

**2.1.代码封装**

```python
class Bip39Mnemonic:
    def __init__(self):
        pass

    def createMnemonic(self, number):
        mnemonic = bip39.get_entropy_bits(number)
        return mnemonic

    def mnemonicToEntropy(self,  mnemonic):
        decode_words = bip39.decode_phrase( mnemonic)
        return decode_words
    def entropyToMnemonic(self, entropy):
        nemonic_entropy = bip39.encode_bytes(entropy)
        return nemonic_entropy

    def mnemonicToSeed(self, mnemonic):
        nemonic_to_seed = bip39.phrase_to_seed(mnemonic)
        return nemonic_to_seed

    def validateMnemonic(self, mnemonic):
        return bip39.check_phrase(mnemonic)

    def generateMnemonic(self):
        # 1. 生成 128 位随机熵 (16 字节)
        entropy = os.urandom(16)

        # 2. 计算校验和 (SHA-256)
        hash_bytes = hashlib.sha256(entropy).digest()
        checksum_bits = bin(hash_bytes[0])[2:].zfill(8)[:4]  # 取前 4 位

        # 3. 组合熵和校验和
        entropy_bits = ''.join([bin(byte)[2:].zfill(8) for byte in entropy])
        combined_bits = entropy_bits + checksum_bits

        # 4. 分割为助记词索引
        indices = [int(combined_bits[i:i + 11], 2) for i in range(0, len(combined_bits), 11)]

        # 5. 映射为助记词
        wordlist = bip39.INDEX_TO_WORD_TABLE
        mnemonic = ' '.join([wordlist[index] for index in indices])

        return mnemonic
```

**2.2.Python 测试用例**

```python
import bip

bip39_mnemonic = bip.Bip39Mnemonic()
mnemonic_phrase = bip39_mnemonic.generateMnemonic()
print(f"Generated mnemonic phrase: {mnemonic_phrase}")

mnemonic_12_phrase = bip39_mnemonic.createMnemonic(12)
print(f"create mnemonic phrase: {mnemonic_phrase}")
```
