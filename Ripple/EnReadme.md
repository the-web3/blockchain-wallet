# Chapter 10: Prevent the loss of wallet private key

## 1. About the private key of the wallet

For most digital currency wallets on the market now, if you lose your mnemonic phrase and wallet private key, the entire wallet is basically lost. The author has encountered such a problem. I use the biwork wallet. There are 10,000 LET, 3 EOS and 0.45 Ethereum in the wallet. The biwork wallet is a decentralized wallet, and its private key and mnemonic code string are stored. on the client side. Later, I accidentally smashed the inner and outer screens of my phone. The cost of repairing the phone exceeded the value of the coins in my wallet. I couldn't find the mnemonic phrase I backed up at that time, and I haven't taken out the coin yet.

Developers who develop blockchain wallets should all know that the most popular currently is the HD wallet (deterministic hierarchical wallet), which supports multiple wallets, multiple currencies, and multiple accounts. For each wallet, the core part is the mnemonic, which is stored on the client as a coded string; the mnemonic can be restored through this coded string, and the entire account can be restored through the mnemonic. For each wallet, it also has a master private key that can restore the wallet; and for each account, it has its own private key, and the account can be completely retrieved through the private key. You can also transfer the digital assets in this wallet.

Whether it is a mnemonic code string, a wallet private key, or the private key of each account, it is a hexadecimal string. For this string, we have many ways to process it.

## 2. Anti-lost wallet mnemonic phrase, wallet private key, and wallet account private key storage solution

Decentralized wallets, whether mnemonic words, wallet private keys, or wallet account private keys, are all stored on the client. Once our backup files storing mnemonic phrases, wallet private keys, and wallet account private keys are lost and our client's wallet is damaged, it will be fatal for encrypted digital asset holders, which will mean that digital asset holders will Lose all cryptocurrencies.

Our principle in making a product is to make it easier for users to use it. Now the digital asset wallets in our market always like to let users back up their mnemonic words or private keys. In fact, this approach is relatively stupid. Why do you say this? For a non-technical person, mnemonic words and private keys have no concept in the user's mind. Many users may not even bother to look at the prompts you send inside the wallet. In a word, there is a threshold for the use of digital asset wallets in the current market.

### 1. Solve this problem from a hardware perspective

After the mnemonic phrase (the mnemonic phrase can be encoded and stored) or the key is generated on the local client, the key is sent to the small hardware device using Bluetooth communication.

