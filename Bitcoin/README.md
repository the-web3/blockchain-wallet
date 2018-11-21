# 第六章：比特币钱包开发

本章主要内容有：比特币地址和比特币地址生成、比特私钥生成、比特币交易签名，发送比特币交易到区块链网络。

## 一.比特币的地址

### 1.比特币地址前缀

基于区块链的货币使用编码字符串，这些字符串采用Base58Check编码，但Bech32编码除外。 编码包括前缀（传统上是单个版本字节），其影响编码结果中的前导符号。 下面是比特币代码库中使用的一些前缀列表。

|十进制前缀    | 十六进制	 |      案列使用               |	   前面标识   |	                        例子                        | 
|-------------|-----------|-----------------------------|---------------|-----------------------------------------------------|
|       0	    |     00	  |    公钥Hash (P2PKH地址)	     |      1	       |    17VZNX1SN5NtKa8UQFxwQbFeFc3iqRYhem               |
|       5	    |     05    |	   脚本Hash(P2SH address)   |      3	       |     3EktnHQD7RiAE6uzMj2ZifT9YgRrkSgzQX              |
|     128	    |     80	  |  私钥 (WIF, 未压缩公钥)	    |      5	       |5Hwgr3u458GLafKBgxtssHSPqJnYoGrSzgQsPwLFhLNYskDPyyA |
|     128	    |     80	  |    私钥 (WIF, 压缩公钥)	     |    K or L	    |L1aW4aubDFB7yfras2S1mN3bqg9nwySY8nkoLmJebSLD5BWv3ENZ|
|4 136 178 30 | 	0488B21E|	          BIP32公钥	        |      xpub	     | xpub661MyMwAqRbcEYS8w7XLSVeEsBXy79zSzH1J8vCdxAZningWLdN3zgtU6LBpB85b3D2yc8sfvZU521AAwdZafEz7mnzBBsz4wKY5e4cp9LB|
|4 136 173 228|	0488ADE4 |	      BIP32私钥             |	      xprv	   | xprv9s21ZrQH143K24Mfq5zL5MhWK9hUhhGbd45hLXo2Pq2oqzMMo63oStZzF93Y5wvzdUayhgkkFoicQZcP3y52uPPxFnfoLZB21Teqt1VvEHx |
|      111    |   	6F	 |       测试公钥hash           |	m or n         |	    mipcBbFg9gMiCh81Kj8tqqdgoZub1ZJRfn                 |
|      196	  |     C4	 |        测试脚本hash          |	    2	          |    2MzQwSSnBHWHqSAqtTVQ6v47XtaisrJa1Vc                |
|      239	  |     EF	 | 测试网私钥 (WIF, 未压缩公钥)  |	9	             | 92Pg46rUhgTT7romnV7iGW6W1gbGdeezqdbJCzShkCsYNzyyNcc   |
|      239	  |     EF	 | 测试网私钥 (WIF, 压缩公钥)    |  c              |	cNJFgo1driFnPcBdBX8BrJrpxchBWXwXCvNH5SoSkdcF6JXXwHMm  |
|4 53 135 207 |	043587CF |     测试网BIP32公钥	         | tpub	           | tpubD6NzVbkrYhZ4WLczPJWReQycCJdd6YVWXubbVUFnJ5KgU5MDQrD998ZJLNGbhd2pq7ZtDiPYTfJ7iBenLVQpYgSQqPjUsQeJXH8VQ8xA67D|
|4 53 131 148 |	04358394 |      测试网BIP32私钥         |	tprv	         |  tprv8ZgxMBicQKsPcsbCVeqqF1KVdH7gwDJbxbzpCxDUsoXHdb6SnTPYxdwSAKDC6KKJzv7khnNWRAJQsRA8BBQyiSfYnRt6zuu4vZQGKjeW4YF|
|             |          |  Bech32公钥hash和脚本Hash	   | bc1	           | bc1qw508d6qejxtdg4y5r3zarvary0c5xw7kv8f3t4 |
|             |          |Bech32测试网公钥hash和脚本Hash| tb1	             |tb1qw508d6qejxtdg4y5r3zarvary0c5xw7kxpjzsx|

请注意，压缩和未压缩比特币公钥的私钥使用相同的版本字节。 压缩表单以不同字符开头的原因是因为在base58编码之前将私有键附加了0x01字节。

