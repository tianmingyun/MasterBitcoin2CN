## Appendix A: Segregated Witness

## 附录 A ：隔离见证

Segregated Witness (segwit) is an upgrade to the bitcoin consensus rules and network protocol, proposed and implemented as a BIP-9 soft-fork that is currently (mid-2017) pending activation.

隔离见证（segwit）是一次比特币共识规则和网络协议的升级，其提议和实施将基于BIP-9 软分叉方案，目前（2017年中）尚待激活。

In cryptography, the term “witness” is used to describe a solution to a cryptographic puzzle. In bitcoin terms, the witness satisfies a cryptographic condition placed on a unspent transaction output (UTXO).

在密码学中，术语“见证”（witness）被用于形容一个加密难题的解决方案。在比特币中，“见证”满足了一种被放置在一个未使用的交易输出（unspent transaction output, UTXO）上的加密条件。

In the context of bitcoin, a digital signature is one type of witness, but a witness is more broadly any solution that can satisfy the conditions imposed on an UTXO and unlock that UTXO for spending. The term “witness” is a more general term for an “unlocking script” or “scriptSig.”

在比特币语境中，一个数字签名就是一种类型的“见证”（one type of witness）。但“见证”是一个更为广泛的任意解决方案，能够满足加诸于一个UTXO的条件，使UTXO解锁后可被花费。术语“见证”一词是一个更普遍用于“解锁脚本”（或scriptSig）的术语。

Before segwit’s introduction, every input in a transaction was followed by the witness data that unlocked it. The witness data was embedded in the transaction as part of each input. The term segregated witness, or segwit for short, simply means separating the signature or unlocking script of a specific output. Think “separate scriptSig,” or “separate signature” in the simplest form.

在引入“隔离见证”之前，每一个交易输入后面都跟着用来对其解锁的见证数据，见证数据作为输入的一部分被内嵌其中。术语“隔离见证”（ segregated witness），或简称为“segwit”，简单理解就是将某个特定输出的签名分离开，或将某个特定输入的脚本进行解锁。用最简单的形式来理解就是“分离解锁脚本”（separate scriptSig），或“分离签名”（separate signature）

Segregated Witness therefore is an architectural change to bitcoin that aims to move the witness data from the scriptSig (unlocking script) field of a transaction into a separate a witness data structure that accompanies a transaction. Clients may request transaction data with or without the accompanying witness data.

因此，隔离见证就是比特币的一种结构性调整，旨在将见证数据部分从一笔交易的scriptSig（解锁脚本）字段移出至一个伴随交易的单独的见证数据结构。客户端请求交易数据时可以选择要或不要该部分伴随的见证数据。

In this section we will look at some of the benefits of Segregated Witness, describe the mechanism used to deploy and implement this architecture change, and demonstrate the use of Segregated Witness in transactions and addresses.

在这一章节，我们将会看到隔离见证的一些好处，描述用于部署和实施该结构性调整的机制，并展示隔离见证在交易和地址中的运用。

Segregated Witness is defined by the following BIPs:

隔离见证由以下BIPs定义：

BIP-141

The main definition of Segregated Witness.

隔离见证的主要定义
BIP-143

Transaction Signature Verification for Version 0 Witness Program

版本0见证程序的交易签名验证
BIP-144

Peer Services—New network messages and serialization formats

对等服务——新的网络消息和序列化格式
BIP-145

getblocktemplate Updates for Segregated Witness (for mining)

隔离见证（对于矿工）的 getblocktemplate 升级

### Why Segregated Witness?

### 为什么需要隔离见证？

Segregated Witness is an architectural change that has several effects on the scalability, security, economic incentives, and performance of bitcoin:

隔离见证是一个将在多方面产生影响的结构性调整——可扩展性、安全性、经济刺激以及比特币整体性能：

Transaction Malleability

交易延展性

By moving the witness outside the transaction, the transaction hash used as an identifier no longer includes the witness data. Since the witness data is the only part of the transaction that can be modified by a third party (see Transaction identifiers), removing it also removes the opportunity for transaction malleability attacks. With Segregated Witness, transaction hashes become immutable by anyone other than the creator of the transaction, which greatly improves the implementation of many other protocols that rely on advanced bitcoin transaction construction, such as payment channels, chained transactions, and lightning networks.

将见证移出交易后，用作标识符的交易哈希不在包含见证数据。因为见证数据是交易中唯一可被第三方修改（参见 交易识别符 章节）的部分，移除它的同时也移除了交易延展性攻击的机会。通过隔离见证，交易变得对任何人（创建者本人除外）都不可变，这极大地提高了许多其它依赖于高级比特币交易架构的协议的可执行性。比如支付通道、跨连交易和闪电网络。

Script Versioning

脚本版本管理

With the introduction of Segregated Witness scripts, every locking script is preceded by a script version number, similar to how transactions and blocks have version numbers. The addition of a script version number allows the scripting language to be upgraded in a backward-compatible way (i.e., using soft fork upgrades) to introduce new script operands, syntax, or semantics. The ability to upgrade the scripting language in a nondisruptive way will greatly accelerate the rate of innovation in bitcoin.

在引入隔离见证脚本后，类似于交易和区块都有其版本号，每一个锁定脚本前也都有了一个脚本版本号。脚本版本号的条件允许脚本语言用一种向后兼容的方式（也就是软分叉升级）升级，以引入新的脚本操作数、语法或语义。非破坏性升级脚本语言的能力将极大地加快比特币的创新速度。

Network and Storage Scaling

网络和存储扩展

The witness data is often a big contributor to the total size of a transaction. More complex scripts such as those used for multisig or payment channels are very large. In some cases these scripts account for the majority (more than 75%) of the data in a transaction. By moving the witness data outside the transaction, Segregated Witness improves bitcoin’s scalability. Nodes can prune the witness data after validating the signatures, or ignore it altogether when doing simplified payment verification. The witness data doesn’t need to be transmitted to all nodes and does not need to be stored on disk by all nodes.

见证数据通常是交易总体积的重要贡献者。更复杂的脚本通常非常大，比如那些用于多重签名或支付通道的脚本。有时候这些脚本占据了一笔交易的大部分（超过75%）空间。通过将见证数据移出交易，隔离见证提升了比特币的可扩展性。节点能够在验证签名后去除见证数据，或在作简单支付验证时整个忽略它。见证数据不需要被发送至所有节点，也不需要被所有节点存储在硬盘中。

Signature Verification Optimization

签名验证优化

Segregated Witness upgrades the signature functions (CHECKSIG, CHECKMULTISIG, etc.) to reduce the algorithm’s computational complexity. Before segwit, the algorithm used to produce a signature required a number of hash operations that was proportional to the size of the transaction. Data-hashing computations increased in O(n2) with respect to the number of signature operations, introducing a substantial computational burden on all nodes verifying the signature. With segwit, the algorithm is changed to reduce the complexity to O(n).

