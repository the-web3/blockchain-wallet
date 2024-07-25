# 如何在钱包里面集成 NFT 的功能

# 一. 什么是 NFT

关于 NFT 的定义大家可能已经在很多地方看到过了，叫做非同质通证或者半同质化通证。当然，看到这个词对于理解 NFT 一点帮助都没有，大部分非圈内人士都还是会一头雾水，这是什么玩意。首先，我们需要了解什么是同质化，什么是半同质化， 什么是非同质化。

## **1.名词解释**

- **同质化：** 同质化指每个资产都是相同的，可相互替换且可分割。我们在日常生活中使用到的同质化资产包括美元、比特币以及企业发行的积分；例如：1 比特币，可以分成 10 个 0.1 个比特币。
- **半同质化：** 半同质化和非同质化略有不同。 因为它们是半可替代的，所以它们本身与其他 NFT 不同。 但是，它们的数量可能大于一，可以以交易包的形式转账。
- **非同质化：**非同质化资产：指每个资产是独一无二的，相互完全独立，不可分割的。比如，一副画就是一种非同质化资产。
- **非同质化通证：**非同质化通证是在这个“非同质化”的概念上进一步做了扩展，利用以太坊等区块链网络来代表独一无二的实物或数字资产。NFT的所有权通过公共区块链网络进行验证和追踪，用户可以验证每个NFT的真实性，并追踪溯源。
- **NFT：**NFT真正的定义应该是：由原创者在区块链上发行的“真实性证明”，通过加密技术来证明NFT的持有者是官方标的资产的真实所有者。

## **2.NFT 的表现形式**

NFT 目前给我们最直观的感觉就是文子，图片，视频文件，音频文件，这也是他们的表现形式之一，从技术的角度出发，NFT 其实就是带有属性的，通过智能合约的形式写到区块链里面独一无二的文子，图片，视频文件和音频文件，用户可以通过区块链来验证每个NFT的真实性，并追踪溯源。

## **3. NFT 的价值**

关于 NFT 的价值，NFT 有没有价值，想想那些抽象派的画作就可以得出结论。经济形势好的时候，它可以天价， 经济形势不好的时候，可能一个馒头也比它的价值高。 上面说了那么多废话，接下来我们进入正题了。

# 二. **ERC20，ERC721 和 ERC1155**

## **1.ERC20**

ERC-20 于 2015 年首次提出，两年后的 2017 年最终被整合到以太坊生态系统中。ERC-20 引入了在以太坊区块链上创建可替代代币的代币标准。简而言之，ERC-20 由支持开发相同代币的属性组成。 例如，代表货币的 ERC-20 代币可以像以太坊的原生货币 Ether 一样发挥作用。这意味着 1 个代币将始终等于另一个代币的价值，并且可以相互互换。ERC 20 代币为可替代代币的开发设定了标准，但可替代代币实际上可以代表什么，下面举一些例子： ERC-20 于 2015 年首次提出，两年后的 2017 年最终被整合到以太坊生态系统中。ERC-20 引入了在以太坊区块链上创建可替代代币的代币标准。简而言之，ERC-20 由支持开发相同代币的属性组成。 例如，代表货币的 ERC-20 代币可以像以太坊的原生货币 Ether 一样发挥作用。这意味着 1 个代币将始终等于另一个代币的价值，并且可以相互互换。ERC 20 代币为可替代代币的开发设定了标准，但可替代代币实际上可以代表什么，下面举一些例子：

- 任何在线平台的声誉点。
- 彩票和计划。
- 金融资产，例如公司的股票、股息和股票
- 法定货币，包括美元。
- 金盎司，还有更多……

以太坊需要一个强大的标准来统一整个操作，以支持代币开发并在区块链网络上对其进行监管。这就是 ERC-20 发挥作用的地方。

去中心化世界的开发人员将 ERC-20 代币标准广泛用于不同目的，例如开发与以太坊生态系统中可用的其他产品和服务兼容的可互操作代币应用程序。

## **2.ERC-20 代币的特点**

- ERC 20 代币是“可替代代币”的另一个名称。
- 可替代性定义了资产或代币交换相同价值资产的能力，比如两张 1 美元的钞票。
- 无论其特性和结构如何，每个 ERC-20 代币都严格等同于相同的值。
- ERC 代币最受欢迎的应用领域是稳定币、治理代币和 ICO。

## **3. ERC-721：不可替代的代币**

要了解 ERC-721 标准，您必须首先了解 NFT（不可替代 Token ）。