.：
     ![.：
](https://github.com/guoshijiang/blockchain-wallet/blob/master/img/privateKey.png)

The advantage of this method is that it ensures that the private key and mnemonic code string are stored privately. When the client completely crashes, the mnemonic or private key can be retrieved through a small hardware device. Mnemonic phrases or private keys are no longer strongly dependent on the client, nor on the user's backup. Here, the hardware only serves as a backup function for the mnemonic phrase or private key, and the wallet only relies on small hardware devices when the mnemonic phrase or key is retrieved. The wallet can be used independently at any other time.

Of course, things always have opposites, and while solving problems it also creates some problems. Using such small hardware makes the wallet more expensive to build and use.

The arrival of the 5G era will lead to the rapid development of the Internet of Things. The development of the Internet of Things will also bring good solutions to the storage of blockchain wallet private keys. In the future, your private keys can be stored in many devices at home. , when your private key is lost, you can retrieve the private key as long as you connect your home device to the network. Of course, the "things" in the Internet of Things are in a network environment, which also has security issues.

#### 1.1. Detailed design of small hardware solution to key loss solution

The figure below is a simple logical design diagram for a small hardware device to store a key. The data transmission method uses Bluetooth transmission, including the two processes of key backup and recovery.

.：
     ![.：
](https://github.com/guoshijiang/blockchain-wallet/blob/master/img/1231.png)



##### Key backup process

The wallet generates a mnemonic phrase through a mnemonic algorithm, encodes the mnemonic phrase, encrypts the encoding string with a password, and then transmits the encrypted mnemonic phrase encoding string to a small hardware device through Bluetooth communication; or the wallet generates a private key. Afterwards, the private key is encrypted with a password and transmitted to the small hardware device via Bluetooth communication. This process is called the key backup process.


##### Key recovery process

When the device used to host the wallet is broken and the mnemonic phrase is forgotten, you need to start the key recovery process. Normally, a small hardware device will store some data for verifying the user. When the small hardware device passes the verification of the user, it can initiate a Bluetooth connection. After the Bluetooth connection is completed, the hardware device sends the encrypted private key or encrypted mnemonic code string to the wallet. After the wallet receives the encrypted string, the user enters the password to decrypt the code string or private key. After decryption, users can use the mnemonic phrase and private key to restore their wallet.


### 2. Key sharing hosting

Secret sharing is an important branch of information security research. Passwords are mainly used to encrypt important information and ensure the security of information. However, secret sharing can ensure the security of keys. It provides a platform for the development of cryptography. Fresh ideas. The principle of secret sharing is to divide the secret into several parts and hand them over to different people for safekeeping. If a certain number of people manage the secret contribute their own secrets, the secret can be restored.

The concept of secret sharing was first independently proposed by the famous cryptographers Shamir and Blakley in 1979 and gave their respective solutions. Shamir's (t, n) threshold scheme is implemented based on Lagrnage (Lagrangian) interpolation method, and Blakley's (t, n) threshold scheme is established by utilizing the properties of multi-dimensional space points.

Once the concept of secret sharing was proposed, it received great attention and research from experts and scholars. A lot has been achieved over the years. It can be roughly divided into the following categories.

* Threshold key sharing

In the (t, n) threshold secret sharing scheme, any set containing at least t participants is an authorized subset, while a set containing t-1 or less participants is an unauthorized subset, achieving (t, n ) Threshold secret sharing methods include Shamir and Blakley's scheme, the Asmuth-Bloom method based on the Chinese Remainder Theorem, and the Karnin-Greene Hellman method using matrix multiplication.

* Secret sharing scheme on general access structure

The threshold scheme is a secret sharing scheme that implements a threshold access structure. It has limitations for other broader access structures, such as sharing secrets among four members A, B, C, and D, so that A and D or B and C can cooperate to restore Secret and threshold secret sharing schemes cannot solve such a situation. To address this type of problem, cryptography researchers proposed a secret sharing scheme based on a general access structure in 1987. In 1988, someone proposed a simpler and more effective method - the monotonic circuit construction method, and proved that any access structure can be realized through a complete secret sharing scheme.

* Multiple secret sharing solutions

Only one sub-secret can be protected to share multiple secrets. In a multiple secret sharing scheme, each participant's sub-secret can be used multiple times, but only one secret can be shared in one secret sharing process.

*Multiple secret sharing solutions

Multiple secret sharing solves the problem of participant's sub-secret reuse, but it can only share one secret during a secret sharing process.

* Verifiable secret sharing scheme

Members participating in secret sharing can verify the correctness of the sub-secrets they own through public variables, thus effectively preventing mutual deception between distributors and participants, as well as between participants. Verifiable secret sharing schemes are divided into two types: interactive and non-interactive. The interactive verifiable secret sharing scheme means that each participant needs to exchange information with each other when verifying the correctness of the secret share; the non-interactive verifiable secret sharing means that each participant does not need to exchange information when verifying the correctness of the secret share. Information needs to be exchanged between each other. In terms of applications, non-interactive verifiable secret sharing can reduce network communication costs and reduce the chance of secret leaks, so the application fields are broader.

* Dynamic secret sharing solution

The dynamic secret sharing scheme was proposed in 1990. It has good security and flexibility. It allows adding or deleting participants, updating participants' sub-secrets regularly or irregularly, and recovering different secrets at different times, etc. . The above are several classic secret sharing schemes. It should be noted that a specific secret sharing scheme is often a collection of several types.

* Other secret sharing

Quantum secret sharing, visual secret sharing, secret sharing based on multi-resolution filtering, secret sharing based on generalized self-shrinking sequences.

#### 2.1. Introduction to classic secret sharing scheme

##### 2.1.1.shamir threshold secret sharing scheme

Shamir's (t, n) threshold scheme is implemented based on the Lagrnage (Lagrange) interpolation method. Simply put, assume that the secret is distributed to members through the secret sharing algorithm, and each member holds a sub-key, also known as Shadow or Secret debris, if:

Any valid member of not less than t can recover the secret using the correct shard he holds.
Any set with less than t members cannot recover the secret.
We call this scheme a (t,n) threshold secret sharing scheme, referred to as the threshold scheme, and t is called the threshold value of the scheme.

As the simplest and most practical threshold secret sharing scheme among various secret sharing schemes, the (k,n) threshold secret sharing scheme is also the most representative and widely used scheme among these schemes.

Below is a brief introduction to this solution.

* System parameters

Assume that n is the number of participants, n is the threshold value, and p is a large prime number requiring p > n and greater than p. The maximum possible value of secret s; both the secret space and the share space are finite fields GF(p).

* Distribute secretly

The process of secret distributor D allocating shares to n participants Pi (0 ≤ i ≤ n), that is, the allocation algorithm of the plan is as follows:

Randomly choose a polynomial of degree k - 1 on GF(p) such that f(0) = a0 = s The secret D to be shared among participants is kept secret from f(x).

D selects n mutually different non-zero elements x1, x2, …, xn in Zp and calculates (0≤ i ≤ n).

Assign ( xi , yi ) to participant Pi ( 0 ≤ i ≤ n), with value xi being public and yi being the secret share, not public.

* Secret reconstruction

Given any t points, let’s set them as the first t points (x1, y1), (x2, y2),…, (xt, yt). By the interpolation formula:


.：
     ![.：
](https://github.com/guoshijiang/blockchain-wallet/blob/master/img/111.png)


As a widely used threshold scheme, the Shamir scheme has the following advantages:

1. The entire polynomial can be determined from t secret shares and other secret shares can be calculated.

2. While the secret share of the original sharer remains unchanged, new sharers can be added, as long as the total number of new sharers does not exceed the number.

3. Before the original shared key is exposed, you can recalculate the share of the sharer's secret in the new round by constructing a second-order polynomial with new coefficients whose constant term is still the shared key, so that the sharer's original secret share can be recalculated. void.

But at the same time, the plan has the following problems

1. During the secret distribution phase, a dishonest secret distributor can distribute invalid secret shares to participants

2. During the secret reconstruction phase, some participants may submit invalid secret shares, making it impossible to recover the correct secret.

3. A point-to-point secure channel is required between the secret distributor and participants.

##### 2.1.2. Secret sharing scheme based on Chinese Remainder Theorem

The Chinese Remainder Theorem first appeared in the ancient Chinese mathematics masterpiece "Sun Tzu's Suan Jing", so it is also called Sun Tzu's Theorem. The Chinese Remainder Theorem is widely used in cryptography.

Let p1, p2,...,pm represent m pairwise relatively prime positive integers. Given any m integers, k1,k2,k3,...,km, there is a unique integer K belonging to GF(p1p2..., pm) that satisfies:

K = K1 mod p1
K = K2 mod P2
...
K = Km mod Pm
In 1983, cryptography experts proposed two secret sharing schemes based on the Chinese Remainder Theorem. The overall ideas are as follows.

* Initialization phase

Let P = {p1, p2, ... , Pn} be the set of participants. {p, d1, d2, ... dn} is a set of integers that satisfy the following conditions.

s < p, s is the secret that needs to be shared.
d1 < d2 < ... dn.
For i = 1,2, ... , n, we have gcd(di, p) = 1, where gcd(x, y) represents the greatest common divisor of x and y.
We have gcd(di, dj) = 1 for all i not equal to j.
d1d2...dk > pdn-t+2dn-k+3...dn.
Article 5 means that the product of the smallest k di is greater than the product of p and the k-1 largest di.

* Secret distribution stage

Let N = d1d2...dt, then N/p is greater than the product of any k-1 di. Let r be a random number in [0, N/p-1]. In order to divide the secret s into n parts, the secret distributor D calculates s' = s+ rp, where s' is limited to [0, N - 1], then the subkey xi of each participant Pi is:

xi = s' mod (di)

* Key reconstruction phase

For any k participants, let's assume that they are P1, P2, ... Pk and take out their k sub-keys x1, x2, ... xk. The corresponding module can be obtained from the Chinese Remainder Theorem:

N1 = d1d2 ...dk
Because N1 is greater than or equal to N, s' can be uniquely determined by the Chinese Remainder Theorem. The key can then be calculated from s', r, p:

s = s'-rp
If we only know k-1 sub-milangs, although we can know s' from N2,

N2 = d1d2 ...dk-1

But since N/N2 > p, and gcd(N2, p) = 1, the number x that makes x greater than or equal to N and x=s'mod(p) is uniformly distributed on the congruence class modulo p, so there is no Sufficient information to decide s'. In this solution, the time complexity of the dense steel recovery algorithm is O(k) and the space complexity is O(n).


#### 2.2. Key anti-loss solution

Regarding key sharing, let’s talk about the above. Based on the above theoretical knowledge, I think everyone already knows how to save the private key, that is, key sharing. According to the threshold sharing theory, the secret can be cut into n parts, and the secret can be recovered from k (k < n) parts. In this case, we can cut our private key into n parts, and let n people or devices manage and set up k secrets to help us recover the key. When our key is lost, we can initiate recovery. As long as k secrets are obtained, we can recover the private key. Whether your secrets should be stored by people or devices depends on your business.


### 3. Decentralized key storage solution

Based on the secret split in 2, we can store the split secret on the blockchain.