隔离见证升级签名函数（CHECKSIG, CHECKMULTISIG, 等）减少了算法的计算复杂性。引入隔离见证前，用于生成签名的算法需要大量的哈希操作，这些操作与交易的大小成正比。在O(n2)中关于签名操作数量方面，数据哈希计算增加，在所有节点验证签名上引入了大量计算负担。引入隔离见证后，算法更改减少了O(n2)的复杂性。

Offline Signing Improvement

离线签名改进

Segregated Witness signatures incorporate the value (amount) referenced by each input in the hash that is signed. Previously, an offline signing device, such as a hardware wallet, would have to verify the amount of each input before signing a transaction. This was usually accomplished by streaming a large amount of data about the previous transactions referenced as inputs. Since the amount is now part of the commitment hash that is signed, an offline device does not need the previous transactions. If the amounts do not match (are misrepresented by a compromised online system), the signature will be invalid.

隔离见证签名包含了在被签名的哈希散列中，每个输入所引用的值（数量）。在此之前，一个离线签名装置，比如硬件钱包，必须在签署交易前验证每一个输入的数量。这通常是通过大量的数据流来完成的，这些数据是关于以前的交易被引用作为输入的。由于该数量现在是已签名的承诺哈希散列的一部分，因此离线装置不需要以前的交易。如果数量不匹配（被一个折中的在线系统误报），则签名无效。

### How Segregated Witness Works

### 隔离见证如何工作

At first glance, Segregated Witness appears to be a change to how transactions are constructed and therefore a transaction-level feature, but it is not. In fact, Segregated Witness is also a change to how individual UTXO are spent and therefore is a per-output feature.

乍一看，隔离见证似乎是对交易如何构建的更改，因此是一个交易层面的特性，但事实并非如此。实际上，隔离见证也更改了单个UTXO如何被使用的方式，因此它是一个输出层面的特性。

A transaction can spend Segregated Witness outputs or traditional (inline-witness) outputs or both. Therefore, it does not make much sense to refer to a transaction as a “Segregated Witness transaction.” Rather we should refer to specific transaction inputs as “Segregated Witness inputs."

一个交易可以引用隔离见证输出或传统（内联见证）输出，或者两者皆可。因此，把一个交易称作“隔离见证交易”是没有意义的。但是我们可以把某个特定的交易输出叫做“隔离见证输出”。

When a transaction spends an UTXO, it must provide a witness. In a traditional UTXO, the locking script requires that witness data be provided inline in the input part of the transaction that spends the UTXO. A Segregated Witness UTXO, however, specifies a locking script that can be satisfied with witness data outside of the input (segregated).

当一个交易引用一个UTXO，它必须提供一个见证。如果是传统的UTXO，一个交易在引用它时，UTXO的锁定脚本要求见证数据在该交易输出部分中以“内联”（inline）的方式被提供。但隔离见证UTXO指定的锁定脚本却能满足处于输入之外（被隔离）的见证数据。

### Soft Fork (Backward Compatibility)

### 软分叉（向后兼容性）

Segregated Witness is a significant change to the way outputs and transactions are architected. Such a change would normally require a simultaneous change in every bitcoin node and wallet to change the consensus rules—what is known as a hard fork. Instead, segregated witness is introduced with a much less disruptive change, which is backward compatible, known as a soft fork. This type of upgrade allows nonupgraded software to ignore the changes and continue to operate without any disruption.

隔离见证对于输出和交易的构建的方式是一个十分重大的改变。这样的改变将通常需要每一个比特币节点和钱包同时发生，以改变共识规则——即所谓的“硬分叉”。但是，隔离见证通过一个更少破坏性的改变引入，这种变化能向后兼容，被称作“软分叉”。这种类型的升级允许未升级的软件去忽略那些改变然后继续去操作避免任何分裂。

Segregated Witness outputs are constructed so that older systems that are not segwit-aware can still validate them. To an old wallet or node, a Segregated Witness output looks like an output that anyone can spend. Such outputs can be spent with an empty signature, therefore the fact that there is no signature inside the transaction (it is segregated) does not invalidate the transaction. Newer wallets and mining nodes, however, see the Segregated Witness output and expect to find a valid witness for it in the transaction’s witness data.

隔离见证输出被设计成老的“非隔离见证”系统仍然能够验证它们，对于老的钱包或节点来说，一个隔离见证输出看起来就像一个“任何人都能花费”（anyone can spend）的输出。这样的输出能被一个空的签名花费，因此一个交易里面没有签名（签名被隔离）的事实也并不会导致该交易不被验证。但是，更新的钱包和挖矿节点能够看到隔离见证输出，并期望在交易的见证数据中为该输出找到一个有效的见证。

### Segregated Witness Output and Transaction Examples

### 隔离见证输出和交易示例

Let’s look at some of our example transactions and see how they would change with Segregated Witness. We’ll first look at how a Pay-to-Public-Key-Hash (P2PKH) payment is transformed with the Segregated Witness program. Then, we’ll look at the Segregated Witness equivalent for Pay-to-Script-Hash (P2SH) scripts. Finally, we’ll look at how both of the preceding Segregated Witness programs can be embedded inside a P2SH script.

让我们来看一些交易示例，看看他们是如何随着隔离见证而改变的。我们将首先看看通过隔离见证程序，如何被改变一个“支付给公钥哈希”（Pay-to-Public-Key-Hash ，P2PKH）的支付。然后，我们再看同样的隔离见证如何作用于“支付给脚本公钥”（Pay-to-Script-Hash ，P2SH）脚本。最后，我们会看看以上两种隔离见证程序如何可以被内嵌入一个 P2SH 脚本。

Pay-to-Witness-Public-Key-Hash (P2WPKH)

支付给见证公钥哈希（P2WPKH）

In [cup_of_coffee], Alice created a transaction to pay Bob for a cup of coffee. That transaction created a P2PKH output with a value of 0.015 BTC that was spendable by Bob. The output’s script looks like this:

在【一杯咖啡】的例子中，爱丽丝为一杯咖啡创建了一笔交易去付款给鲍勃，该笔交易构建了一个价值0.015BTC的 P2PKH 输出（鲍勃可用来花费），该输出脚本看起来像这样：

Example P2PKH output script

P2PKH 输出脚本示例：

1
DUP HASH160 ab68025513c3dbd2f7b92a94e0581f5d50f654e7 EQUALVERIFY CHECKSIG
With Segregated Witness, Alice would create a Pay-to-Witness-Public-Key-Hash (P2WPKH) script, which looks like this:

如果通过隔离见证，爱丽丝将会蒋健一个“支付给见证公钥哈希”（P2WPKH）脚本，看起来是这样的：

Example P2WPKH output script

P2WPKH 输出脚本示例：