Cryptokitties（广泛使用的不可替代代币）的创始人兼首席技术官 Dieter Shirley 最初提议开发一种新的代币类型来支持 NFT。该提案将于 2018 年晚些时候获得批准。它专门用于 NFT，这意味着按照 ERC-721 规则开发的代币可以代表以太坊区块链上任何数字资产的价值。

有了这个，我们得出一个结论：如果 ERC-20 对于发明新的加密货币至关重要，那么 ERC-721 对于代表某人对这些资产的所有权的数字资产来说是无价的。ERC-721 可以表示以下内容：

- 独一无二的数字艺术作品
- 推文和社交媒体帖子
- 游戏内收藏品
- 游戏角色
- 任何卡通人物和数百万其他 NFT ......

这种特殊类型的 Token 为使用 NFT 的企业带来了惊人的可能性。同样，ERC-721 也为他们带来了挑战，为了应对这些挑战，ERC-721 标准开始发挥作用。

请注意，每个 NFT 都有一个称为 tokenId 的 uint256 变量。因此，对于每个 EBR-721 合约，对合约地址 - uint256 tokenId 必须是唯一的。

此外，dApps 还应该有一个“转换器”来规范 NFT 的输入和输出过程。例如，转换器将 tokenId 视为输入并输出不可替代的令牌，例如僵尸、杀戮、游戏收藏品等的图像。

## **4. ERC-721 代币的特征**

- ERC-721 代币是不可替代代币 (NFT) 的标准。
- 这些代币不能交换任何同等价值的东西，因为它们是独一无二的。
- 每个 ERC-721 代表各自 NFT 的价值，可能不同。
- ERC-721 代币最受欢迎的应用领域是游戏中的 NFT。

## **5. ERC-1155 多代币标准**

结合 ERC-20 和 ERC-720 的能力，Witek Radomski（Enjin 的 CTO）为以太坊智能合约引入了一个包罗万象的代币标准。它是一个标准接口，支持开发可替代、半可替代、不可替代的代币和其他具有通用智能联系人的配置。 现在，您可以使用单一界面满足所有代币开发需求并解决问题，使 ERC-1155 成为游戏规则改变者。这种独特代币标准的想法是开发一个强大的智能合约接口，代表和管理不同形式的 ERC 代币。 ERC-1155 的另一个优点是它改进了以前 ERC 令牌标准的整体功能，使以太坊生态系统更加高效和可扩展。

## **6. ERC-1155 代币的特征**

- ERC-1155 是一个智能合约接口，代表可替代、半可替代和不可替代的代币。
- ERC-1155 可以执行 ERC-20 和 ERC-720 的功能，甚至可以同时执行两者。
- 每个代币可以根据代币的性质代表不同的价值；可替代的、半可替代的或不可替代的。
- ERC-1155 适用于创建 NFT、可兑换购物券、ICO 等。

## **7. 总结**

ERC20， ERC721 和 ERC1155 都各自有自己的标准接口，这个大家可以去阅读源码实现，这里不再做过多的叙述。

# 三. Ethereum 的 NFT 开发实战

NFT 的主战场其实还是主要在 Ethereum 上，NFT 创新也是先出现在 Ethereum 上；因此，学习 Ethereum 上的 NFT 开发，NFT 如何在钱包里面流转也变得非常之重要。

## 1.NFT Mint

- 完整代码：https://github.com/the-web3/nft-share/tree/main/EthNft


## 2. 钱包里面实现 NFT 离线交易签名

```js
export async function signOpMainnetTransaction (params: any): Promise<string> {
    const { privateKey, nonce, from, to, gasLimit, gasPrice, amount, data, chainId, decimal, maxFeePerGas, maxPriorityFeePerGas, tokenAddress, tokenId } = params;
    const wallet = new ethers.Wallet(Buffer.from(privateKey, 'hex'));
    const txData: any = {
        nonce: ethers.utils.hexlify(nonce),
        from,
        to,
        gasLimit: ethers.utils.hexlify(gasLimit),
        value: ethers.utils.hexlify(ethers.utils.parseUnits(amount, decimal)),
        chainId
    };
    if (maxFeePerGas && maxPriorityFeePerGas) {
        txData.maxFeePerGas = ethers.utils.hexlify(maxFeePerGas);
        txData.maxPriorityFeePerGas = ethers.utils.hexlify(maxPriorityFeePerGas);
    } else {
        txData.gasPrice = ethers.utils.hexlify(gasPrice);
    }
    if (tokenAddress && tokenAddress !== '0x00') {
        let idata: any;
        if (tokenId == "0x00") {
            const ABI = [
                'function transfer(address to, uint amount)'
            ];
            const iface = new Interface(ABI);
            idata = iface.encodeFunctionData('transfer', [to, ethers.utils.hexlify(ethers.utils.parseUnits(amount, decimal))]);
        } else {
            const ABI = [
                "function transferFrom(address from, address to, uint256 tokenId)"
            ];
            const iface = new Interface(ABI);
            idata = iface.encodeFunctionData("transferFrom", [wallet.address, to, tokenId])
        }
        txData.data = idata;
        txData.to = tokenAddress;
        txData.value = 0;
    }
    if (data) {
        txData.data = data;
    }
    return wallet.signTransaction(txData);
}
```

