Bitcore是BitPay提供的一套工具。 其目标是为Bitcoin开发人员提供易于使用的工具。 几乎所有的Bitcore的代码都是用JavaScript编写的。 有一些专门为NodeJS编写的模块。 最后，Bitcore的“节点”模块包括Bitcoin Core的C ++代码。 有关详细信息，请参阅[https://bitcore.io](https://bitcore.io/)。

# Bitcore的功能列表

Bitcoin full node \(bitcore-node\)  
Block explorer \(insight\)  
Block, transaction, and wallet utilities \(bitcore-lib\)  
Communicating directly with Bitcoin’s P2P network \(bitcore-p2p\)  
Seed entropy mnemonic generation（种子熵助记符） \(bitcore-mnemonic\)  
Payment protocol \(bitcore-payment-protocol\)  
Message verification and signing \(bitcore-message\)  
Elliptic curve Integrated Encryption Scheme（椭圆曲线综合加密方案） \(bitcore-ecies\)  
Wallet service \(bitcore-wallet-service\)  
Wallet client \(bitcore-wallet-client\)  
Playground \(bitcore-playground\)  
Integrating services directly with Bitcoin Core \(bitcore-node\)

# Bitcore库示例

## 先决条件

NodeJS &gt;= 4.x 或者使用[hosted online playground](https://bitcore.io/playground)

如果使用NodeJS和节点REPL：

```
$ npm install -g bitcore-lib bitcore-p2p
$ NODE_PATH=$(npm list -g | head -1)/node_modules node
```

## 使用bitcore-lib的钱包示例

使用关联的私钥创建新的比特币地址：

```
> bitcore = require('bitcore-lib')
> privateKey = new bitcore.PrivateKey()
> address = privateKey.toAddress().toString()
```

创建分层确定性私钥和地址：

```
> hdPrivateKey = bitcore.HDPrivateKey()
> hdPublicKey = bitcore.HDPublicKey(hdPrivateKey)
> hdAddress = new bitcore.Address(hdPublicKey.publicKey).toString()
```

从UTXO创建和签署交易：

```
> utxo = {
  txId: transaction id containing an unspent output,
  outputIndex: output indexi e.g. 0,
  address: addressOfUtxo,
  script: bitcore.Script.buildPublicKeyHashOut(addressOfUtxo).toString(),
  satoshis: amount sent to the address
}
> fee = 3000 //set appropriately for conditions on the network
> tx = new bitcore.Transaction()
        .from(utxo)
        .to(address, 35000)
        .fee(fee)
        .enableRBF()
        .sign(privateKeyOfUtxo)
```

替换mempool中的最后一个交易（替换费）：

```
> rbfTx = new Transaction()
           .from(utxo)
           .to(address, 35000)
           .fee(fee*2)
           .enableRBF()
           .sign(privateKeyOfUtxo);
> tx.serialize();
> rbfTx.serialize();
```

将交易广播到比特币网络（注意：仅广播有效交易;请参阅[https://bitnodes.21.co/nodes](https://bitnodes.21.co/nodes)）：  
将以下代码复制到名为broadcast.js的文件中。  
tx和rbfTx变量分别是tx.serialize（）和rbfTx.serialize（）的输出。  
为了更换费用，对等人必须支持bitcoind选项mempoolreplace并将其设置为1。  
运行文件节点broadcast.js：

```
var p2p = require('bitcore-p2p');
var bitcore = require('bitcore-lib');
var tx = new bitcore.Transaction('output from serialize function');
var rbfTx = new bitcore.Transaction('output from serialize function');
var host = 'ip address'; //use valid peer listening on tcp 8333
var peer = new p2p.Peer({host: host});
var messages = new p2p.Messages();
peer.on('ready', function() {
  var txs = [messages.Transaction(tx), messages.Transaction(rbfTx)];
  var index = 0;
  var interval = setInterval(function() {
    peer.sendMessage(txs[index++]);
    console.log('tx: ' + index + ' sent');
    if (index === txs.length) {
      clearInterval(interval);
      console.log('disconnecting from peer: ' + host);
      peer.disconnect();
    }
  }, 2000);
});
peer.connect();
```