1
0 ab68025513c3dbd2f7b92a94e0581f5d50f654e7
As you can see, a Segregated Witness output’s locking script is much simpler than a traditional output. It consists of two values that are pushed on to the script evaluation stack. To an old (nonsegwit-aware) bitcoin client, the two pushes would look like an output that anyone can spend and does not require a signature (or rather, can be spent with an empty signature). To a newer, segwit-aware client, the first number (0) is interpreted as a version number (the witness version) and the second part (20 bytes) is the equivalent of a locking script known as a witness program. The 20-byte witness program is simply the hash of the public key, as in a P2PKH script

正如你所见，一个隔离见证输出的锁定脚本相对一个传统输出明显大为简化。它包含两个值，这些值被推送到脚本评估堆栈中。对于一个传统（非隔离见证）比特币客户端来说，这两个推送值看起来像一个任何人都能花费的输出而不需要签名（或者更确切的说，能被空的签名使用）。而对一个更新的、隔离见证客户端来说，第一个数字（0）被解释为一个版本号（见证版本），第二部分（20字节）相当于一个锁定脚本，被称为“见证程序”（ witness program）。这20字节的检证程序即是公钥哈希值，就像在 P2PKH 脚本中一样。

Now, let’s look at the corresponding transaction that Bob uses to spend this output. For the original script (nonsegwit), Bob’s transaction would have to include a signature within the transaction input:

现在，让我们来看看鲍勃用来去花费这个输出相应的交易。对于初始脚本（非隔离见证），鲍勃的交易必须包含签名在交易输入中：

Decoded transaction showing a P2PKH output being spent with a signature

以下被解码的交易显示了一个 P2PKH 输出被一个签名使用：

1
2
3
4
5
6
7
[...]
“Vin” : [
"txid": "0627052b6f28912f2703066a912ea577f2ce4da4caa5a5fbd8a57286c345c2f2",
"vout": 0,
     	 "scriptSig": “<Bob’s scriptSig>”,
]
[...]
However, to spend the Segregated Witness output, the transaction has no signature on that input. Instead, Bob’s transaction has an empty scriptSig and includes a Segregated Witness, outside the transaction itself:

但是，使用一个隔离见证输出时，交易输入内不存在签名。相替代的，鲍勃的交易只有一个空的 scriptSig ，并在交易本身之外包含了一个隔离见证：

Decoded transaction showing a P2WPKH output being spent with separate witness data

以下被解码的交易显示了一个被隔离见证数据使用的 P2WPKH 输出：

1
2
3
4
5
6
7
8
9
[...]
“Vin” : [
"txid": "0627052b6f28912f2703066a912ea577f2ce4da4caa5a5fbd8a57286c345c2f2",
"vout": 0,
     	 "scriptSig": “”,
]
[...]
“witness”: “<Bob’s witness data>”
[...]
Wallet construction of P2WPKH 钱包的P2WPKH建设

It is extremely important to note that P2WPKH should only be created by the payee (recipient) and not converted by the sender from a known public key, P2PKH script, or address. The sender has no way of knowing if the recipient’s wallet has the ability to construct segwit transactions and spend P2WPKH outputs.

值得极为注意的是，P2WPKH 应该只有收款人（接收方）创建，而不是由发送者从已知的公钥、P2PKH 脚本或地址进行转换。发送方无从知道接收者的钱包是否具有能力构建隔离见证交易，并使用 P2WPKH 输出。

Additionally, P2WPKH outputs must be constructed from the hash of a compressed public key. Uncompressed public keys are nonstandard in segwit and may be explicitly disabled by a future soft fork. If the hash used in the P2WPKH came from an uncompressed public key, it may be unspendable and you may lose funds. P2WPKH outputs should be created by the payee’s wallet by deriving a compressed public key from their private key.

另外，P2WPKH 输出必须从被压缩的公钥的哈希值中创建。未压缩公钥在隔离见证中是非标准的，可能会被将来的软分叉明确禁用。如果在 P2WPKH 中使用的哈希值来自未压缩的公钥，那么它可能不可用，您将可能丢失资金。P2WPKH 输出应该由收款人的钱包，通过从钱包私钥中获取压缩公钥进行创建。

Warning	P2WPKH should be constructed by the payee (recipient) by converting a compressed public key to a P2WPKH hash. You should never transform a P2PKH script, bitcoin address, or uncompressed public key to a P2WPKH witness script.
警告	P2WPKH 应该由收款人（接收者）通过将被压缩的公钥转换成P2WPKH哈希值进行创建。你绝不应该将P2PKH脚本、比特币地址或未压缩公钥转换成P2WPKH见证脚本。
Pay-to-Witness-Script-Hash (P2WSH)

支付给见证脚本哈希（P2WSH）

The second type of witness program corresponds to a Pay-to-Script-Hash (P2SH) script. We saw this type of script in [p2sh]. In that example, P2SH was used by Mohammed’s company to express a multisignature script. Payments to Mohammed’s company were encoded with a locking script like this:

第二种类型的检证程序对应一个“支付给脚本哈希”（ Pay-to-Script-Hash , P2SH）脚本。我们在[p2sh]这张中见过。在哪个例子中，穆罕默德的公司使用了P2SH 来表达一个多重签名脚本。对穆罕默德的公司的支付被编码成一个这样的锁定脚本：

Example P2SH output script

P2SH 输出脚本示例：

1
HASH160 54c557e07dde5bb6cb791c7a540e0a4796f5e97e EQUAL
This P2SH script references the hash of a redeem script that defines a 2-of-3 multisignature requirement to spend funds. To spend this output, Mohammed’s company would present the redeem script (whose hash matches the script hash in the P2SH output) and the signatures necessary to satisfy that redeem script, all inside the transaction input:

这个P2SH脚本引用了一个赎回脚本（ redeem script ）的哈希值，改脚本定义了一个“2 of 3”的多重签名需求来使用资金。为了使用该输出，穆罕默德的公司将提供这个赎回脚本（其哈希值与P2SH 输出中的脚本哈希值匹配），以及满足该赎回脚本所需的签名，所有这些都在交易输出中：

Decoded transaction showing a P2SH output being spent

显示一个P2SH输出被使用的解码交易：

1
2
3
4
5
6
[...]
“Vin” : [
"txid": "abcdef12345...",
"vout": 0,
     	 "scriptSig": “<SigA> <SigB> <2 PubA PubB PubC PubD PubE 5 CHECKMULTISIG>”,
]
Now, let’s look at how this entire example would be upgraded to segwit. If Mohammed’s customers were using a segwit-compatible wallet, they would make a payment, creating a Pay-to-Witness-Script-Hash (P2WSH) output that would look like this:

现在，让我们看看整个示例如何升级成为隔离见证。如果穆罕默德的客户使用的是一个隔离见证兼容的钱包，他们就会付款，创建一个“支付给脚本哈希”(P2WSH)输出，看起来就像这样：

Example P2WSH output script

P2WSH 输出脚本示例：