**注意：为了交易的执行效率，上面的代码可以根据自己的需求改成 EIP1559 的交易模式，如果是 The Web3 付费学员，可以在 The Web3 的以太坊钱包 SDK 里面需求完整的代码。**

- 测试代码

```js
test('sign', async () => {
    const rawHex = await signOpMainnetTransaction({
        "privateKey": "privateKey",
        "nonce": 25,
        "from": "0x72fFaA289993bcaDa2E01612995E5c75dD81cdBC",
        "to": "0xe3b4ECd2EC88026F84cF17fef8bABfD9184C94F0",
        "gasLimit": 120000,
        "amount": "1",
        "gasPrice": 27219060,
        "decimal": 1,
        "chainId": 1,
        "tokenAddress": "0x48C11b86807627AF70a34662D4865cF854251663",
        "tokenId": "2450"
    })
    console.log(rawHex)
});
```

- 注意：这里的 tokenAddress 填写的是合约地址，TokenId 填的是 NFT 的 Id，decimal 填 1，NFT 的数量也填 1 即可

## **3.发送 NFT 交易到区块链网络**

- 请求参数 签名的交易
- 请求示范

```text
curl --location 'https://eth-mainnet.g.alchemy.com/v2/XZw9s8EsSyUtwDOjtVvzwL8N0T96Zxt0' \
--header 'Content-Type: application/json' \
--header 'Cookie: _cfuvid=A7vae8DmfdNdKLQ37u_mDw17rqRDDuKqXFrJuWD1ccA-1716725090138-0.0.1.1-604800000' \
--data '{
    "jsonrpc":"2.0",
    "method":"eth_sendRawTransaction",
    "params":["0x"],
    "id":1
}'
```

- 返回值

```text
{
    "jsonrpc": "2.0",
    "id": 1,
    "error": {
        "code": -32000,
        "message": "typed transaction too short"
    }
}
```

- 返回值为交易 Hash

# 四. Solana 的 NFT 开发实战

## 1.NFT Mint

- 完整代码：https://github.com/the-web3/nft-share/tree/main/SolanaNft


## 2.钱包里面实现 NFT 离线交易签名

- *群发 NFT 命令代码*

```js
"use strict";
Object.defineProperty(exports, "__esModule", { value: true });
/*
 群发 NFT 命令
*/
const web3_js_1 = require("@solana/web3.js");
const program = require('commander');
const sign_1 = require("../src/sign");
const MainnetUrl = "https://api.mainnet-beta.solana.com";
// 测试地址： BUxRsHy1vuMswkrCrLv21TjByQQQ8GAycVdFm5JW6f6L
program
    .option('-f, --from <from>', 'from address')
    .option('-t, --to <to>', 'to address')
    .option('-c, --nonceAccountAddress <nonceAccountAddress>', 'nonce account address')
    .option('-o, --authorAddress <authorAddress>', 'author address')
    .option('-p, --privateKeys <privateKeys>', 'private Key')
    .action(async (singCmd) => {
    let connection = new web3_js_1.Connection(MainnetUrl);
    const version = await connection.getVersion();
    console.log("connection established:", MainnetUrl, version);
    // NFT Token 列表
    const nftTokenAddressList = [];
    // 转账逻辑
    for (let i = 0; i < nftTokenAddressList.length; i++) {
        const nonceAcctInfo = await connection.getAccountInfo(new web3_js_1.PublicKey(singCmd.nonceAccountAddress));
        const nonceAccount = web3_js_1.NonceAccount.fromAccountData(nonceAcctInfo.data);
        const nonce = nonceAccount.nonce;
        const params = {
            txObj: {
                from: singCmd.from,
                amount: 1,
                to: singCmd.to,
                nonce: nonce,
                nonceAccountAddress: singCmd.nonceAccountAddress,
                authorAddress: singCmd.authorAddress,
                decimal: 0,
                txType: "TRANSFER_TOKEN",
                mintAddress: nftTokenAddressList[i],
                hasCreatedTokenAddr: true
            },
            privs: singCmd.privateKeys,
        };
        const txMsg = await (0, sign_1.signTransaction)(params);
        const txhash = await connection.sendEncodedTransaction(txMsg);
        console.log("txhash is ", txhash);
    }
});
program.
    parse(process.argv);
```

