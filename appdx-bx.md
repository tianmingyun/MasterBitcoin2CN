# 附录7、比特币浏览器命令

Bitcoin Explorer（bx）是一个命令行工具，提供了各种用于密钥管理和交易构建的命令。 它是libbitcoin比特币库的一部分。

```
Usage: bx COMMAND [--help]


Info: The bx commands are:


address-decode
address-embed
address-encode
address-validate
base16-decode
base16-encode
base58-decode
base58-encode
base58check-decode
base58check-encode
base64-decode
base64-encode
bitcoin160
bitcoin256
btc-to-satoshi
ec-add
ec-add-secrets
ec-multiply
ec-multiply-secrets
ec-new
ec-to-address
ec-to-public
ec-to-wif
fetch-balance
fetch-header
fetch-height
fetch-history
fetch-stealth
fetch-tx
fetch-tx-index
hd-new
hd-private
hd-public
hd-to-address
hd-to-ec
hd-to-public
hd-to-wif
help
input-set
input-sign
input-validate
message-sign
message-validate
mnemonic-decode
mnemonic-encode
ripemd160
satoshi-to-btc
script-decode
script-encode
script-to-address
seed
send-tx
send-tx-node
send-tx-p2p
settings
sha160
sha256
sha512
stealth-decode
stealth-encode
stealth-public
stealth-secret
stealth-shared
tx-decode
tx-encode
uri-decode
uri-encode
validate-tx
watch-address
wif-to-ec
wif-to-public
wrap-decode
wrap-encode

```

更多信息参见[Bitcoin Explorer homepage](#) 和 [Bitcoin Explorer user documentation](#)

bx命令使用示例

我们来看一些使用Bitcoin Explorer命令来测试密钥和地址的例子。

使用种子命令生成随机“种子”值，该种子命令使用操作系统的随机数生成器。 将种子传递到ec-new命令以生成新的私钥。 我们将标准输出保存到文件private_key中：

```
$ bx seed | bx ec-new > private_key
$ cat private_key
73096ed11ab9f1db6135857958ece7d73ea7c30862145bcc4bbc7649075de474

```

现在，使用ec-to-public命令从私钥生成公钥。 我们将private_key文件传递到标准输入并将命令的标准输出保存到新文件public_key中：

```
$ bx ec-to-public < private_key > public_key
$ cat public_key
02fca46a6006a62dfdd2dbb2149359d0d97a04f430f12a7626dd409256c12be500

```

我们可以使用ec-to-address命令将public_key重新格式化为一个地址。 我们将public_key传递给标准输入：

```
$ bx ec-to-address < public_key
17re1S4Q8ZHyCP8Kw7xQad1Lr6XUzWUnkG

```

以这种方式产生的密钥产生零型非确定性钱包。 这意味着每个密钥都是由一个独立的种子生成的。 Bitcoin Explorer命令也可以根据BIP-32确定性地生成密钥。 在这种情况下，从种子创建“主”键，然后确定性地扩展以产生一个子项的树，从而产生一个2类确定性钱包。

首先，我们使用seed和hd-new命令生成一个主密钥，该密钥将被用作导出密钥层次结构的基础：

```
$ bx seed > seed
$ cat seed
eb68ee9f3df6bd4441a9feadec179ff1


$ bx hd-new < seed > master
$ cat master
xprv9s21ZrQH143K2BEhMYpNQoUvAgiEjArAVaZaCTgsaGe6LsAnwubeiTcDzd23mAoyizm9cApe51gNfLMkBqkYoWWMCRwzfuJk8RwF1SVEpAQ

```

我们现在使用hd-private命令在帐户中生成一个强化的“帐户”键和两个私钥序列：

```
$ bx hd-private --hard < master > account
$ cat account
xprv9vkDLt81dTKjwHB8fsVB5QK8cGnzveChzSrtCfvu3aMWvQaThp59ueufuyQ8Qi3qpjk4aKsbmbfxwcgS8PYbgoR2NWHeLyvg4DhoEE68A1n


$ bx hd-private --index 0 < account
xprv9xHfb6w1vX9xgZyPNXVgAhPxSsEkeRcPHEUV5iJcVEsuUEACvR3NRY3fpGhcnBiDbvG4LgndirDsia1e9F3DWPkX7Tp1V1u97HKG1FJwUpU


$ bx hd-private --index 1 < account
xprv9xHfb6w1vX9xjc8XbN4GN86jzNAZ6xHEqYxzbLB4fzHFd6VqCLPGRZFsdjsuMVERadbgDbziCRJru9n6tzEWrASVpEdrZrFidt1RDfn4yA3

```

接下来，我们使用hd-public命令来生成两个公钥的相应序列：

```
$ bx hd-public --index 0 < account
xpub6BH1zcTuktiFu43rUZ2gXqLgzu5F3tLEeTQ5t6iE3aQtM2VMTxMcyLN9fYHiGhGpQe9QQYmqL2eYPFJ3vezHz5wzaSW4FiGrseNDR4LKqTy


$ bx hd-public --index 1 < account
xpub6BH1zcTuktiFx6CzhPbGjG3UYQ13WR16CmtbPiagEKpEVtpyjshWyMaMV1cn7nUPUkgQHPVXJVqsrA8xWbGQDhohEcDFTEYMvYzwRD7Juf8

```

公钥也可以使用hd-to-public命令从其相应的私钥派生：

```
$ bx hd-private --index 0 < account | bx hd-to-public
xpub6BH1zcTuktiFu43rUZ2gXqLgzu5F3tLEeTQ5t6iE3aQtM2VMTxMcyLN9fYHiGhGpQe9QQYmqL2eYPFJ3vezHz5wzaSW4FiGrseNDR4LKqTy


$ bx hd-private --index 1 < account | bx hd-to-public
xpub6BH1zcTuktiFx6CzhPbGjG3UYQ13WR16CmtbPiagEKpEVtpyjshWyMaMV1cn7nUPUkgQHPVXJVqsrA8xWbGQDhohEcDFTEYMvYzwRD7Juf8

```

我们可以在确定性链中产生几乎无限数量的密钥，全部来源于单个种子。 这种技术用于许多钱包应用程序中以生成可以使用单个种子值进行备份和恢复的密钥。 每次创建一个新的密钥时，这比将其所有随机生成的密钥备份在一起更容易。

可以使用助记符编码命令对种子进行编码：

```
$ bx hd-mnemonic < seed > words
adore repeat vision worst especially veil inch woman cast recall dwell appreciate

```

然后可以使用mnemonic-decode命令对种子进行解码：

```
$ bx mnemonic-decode < words
eb68ee9f3df6bd4441a9feadec179ff1

```

助记符编码可以使种子更容易记录甚至记住。