1
0 9592d601848d04b172905e0ddb0adde59f1590f1e553ffc81ddc4b0ed927dd73
Again, as with the example of P2WPKH, you can see that the Segregated Witness equivalent script is a lot simpler and omits the various script operands that you see in P2SH scripts. Instead, the Segregated Witness program consists of two values pushed to the stack: a witness version (0) and the 32-byte SHA256 hash of the redeem script.

再一次，就像P2WPKH的例子一样，你可以看到，隔离见证等同脚本要简单得多，省略了各种你在P2SH脚本中见到的脚本操作符。相反，隔离见证程序仅包含两个推送到堆栈的值：一个见证版本（0）和一个32字节的赎回脚本的哈希值。

Tip	While P2SH uses the 20-byte RIPEMD160(SHA256(script)) hash, the P2WSH witness program uses a 32-byte SHA256(script) hash. This difference in the selection of the hashing algorithm is deliberate and is used to differentiate between the two types of witness programs (P2WPKH and P2WSH) by the length of the hash and to provide stronger security to P2WSH (128 bits versus 80 bits of P2SH).
小贴士	当P2SH使用20字节的RIPEMD160(SHA256(script)) 哈希值时，P2WSH见证程序使用了一个32字节的SHA256（脚本）哈希值。在选择哈希算法时，这一差异是有意为之，被用于通过哈希值长度来区分两种类型的见证程序（P2WPKH and P2WSH），并为P2WSH（128位 而不是 80 位P2SH）提供更强的安全性。
Mohammed’s company can spend outputs the P2WSH output by presenting the correct redeem script and sufficient signatures to satisfy it. Both the redeem script and the signatures would be segregated outside the spending transaction as part of the witness data. Within the transaction input, Mohammed’s wallet would put an empty scriptSig:

穆罕默德的公司可以通过提供正确的赎回脚本和足够的签名满足并花出P2WSH输出。作为见证数据的一部分，赎回脚本和签名被隔离在此支出交易之外。在交易输入内部，穆罕默德的钱包会防止一个空的scriptSig：

Decoded transaction showing a P2WSH output being spent with separate witness data

显示了一个P2WSH输出被用隔离见证数据花出的解码交易：

1
2
3
4
5
6
7
8
9
[...]
“Vin” : [
"txid": "abcdef12345...",
"vout": 0,
     	 "scriptSig": “”,
]
[...]
“witness”: “<SigA> <SigB> <2 PubA PubB PubC PubD PubE 5 CHECKMULTISIG>”
[...]
Differentiating between P2WPKH and P2WSH

区分P2WPKH和P2WSH

In the previous two sections, we demonstrated two types of witness programs: Pay-to-Witness-Public-Key-Hash (P2WPKH)and Pay-to-Witness-Script-Hash (P2WSH). Both types of witness programs consist of a single byte version number followed by a longer hash. They look very similar, but are interpreted very differently: one is interpreted as a public key hash, which is satisfied by a signature and the other as a script hash, which is satisfied by a redeem script. The critical difference between them is the length of the hash:

在前面的两节中，我们展示了两种类型的检证程序：支付给见证公钥哈希 (P2WPKH)和 支付给见证脚本哈希(P2WSH) 。这两种检证程序都有一个字节版本号和一个跟随其后的更长的哈希值组成。它们看起来非常相似，但是被解释得非常不同：一个被解释为一个公钥哈希值，它被一个签名所满足，另一个被解释为一个脚本哈希值，它被一个赎回脚本所满足。他们之间的关键区别是哈希值的长度：

The public key hash in P2WPKH is 20 bytes
P2WPKH中的公钥哈希值是20字节
The script hash in P2WSH is 32 bytes
P2WSH中的脚本哈希值是32字节
This is the one difference that allows a wallet to differentiate between the two types of witness programs. By looking at the length of the hash, a wallet can determine what type of witness program it is, P2WPKH or P2WSH. 这个区别使得钱包可以对这两种类型的见证程序进行区分。通过查看哈希值的长度，钱包可以确定它是什么类型的见证程序，P2WPKH 或者 P2WSH。

### Upgrading to Segregated Witness

### 隔离见证升级

As we can see from the previous examples, upgrading to Segregated Witness is a two-step process. First, wallets must create special segwit type outputs. Then, these outputs can be spent by wallets that know how to construct Segregated Witness transactions. In the examples, Alice’s wallet was segwit-aware and able to create special outputs with Segregated Witness scripts. Bob’s wallet is also segwit-aware and able to spend those outputs. What may not be obvious from the example is that in practice, Alice’s wallet needs to *know* that Bob uses a segwit-aware wallet and can spend these outputs. Otherwise, if Bob’s wallet is not upgraded and Alice tries to make segwit payments to Bob, Bob’s wallet will not be able to detect these payments.

正如我们前面看到的例子，隔离见证的升级需要经过两步过程。首先，钱包必须创建特殊的隔离型输出。然后，这些输出可以被知道如何构建隔离见证事务的钱包花费。在这些例子中，爱丽丝的钱包是segwit意识到的，并且能够使用Segregated Witness脚本创建特殊输出。鲍勃的钱包也是segwit意识到，并能够花这些输出。从这个例子中可能不明显的是，在实践中，爱丽丝的钱包需要知道Bob使用了一个支持segwit的钱包，并可以使用这些输出。否则，如果鲍勃的钱包没有升级，并且Alice试图对Bob进行分段付款，那么鲍勃的钱包将无法检测到这些付款。

| Tip  | For P2WPKH and P2WSH payment types, both the sender and the recipient wallets need to be upgraded to be able to use segwit. Furthermore, the sender’s wallet needs to know that the recipient’s wallet is segwit-aware. |
| ---- | ---------------------------------------- |
|      |                                          |

| 提示  | 对于P2WPKH和P2WSH付款类型，发件人和收件人钱包都需要升级才能使用segwit。此外，发件人的钱包需要知道收件人的钱包是否具有隔离识别功能。|
| ---- | ---------------------------------------- |
|      |                                          |


Segregated Witness will not be implemented simultaneously across the entire network. Rather, Segregated Witness is implemented as a backward-compatible upgrade, where *old and new clients can coexist*. Wallet developers will independently upgrade wallet software to add segwit capabilities. The P2WPKH and P2WSH payment types are used when both sender and recipient are segwit-aware. The traditional P2PKH and P2SH will continue to work for nonupgraded wallets. That leaves two important scenarios, which are addressed in the next section:

隔离见证不会在整个网络中同时实施。相反，隔离见证被实施为向后兼容的升级，其中*新老客户可以共存*。钱包开发人员将独立升级钱包软件以添加隔离区功能。当发件人和收件人都可以感知到网志时，使用P2WPKH和P2WSH付款类型。传统的P2PKH和P2SH将继续为非升级的钱包工作。这留下了两个重要的情况，下一节将讨论这个情况：