- 离线签名代码

```js
async function signTransaction(params) {
    var _a, _b;
    const { txObj: { from, amount, to, nonce, nonceAccountAddress, authorAddress, decimal, txType, mintAddress, hasCreatedTokenAddr }, privs, } = params;
    const privateKey = (_a = (privs === null || privs === void 0 ? void 0 : privs.find(ele => ele.address === from))) === null || _a === void 0 ? void 0 : _a.key;
    if (!privateKey)
        throw new Error("privateKey 为空");
    const authorPrivateKey = (_b = (privs === null || privs === void 0 ? void 0 : privs.find(ele => ele.address === authorAddress))) === null || _b === void 0 ? void 0 : _b.key;
    if (!authorPrivateKey)
        throw new Error("authorPrivateKey 为空");
    // privateKey从hex转化为uint8array，生成feePayer
    const feePayer = web3_js_1.Keypair.fromSecretKey(new Uint8Array(Buffer.from(privateKey, "hex")));
    // 生成授权账号
    const author = web3_js_1.Keypair.fromSecretKey(new Uint8Array(Buffer.from(authorPrivateKey, "hex")));
    const calcAmount = new BigNumber(amount).times(new BigNumber(10).pow(decimal)).toString();
    // check if calcAmount is a float number
    if (calcAmount.indexOf(".") !== -1)
        throw new Error("decimal 无效");
    // 生成一个nonce交易
    let tx = new web3_js_1.Transaction();
    let tx1 = new web3_js_1.Transaction();
    const toPubkey = new web3_js_1.PublicKey(to);
    const fromPubkey = new web3_js_1.PublicKey(from);
    if (txType === "TRANSFER_TOKEN") {
        const mint = new web3_js_1.PublicKey(mintAddress);
        const fromTokenAccount = await SPLToken.Token.getAssociatedTokenAddress(SPLToken.ASSOCIATED_TOKEN_PROGRAM_ID, // ATokenGPvbdGVxr1b2hvZbsiqW5xWH25efTNsLJA8knL
        SPLToken.TOKEN_PROGRAM_ID, // TokenkegQfeZyiNwAJbNbGKPFXCWuBvf9Ss623VQ5DA
        mint, fromPubkey);
        const toTokenAccount = await SPLToken.Token.getAssociatedTokenAddress(SPLToken.ASSOCIATED_TOKEN_PROGRAM_ID, SPLToken.TOKEN_PROGRAM_ID, mint, toPubkey);
        tx.add(web3_js_1.SystemProgram.nonceAdvance({
            noncePubkey: new web3_js_1.PublicKey(nonceAccountAddress),
            authorizedPubkey: author.publicKey,
        }), SPLToken.Token.createTransferInstruction(SPLToken.TOKEN_PROGRAM_ID, fromTokenAccount, // ata
        toTokenAccount, // ata
        fromPubkey, // from pubkey
        [feePayer, author], // mutiple signers需要带
        calcAmount));
        if (!hasCreatedTokenAddr) {
            tx1.add(web3_js_1.SystemProgram.nonceAdvance({
                noncePubkey: new web3_js_1.PublicKey(nonceAccountAddress),
                authorizedPubkey: author.publicKey,
            }), SPLToken.Token.createAssociatedTokenAccountInstruction(SPLToken.ASSOCIATED_TOKEN_PROGRAM_ID, SPLToken.TOKEN_PROGRAM_ID, mint, toTokenAccount, // 要创建的token account
            toPubkey, feePayer.publicKey), SPLToken.Token.createTransferInstruction(SPLToken.TOKEN_PROGRAM_ID, fromTokenAccount, // ata
            toTokenAccount, // ata
            fromPubkey, // from pubkey
            [feePayer, author], // mutiple signers需要带
            calcAmount));
        }
    }
    else {
        tx.add(web3_js_1.SystemProgram.nonceAdvance({
            noncePubkey: new web3_js_1.PublicKey(nonceAccountAddress),
            authorizedPubkey: author.publicKey,
        }), web3_js_1.SystemProgram.transfer({
            fromPubkey: feePayer.publicKey,
            toPubkey: toPubkey,
            lamports: calcAmount,
        }));
    }
    tx.recentBlockhash = nonce;
    tx.sign(feePayer, author);
    const serializeMsg = tx.serialize().toString("base64");
    if (txType === "TRANSFER_TOKEN") {
        if (!hasCreatedTokenAddr) {
            tx1.recentBlockhash = nonce;
            tx1.sign(feePayer, author);
            const serializeMsg1 = tx1.serialize().toString("base64");
            return JSON.stringify([serializeMsg1, serializeMsg]);
        }
        else {
            return JSON.stringify([serializeMsg]);
        }
    }
    return serializeMsg;
}
exports.signTransaction = signTransaction;
```