下表显示了每个可能的十进制版本值的160位哈希的前导符号和地址长度：

{| class="wikitable"
|-
!十进制版本
!前面标识
!地址长度
|-
|0
|1
|up to 34
|-
|1
|Q-Z, a-k, m-o
|33
|-
|2
|o-z, 2
|33 or 34
|-
|3
|2
|34
|-
|4
|2 or 3
|34
|-
|5-6
|3
|34
|-
|7
|3 or 4
|34
|-
|8
|4
|34
|-
|9
|4 or 5
|34
|-
|10-11
|5
|34
|-
|12
|5 or 6
|34
|-
|13
|6
|34
|-
|14
|6 or 7
|34
|-
|15-16
|7
|34
|-
|17
|7 or 8
|34
|-
|18
|8
|34
|-
|19
|8 or 9
|34
|-
|20-21
|9
|34
|-
|22
|9 or A
|34
|-
|23
|A
|34
|-
|24
|A or B
|34
|-
|25-26
|B
|34
|-
|27
|B or C
|34
|-
|28
|C
|34
|-
|29
|C or D
|34
|-
|30-31
|D
|34
|-
|32
|D or E
|34
|-
|33
|E
|34
|-
|34
|E or F
|34
|-
|35-36
|F
|34
|-
|37
|F or G
|34
|-
|38
|G
|34
|-
|39
|G or H
|34
|-
|40-41
|H
|34
|-
|42
|H or J
|34
|-
|43
|J
|34
|-
|44
|J or K
|34
|-
|45-46
|K
|34
|-
|47
|K or L
|34
|-
|48
|L
|34
|-
|49
|L or M
|34
|-
|50-51
|M
|34
|-
|52
|M or N
|34
|-
|53
|N
|34
|-
|54
|N or P
|34
|-
|55-56
|P
|34
|-
|57
|P or Q
|34
|-
|58
|Q
|34
|-
|59
|Q or R
|34
|-
|60-61
|R
|34
|-
|62
|R or S
|34
|-
|63
|S
|34
|-
|64
|S or T
|34
|-
|65-66
|T
|34
|-
|67
|T or U
|34
|-
|68
|U
|34
|-
|69
|U or V
|34
|-
|70-71
|V
|34
|-
|72
|V or W
|34
|-
|73
|W
|34
|-
|74
|W or X
|34
|-
|75-76
|X
|34
|-
|77
|X or Y
|34
|-
|78
|Y
|34
|-
|79
|Y or Z
|34
|-
|80-81
|Z
|34
|-
|82
|Z or a
|34
|-
|83
|a
|34
|-
|84
|a or b
|34
|-
|85
|b
|34
|-
|86
|b or c
|34
|-
|87-88
|c
|34
|-
|89
|c or d
|34
|-
|90
|d
|34
|-
|91
|d or e
|34
|-
|92-93
|e
|34
|-
|94
|e or f
|34
|-
|95
|f
|34
|-
|96
|f or g
|34
|-
|97-98
|g
|34
|-
|99
|g or h
|34
|-
|100
|h
|34
|-
|101
|h or i
|34
|-
|102-103
|i
|34
|-
|104
|i or j
|34
|-
|105
|j
|34
|-
|106
|j or k
|34
|-
|107-108
|k
|34
|-
|109
|k or m
|34
|-
|110
|m
|34
|-
|111
|m or n
|34
|-
|112-113
|n
|34
|-
|114
|n or o
|34
|-
|115
|o
|34
|-
|116
|o or p
|34
|-
|117-118
|p
|34
|-
|119
|p or q
|34
|-
|120
|q
|34
|-
|121
|q or r
|34
|-
|122-123
|r
|34
|-
|124
|r or s
|34
|-
|125
|s
|34
|-
|126
|s or t
|34
|-
|127-128
|t
|34
|-
|129
|t or u
|34
|-
|130
|u
|34
|-
|131
|u or v
|34
|-
|132-133
|v
|34
|-
|134
|v or w
|34
|-
|135
|w
|34
|-
|136
|w or x
|34
|-
|137-138
|x
|34
|-
|139
|x or y
|34
|-
|140
|y
|34
|-
|141
|y or z
|34
|-
|142-143
|z
|34
|-
|144
|z or 2
|34 or 35
|-
|145-255
|2
|35
|}
== References ==
<references />

{{Bitcoin Core documentation}}