- Ability of a sender’s wallet that is not segwit-aware to make a payment to a recipient’s wallet that can process segwit transactions

- 发件人的钱包的能力，不是segwit意识到付款的收件人的钱包，可以处理segwit交易

- Ability of a sender’s wallet that is segwit-aware to recognize and distinguish between recipients that are segwit-aware and ones that are not, by their *addresses*

- 具有隔离识别功能的发件人钱包能够识别和区分具有隔离识别功能的收件人和不是*他们*地址的收件人*

#### Embedding Segregated Witness inside P2SH

#### 在P2SH中嵌入隔离见证

Let’s assume, for example, that Alice’s wallet is not upgraded to segwit, but Bob’s wallet is upgraded and can handle segwit transactions. Alice and Bob can use "old" non-segwit transactions. But Bob would likely want to use segwit to reduce transaction fees, taking advantage of the discount that applies to witness data.

举个例子，假设爱丽丝的钱包没有升级到segwit，但是鲍勃的钱包已经升级，可以处理segwit交易。爱丽丝和鲍勃可以使用“旧”非segwit交易。但是鲍勃很可能会使用segwit来降低交易费用，利用适用于目击者数据的折扣。

In this case Bob’s wallet can construct a P2SH address that contains a segwit script inside it. Alice’s wallet sees this as a "normal" P2SH address and can make payments to it without any knowledge of segwit. Bob’s wallet can then spend this payment with a segwit transaction, taking full advantage of segwit and reducing transaction fees.

在这种情况下，鲍勃的钱包可以构建一个包含一个segwit脚本的P2SH地址。爱丽丝的钱包认为这是一个“正常的”P2SH地址，并可以在没有任何segwit的知识的情况下付款。然后，鲍勃的钱包可以通过隔离交易来支付这笔款项，充分利用隔离交易并降低交易费用。

Both forms of witness scripts, P2WPKH and P2WSH, can be embedded in a P2SH address. The first is noted as P2SH(P2WPKH) and the second is noted as P2SH(P2WSH).

两种形式的见证脚本P2PKH和P2WSH都可以嵌入到P2SH地址中。第一个是P2SH（P2PKH），第二个是P2SH（P2WSH）。

#### Pay-to-Witness-Public-Key-Hash inside Pay-to-Script-Hash

#### 在 Pay-to-Script-Hash 中的 Pay-to-Witness-Public-Key-Hash

The first form of witness script we will examine is P2SH(P2WPKH). This is a Pay-to-Witness-Public-Key-Hash witness program, embedded inside a Pay-to-Script-Hash script, so that it can be used by a wallet that is not aware of segwit.

见证脚本的第一种形式是P2SH（P2WPKH）。这是一个Pay-to-Witness-Public-Key-Hash证明程序，嵌入在Pay-to-Script-Hash脚本中，所以它可以被不知道segwit的钱包使用。