## 3. 发送 NFT 交易到区块链网络

- 请求示范

```text
curl --location 'https://sly-yolo-dinghy.solana-mainnet.quiknode.pro/2ac2af5b8c2e5e9e74c7906e949f1976314aa996' \
--header 'Content-Type: application/json' \
--data '{
    "jsonrpc": "2.0",
    "id": 1,
    "method": "sendTransaction",
    "params": [
      "ASG4jVQEsZhgMGnkXIYEvyhvGMYBfwL8lFd40mPz4AjpeWqZlVM122Vx8iCZIUNmbGdqDcdplm0xMUGri4WiCAIBAAEDOns4dLpGe+a4HqNh49dFOvi4HIiu3SS1Ax/doLxxrTJa8yfsHKLzR/zaYPv8xS5VGKfAp4PkO8pQN4Jn396IRgAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAKy6/vKD5x9Dmbz1bBJ85fYZK94CfRhjYwMBlQyb4mz8BAgIAAQwCAAAAQEIPAAAAAAA=",
      {
        "encoding":"base64"
      }
     
    ]
  }'
```

- 返回值

```text
{
    "jsonrpc": "2.0",
    "result": "g6yMJUd16jAcPiQfi7QK1M5tN7yheeKAC7NbUh3BQCSfav3pgv74ovCSGuhgApzma1s6ew8WEmX1Bzfk6sCfg9P",
    "id": 1
}
```

# 五. 总结

本教程以 Ethereum，Solana 上的 NFT 合约开发和钱包中的 NFT 离线签名转账为例子说明，其他的链的 NFT 和这两个链的 NFT 几乎也是大同小异， NFT 在玩法上也比较类似，这里就不再做过多的赘述，对于上面的实例，请大家多看看我们贴进去的代码链接，对初学者应该会有很大的帮助。

NFT 在交易所钱包的集成也类似代币 Token 的集成方式，有充值，提现，归集，转冷，冷转热等逻辑。

和代币不一样的是，由于流动性和 NFT 资产的一些属性问题，NFT 在交易所里面的交易方式一般是一种一种低频交易资产。

# 六. 资料链接

## 1.Ethereum

- solidity 合约开发语言： https://docs.soliditylang.org/en/v0.8.14/

- hardhat： https://hardhat.org/getting-started/

- foundry： [https://book.getfoundry.sh](https://book.getfoundry.sh/)

   智能合约开发工具，比较推荐使用的工具之一

- truffle：https://trufflesuite.com/docs/

    智能合约开发工具，不太推荐使用

- remix工具： http://remix.ethereum.org/； 初学者推荐使用

- Remix docs: https://remix-ide.readthedocs.io/en/latest 初学者推荐使用

- metamask 钱包:  https://metamask.io/

- Influra: https://infura.io/ 一家提供 ETH 开放节点的服务上，当然还有其他很多服务商，例如 https://www.alchemy.com/；

- 测试币:  建议去个网络的水龙头领取

## 2. Solana

- Solana cli： https://docs.solana.com/cli/conventions

- Metaplex： https://www.metaplex.com/

- Web3：https://github.com/solana-labs/solana-web3.js

- Rust： 

  💡💡The Rust Programming Language：[https://rustwiki.org/zh-CN/book/title-page.html ](https://rustwiki.org/zh-CN/book/title-page.html)

  💡💡Rust by Example：[https://rustwiki.org/zh-CN/rust-by-example/index.html ](https://rustwiki.org/zh-CN/rust-by-example/index.html)

  💡💡Rust 语⾔圣经：https://course.rs/about-book.html

- anchor：https://project-serum.github.io/anchor/getting-started/introduction.html