Bob’s wallet constructs a P2WPKH witness program with Bob’s public key. This witness program is then hashed and the resulting hash is encoded as a P2SH script. The P2SH script is converted to a bitcoin address, one that starts with a "3," as we saw in the [[p2sh\]](https://github.com/bitcoinbook/bitcoinbook/blob/second_edition/appdx-segwit.asciidoc#p2sh) section.

鲍勃的钱包用鲍勃的公钥构造了一个P2WPKH证人程序。这个见证程序然后被散列，结果散列被编码为P2SH脚本。P2SH脚本被转换成比特币地址，一个以“3”开始的地址，正如我们在[[p2sh\]](https://github.com/bitcoinbook/bitcoinbook/blob/second_edition/appdx-segwit.asciidoc#p2sh) 部分看到的那样。

Bob’s wallet starts with the P2WPKH witness program we saw earlier:

鲍勃的钱包从我们之前看到的P2WPKH 见证程序开始：

Bob’s P2WPKH witness program

鲍勃的见证程序：

```
0 ab68025513c3dbd2f7b92a94e0581f5d50f654e7
```

The P2WPKH witness program consists of the witness version and Bob’s 20-byte public key hash.

P2WPKH证人程序由证人版本和鲍勃的20字节公钥散列组成。

Bob’s wallet then hashes the preceding witness program, first with SHA256, then with RIPEMD160, producing another 20-byte hash:

鲍勃的钱包然后散列之前的见证程序，先用SHA256，然后用RIPEMD160，产生另一个20字节的散列：

HASH160 of the P2WPKH witness program

P2WPKH见证程序的HASH160

```
3e0547268b3b19288b3adef9719ec8659f4b2b0b
```

The hash of the witness program is then embedded in a P2SH script:

然后见证程序的hash嵌入到P2SH脚本中：

```
HASH160 3e0547268b3b19288b3adef9719ec8659f4b2b0b EQUAL
```

Finally, the P2SH script is converted to a P2SH bitcoin address:

最后，P2SH脚本转换为P2SH比特币地址：

P2SH address

P2SH地址

```
37Lx99uaGn5avKBxiW26HjedQE3LrDCZru
```

Now, Bob can display this address for customers to pay for their coffee. Alice’s wallet can make a payment to 3deadbeef, just as it would to any other bitcoin address. Even though Alice’s wallet has no support for segwit, the payment it creates can be spent by Bob with a segwit transaction.

现在，鲍勃可以显示这个地址给顾客付钱买咖啡。爱丽丝的钱包可以支付给3deadbeef，就像任何其他比特币地址一样。尽管爱丽丝的钱包不支持segwit，但它创建的付款可以由鲍勃使用segwit交易进行支付。

#### Pay-to-Witness-Script-Hash inside Pay-to-Script-Hash

#### Pay-to-Witness-Script-Hash 在 Pay-to-Script-Hash 内

Similarly, a P2WSH witness program for a multisig script or other complicated script can be embedded inside a P2SH script and address, making it possible for any wallet to make payments that are segwit compatible.

同样，一个用于multisig脚本或其他复杂脚本的P2WSH见证程序可以嵌入到P2SH脚本和地址中，使得任何钱包都可以进行与segwit兼容的支付。

As we saw in [Pay-to-Witness-Script-Hash (P2WSH)](https://github.com/bitcoinbook/bitcoinbook/blob/second_edition/appdx-segwit.asciidoc#p2wsh), Mohammed’s company is using Segregated Witness payments to multisignature scripts. To make it possible for any client to pay his company, regardless of whether their wallets are upgraded for segwit, Mohammed’s wallet can embed the P2WSH witness program inside a P2SH script.

正如我们在[Pay-to-Witness-Script-Hash（P2WSH）](https://github.com/bitcoinbook/bitcoinbook/blob/second_edition/appdx-segwit.asciidoc#p2wsh)中看到的，穆罕默德的公司正在使用隔离见证 对多重签名脚本的付款。为了让任何客户支付他的公司，无论他们的钱包是否升级为隔离开关，穆罕默德的钱包都可以在P2SH脚本中嵌入P2WSH证人程序。

First, Mohammed’s wallet creates the P2WSH witness program that corresponds to the multisignature script, hashed with SHA256:

首先，穆罕默德的钱包创建与多重签名脚本对应的P2WSH见证程序，并用SHA256进行哈希处理：

Mohammed’s wallet creates a P2WSH witness program

穆罕默德的钱包创建了P2WSH见证程序

```
0 9592d601848d04b172905e0ddb0adde59f1590f1e553ffc81ddc4b0ed927dd73
```

Then, the witness program itself is hashed with SHA256 and RIPEMD160, producing a new 20-byte hash, as used in traditional P2SH:

然后，见证程序本身用SHA256和RIPEMD160散列，产生一个新的20字节散列，如传统的P2SH所使用的散列：

The HASH160 of the P2WSH witness program

P2WSH见证程序的HASH160

```
86762607e8fe87c0c37740cddee880988b9455b2
```

Next, Mohammed’s wallet puts the hash into a P2SH script:

接下来，穆罕默德的钱包将哈希码放入P2SH脚本中：

```
HASH160 86762607e8fe87c0c37740cddee880988b9455b2 EQUAL
```

Finally, the wallet constructs a bitcoin address from this script:

最后，钱包从这个脚本构造一个比特币地址：

P2SH bitcoin address

P2SH比特币地址

```
3Dwz1MXhM6EfFoJChHCxh1jWHb8GQqRenG
```

Now, Mohammed’s clients can make payments to this address without any need to support segwit. Mohammed’s company can then construct segwit transactions to spend these payments, taking advantage of segwit features including lower transaction fees.

现在，穆罕默德的客户可以付款到这个地址，而不需要支持segwit。然后，穆罕默德的公司可以构建隔离交易来支付这些款项，利用包括较低的交易费用在内的隔离功能。

#### Segregated Witness addresses

#### 隔离见证地址

After segwit is deployed on the bitcoin network, it will take some time until wallets are upgraded. It is quite likely therefore that segwit will mostly be used embedded in P2SH, as we saw in the previous section, at least for several months.

在将比特币部署在比特币网络之后，钱包升级之前需要一些时间。因此，很可能像我们在前一节中看到的那样，segwit将主要用于嵌入到P2SH中，至少几个月。

Eventually, however, almost all wallets will be able to support segwit payments. At that time it will no longer be necessary to embed segwit in P2SH. It is therefore likely that a new form of bitcoin address will be created, one that indicates the recipient is segwit-aware and that directly encodes a witness program. There have been a number of proposals for a Segregated Witness address scheme, but none have been actively pursued.

但是，最终几乎所有的钱包都能够支持隔离支付。那时就不再需要在P2SH中嵌入segwit。因此，可能会创建一种新的比特币地址形式，表明接收者是具有隔离意识的，并直接对证人程序进行编码。有关隔离见证人地址计划的建议有很多，但没有一个被积极推行。

#### Transaction identifiers

#### 事务标识符

One of the greatest benefits of Segregated Witness is that it eliminates third-party transaction malleability.

隔离见证的最大好处之一就是消除了第三方交易延展性。

Before segwit, transactions could have their signatures subtly modified by third parties, changing their transaction ID (hash) without changing any fundamental properties (inputs, outputs, amounts). This created opportunities for denial-of-service attacks as well as attacks against poorly written wallet software that assumed unconfirmed transaction hashes were immutable.

在segwit之前，交易可以通过第三方微妙地修改其签名，在不改变任何基本属性（输入，输出，金额）的情况下更改其交易ID（散列）。这为拒绝服务攻击创造了机会，以及攻击了编写不好的钱包软件，这些软件假定未经证实的事务哈希是不可变的。

With the introduction of Segregated Witness, transactions have two identifiers, txid and wtxid. The traditional transaction ID txid is the double-SHA256 hash of the serialized transaction, without the witness data. A transaction wtxid is the double-SHA256 hash of the new serialization format of the transaction with witness data.

通过引入隔离见证，事务有两个标识符txid和wtxid。传统的事务ID txid是序列化事务的双SHA256散列，没有见证数据。事务wtxid是具有见证数据的事务的新序列化格式的双SHA256散列。

The traditional txid is calculated in exactly the same way as with a nonsegwit transaction. However, since the segwit transaction has empty scriptSigs in every input, there is no part of the transaction that can be modified by a third party. Therefore, in a segwit transaction, the txid is immutable by a third party, even when the transaction is unconfirmed.

传统txid的计算方式与nonsegwit事务完全相同。但是，由于segwit事务在每个输入中都有空的scriptSigs，因此没有可由第三方修改的部分事务。因此，在隔离交易中，即使交易未经确认，txid也是第三方不可改变的。

The wtxid is like an "extended" ID, in that the hash also incorporates the witness data. If a transaction is transmitted without witness data, then the wtxid and txid are identical. Note than since the wtxid includes witness data (signatures) and since witness data may be malleable, the wtxid should be considered malleable until the transaction is confirmed. Only the txid of a segwit transaction can be considered immutable by third parties and only if *all* the inputs of the transaction are segwit inputs.

wtxid就像一个“扩展的”ID，因为hash也包含了见证数据。如果交易没有目击者数据传输，则wtxid和txid是相同的。注意，由于wtxid包含目击者数据（签名），并且由于目击者数据可能具有延展性，所以在交易确认之前，wtxid应该被认为是可延展的。只有当交易的输入是segwit输入时，第三方才可以认定segwit交易的txid不可变。

| Tip  | Segregated Witness transactions have two IDs: txid and wtxid. The txid is the hash of the transaction without the witness data and the wtxid is the hash inclusive of witness data. The txid of a transaction where all inputs are segwit inputs is not susceptible to third-party transaction malleability. |
| ---- | ---------------------------------------- |
|      |                                          |

| 小贴士  | 隔离见证事务有两个ID：txid和wtxid。txid是没有见证数据的交易的散列，wtxid是包含证人数据的散列。所有输入为隔离开关输入的交易不受第三方交易延展性影响。|
| ---- | ---------------------------------------- |
|      |                                          |

### Segregated Witness' New Signing Algorithm

### 隔离见证新的签名算法

Segregated Witness modifies the semantics of the four signature verification functions (CHECKSIG, CHECKSIGVERIFY, CHECKMULTISIG, and CHECKMULTISIGVERIFY), changing the way a transaction commitment hash is calculated.

隔离见证修改了四个签名验证函数（CHECKSIG，CHECKSIGVERIFY，CHECKMULTISIG和CHECKMULTISIGVERIFY）的语义，改变了交易承诺散列的计算方式。

Signatures in bitcoin transactions are applied on a *commitment hash*, which is calculated from the transaction data, locking specific parts of the data indicating the signer’s commitment to those values. For example, in a simple SIGHASH_ALL type signature, the commitment hash includes all inputs and outputs.

比特币交易中的签名应用于*交易哈希*，交易数据计算，锁定数据的特定部分，表明签名者对这些值的承诺。例如，在简单的SIGHASH_ALL类型签名中，承诺哈希包括所有的输入和输出。

Unfortunately, the way the commitment hash was calculated introduced the possibility that a node verifying the signature can be forced to perform a significant number of hash computations. Specifically, the hash operations increase in O(n2) with respect to the number of signature operations in the transaction. An attacker could therefore create a transaction with a very large number of signature operations, causing the entire bitcoin network to have to perform hundreds or thousands of hash operations to verify the transaction.

不幸的是，计算承诺哈希的方式引入了验证签名的节点可能被迫执行大量哈希计算的可能性。具体而言，散列运算相对于事务中的签名操作的数量增加O（n2）。因此，攻击者可以通过大量的签名操作创建一个交易，导致整个比特币网络不得不执行数百或数千个哈希操作来验证交易。

Segwit represented an opportunity to address this problem by changing the way the commitment hash is calculated. For segwit version 0 witness programs, signature verification occurs using an improved commitment hash algorithm as specified in BIP-143.

Segwit代表了通过改变承诺散列计算方式来解决这个问题的机会。对于segwit版本0见证程序，使用BIP-143中规定的改进的承诺哈希算法进行签名验证。

The new algorithm achieves two important goals. Firstly, the number of hash operations increases by a much more gradual O(n) to the number of signature operations, reducing the opportunity to create denial-of-service attacks with overly complex transactions. Secondly, the commitment hash now also includes the value (amounts) of each input as part of the commitment. This means that a signer can commit to a specific input value without needing to "fetch" and check the previous transaction referenced by the input. In the case of offline devices, such as hardware wallets, this greatly simplifies the communication between the host and the hardware wallet, removing the need to stream previous transactions for validation. A hardware wallet can accept the input value "as stated" by an untrusted host. Since the signature is invalid if that input value is not correct, the hardware wallet doesn’t need to validate the value before signing the input.

新算法实现了两个重要目标。首先，散列操作的数量比签名操作的数量增加了一个更加渐进的O（n），减少了用过于复杂的事务创建拒绝服务攻击的机会。其次，承诺散列现在还包括作为承诺的一部分的每个输入的值（金额）。这意味着签名者可以提交特定的输入值，而不需要“获取”并检查输入引用的前一个事务。在离线设备（如硬件钱包）的情况下，这极大地简化了主机与硬件钱包之间的通信，消除了对以前的事务流进行验证的需要。硬件钱包可以接受不可信主机“输入”的输入值。由于签名是无效的，如果输入值不正确，硬件钱包在签名输入之前不需要验证该值。

### Economic Incentives for Segregated Witness

### 隔离见证的经济激励

Bitcoin mining nodes and full nodes incur costs for the resources used to support the bitcoin network and the blockchain. As the volume of bitcoin transactions increases, so does the cost of resources (CPU, network bandwidth, disk space, memory). Miners are compensated for these costs through fees that are proportional to the size (in bytes) of each transaction. Nonmining full nodes are not compensated, so they incur these costs because they have a need to run an authoritative fully validating full-index node, perhaps because they use the node to operate a bitcoin business.

比特币挖掘节点和完整节点会产生用于支持比特币网络和区块链的资源的成本。随着比特币交易量的增加，资源成本（CPU，网络带宽，磁盘空间，内存）也在增加。矿工通过与每次交易的大小（字节）成比例的费用来补偿这些成本。Nonmining完整的节点没有得到补偿，所以他们承担这些成本，因为他们需要运行一个权威的充分验证全索引节点，可能是因为他们使用节点操作比特币业务。

Without transaction fees, the growth in bitcoin data would arguably increase dramatically. Fees are intended to align the needs of bitcoin users with the burden their transactions impose on the network, through a market-based price discovery mechanism.

如果没有交易费用，比特币数据的增长可能会大幅增加。费用旨在通过基于市场的价格发现机制，使比特币用户的需求与交易对网络带来的负担相一致。

The calculation of fees based on transaction size treats all the data in the transaction as equal in cost. But from the perspective of full nodes and miners, some parts of a transaction carry much higher costs. Every transaction added to the bitcoin network affects the consumption of four resources on nodes:

基于交易规模的费用计算将交易中的所有数据视为相同的成本。但是从完整节点和矿工的角度来看，交易的某些部分的成本要高得多。加入比特币网络的每笔交易都会影响节点上四种资源的消耗：

- Disk Space

- 磁盘空间

  Every transaction is stored in the blockchain, adding to the total size of the blockchain. The blockchain is stored on disk, but the storage can be optimized by “pruning” older transactions.

  每笔交易都存储在区块链中，并添加到区块链的总大小中。区块链存储在磁盘上，但可以通过“修剪”较旧的事务来优化存储。

- CPU

- CPU

  Every transaction must be validated, which requires CPU time.

  每个事务都必须经过验证，这需要CPU时间。

- Bandwidth

- 带宽

  Every transaction is transmitted (through flood propagation) across the network at least once. Without any optimization in the block propagation protocol, transactions are transmitted again as part of a block, doubling the impact on network capacity.

  每笔交易至少通过网络传输一次（通过泛洪传播）。如果在块传播协议中没有任何优化，事务将作为块的一部分再次传输，从而将对网络容量的影响加倍。

- Memory

- 内存

  Nodes that validate transactions keep the UTXO index or the entire UTXO set in memory to speed up validation. Because memory is at least one order of magnitude more expensive than disk, growth of the UTXO set contributes disproportionately to the cost of running a node.

  验证交易的节点将UTXO索引或整个UTXO设置在内存中，以加速验证。由于内存至少比磁盘贵一个数量级，所以UTXO集的增长对运行节点的成本贡献不成比例。

As you can see from the list, not every part of a transaction has an equal impact on the cost of running a node or on the ability of bitcoin to scale to support more transactions. The most expensive part of a transaction are the newly created outputs, as they are added to the in-memory UTXO set. By comparison, signatures (aka witness data) add the least burden to the network and the cost of running a node, because witness data are only validated once and then never used again. Furthermore, immediately after receiving a new transaction and validating witness data, nodes can discard that witness data. If fees are calculated on transaction size, without discriminating between these two types of data, then the market incentives of fees are not aligned with the actual costs imposed by a transaction. In fact, the current fee structure actually encourages the opposite behavior, because witness data is the largest part of a transaction.

从列表中可以看出，并不是交易的每个部分都对运行节点的成本或者比特币支持更多交易的能力产生同等的影响。交易中最昂贵的部分是新创建的输出，因为它们被添加到内存中的UTXO集。相比之下，签名（又名见证数据）为网络增加了最小的负担，并且节省了运行节点的成本，因为目击者数据只被验证一次，之后又不再使用。此外，在收到新的交易并验证目击者数据之后，节点可以立即丢弃该目击者数据。如果按照交易规模计算费用，而不区分这两种数据，那么市场上的收费激励就不符合交易实际成本。事实上，目前的费用结构实际上鼓励了相反的行为，因为目击者数据是交易的最大部分。

The incentives created by fees matter because they affect the behavior of wallets. All wallets must implement some strategy for assembling transactions that takes into consideration a number of factors, such as privacy (reducing address reuse), fragmentation (making lots of loose change), and fees. If the fees are overwhelmingly motivating wallets to use as few inputs as possible in transactions, this can lead to UTXO picking and change address strategies that inadvertently bloat the UTXO set.

费用所产生的激励因为它们影响钱包的行为。所有的钱包都必须实行一些策略来组合交易，这些策略要考虑到隐私（减少地址重复使用），碎片化（大量松动）以及收费等因素。如果费用压倒性地促使钱包在交易中使用尽可能少的投入，这可能导致UTXO选择和改变地址策略，从而不经意地膨胀UTXO集合。

Transactions consume UTXO in their inputs and create new UTXO with their outputs. A transaction, therefore, that has more inputs than outputs will result in a decrease in the UTXO set, whereas a transaction that has more outputs than inputs will result in an increase in the UTXO set. Let’s consider the *difference* between inputs and outputs and call that the “Net-new-UTXO.” That’s an important metric, as it tells us what impact a transaction will have on the most expensive network-wide resource, the in-memory UTXO set. A transaction with positive Net-new-UTXO adds to that burden. A transaction with a negative Net-new-UTXO reduces the burden. We would therefore want to encourage transactions that are either negative Net-new-UTXO or neutral with zero Net-new-UTXO.

事务在其输入中消耗UTXO，并用它们的输出创建新的UTXO。因此，交易比输出多的输入将导致UTXO集合的减少，而输出多于输入的交易将导致UTXO集合的增加。让我们考虑输入和输出之间的差异*，并称之为“Net-new-UTXO”。这是一个重要的指标，因为它告诉我们一个事务对最昂贵的全网资源，内存 UTXO设置。积极的Net-new-UTXO交易增加了这一负担。负Net-new-UTXO的交易减轻了负担。因此，我们希望鼓励交易是负Net-new-UTXO或零Net-new-UTXO的中立。

Let’s look at an example of what incentives are created by the transaction fee calculation, with and without Segregated Witness. We will look at two different transactions. Transaction A is a 3-input, 2-output transaction, which has a Net-new-UTXO metric of –1, meaning it consumes one more UTXO than it creates, reducing the UTXO set by one. Transaction B is a 2-input, 3-output transaction, which has a Net-new-UTXO metric of 1, meaning it adds one UTXO to the UTXO set, imposing additional cost on the entire bitcoin network. Both transactions use multisignature (2-of-3) scripts to demonstrate how complex scripts increase the impact of segregated witness on fees. Let’s assume a transaction fee of 30 satoshi per byte and a 75% fee discount on witness data:

让我们来看一个例子，说明交易费用计算产生了哪些激励措施，有无隔离见证。我们将看两个不同的交易。交易A是3输入2输出的交易，其具有-1的净新UTXO度量，这意味着它消耗比它创建的多一个UTXO，将UTXO减1。交易B是2输入3输出的交易，其具有1的净新UTXO度量，这意味着它向UTXO集增加一个UTXO，对整个比特币网络施加额外的成本。这两个交易都使用多重签名（2/3）脚本来演示复杂脚本如何增加隔离证人对费用的影响。我们假设交易费为每字节30 satoshi，证据数据为75％的折扣费用：

- Without Segregated Witness

- 未使用隔离见证

  Transaction A fee: 25,710 satoshiTransaction B fee: 18,990 satoshi

  交易费用：25,710 satoshi 交易B费用：18,990 satoshi

- With Segregated Witness

- 使用隔离见证

  Transaction A fee: 8,130 satoshiTransaction B fee: 12,045 satoshi
  交易手续费：8,130 satoshi 交易B手续费：12,045 satoshi

Both transactions are less expensive when segregated witness is implemented. But comparing the costs between the two transactions, we see that before Segregated Witness, the fee is higher for the transaction that has a negative Net-new-UTXO. After Segregated Witness, the transaction fees align with the incentive to minimize new UTXO creation by not inadvertently penalizing transactions with many inputs.

当隔离证人实施时，这两个交易都较为便宜。但是比较这两笔交易的成本，我们发现在隔离见证之前，交易净收益为负的净新UTXO的收费更高。在隔离见证之后，交易费用与最小化新的UTXO创造的激励相一致，而不会无意中惩罚许多输入的交易。

Segregated Witness therefore has two main effects on the fees paid by bitcoin users. Firstly, segwit reduces the overall cost of transactions by discounting witness data and increasing the capacity of the bitcoin blockchain. Secondly, segwit’s discount on witness data corrects a misalignment of incentives that may have inadvertently created more bloat in the UTXO set.

因此，隔离见证对比特币用户支付的费用有两个主要的影响。首先，segwit通过折扣目击者数据和增加比特币区块链的能力来降低交易的总体成本。其次，segwit对证人数据的折扣纠正了可能无意中在UTXO集合中产生更多膨胀的激励的错位。


### 原文

[Appendix A: Segregated Witness](https://github.com/bitcoinbook/bitcoinbook/blob/second_edition/appdx-segwit.asciidoc)

### 参考

[隔离见证是怎么回事？](http://www.jianshu.com/p/94ad9f6f34ba)

[你知道什么是隔离见证（SegWit）吗？](http://chainb.com/?P=Cont&id=4219)

[隔离见证实施：为什么比特币容量没有显著增加？](https://steemit.com/cn/@btsabc/2gvjbm)

[隔离见证的风险: 为可能破坏比特币网络的挖矿巨头打开了大门](https://nchain.com/zh/blog/%E9%9A%94%E7%A6%BB%E8%A7%81%E8%AF%81%E7%9A%84%E9%A3%8E%E9%99%A9-%E4%B8%BA%E5%8F%AF%E8%83%BD%E7%A0%B4%E5%9D%8F%E6%AF%94%E7%89%B9%E5%B8%81%E7%BD%91%E7%BB%9C%E7%9A%84%E6%8C%96%E7%9F%BF%E5%B7%A8%E5%A4%B4/)

[比特币——不要让隔离见证协议改变你的本质](http://www.shellsec.com/news/48894.html)

[Consensus 2017 比特币扩容协议：隔离见证和2MB扩容将同时进行部署](http://www.8btc.com/bitcoin-scaling-agreement-at-consensus-2017)

[知乎 - 什么是隔离见证](https://www.zhihu.com/question/58567061)

[为什么我们不支持隔离见证](http://www.jianshu.com/p/286b28323b45)
