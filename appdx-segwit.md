# 附录A 隔离见证

原文参见 [Appendix A: Segregated Witness](https://github.com/bitcoinbook/bitcoinbook/blob/second_edition/appdx-segwit.asciidoc)

隔离见证Segregated Witness（通常简写为SegWit）是对比特币软件提出的一种共识规则和网络协议的更新，通过BIP-9软分叉技术，计划在2017年中期激活。

在密码学中，术语“见证”用于描述加密难题的解决方案。在比特币术语中，见证含有一个加密条件放在一个未被使用的交易输出（UTXO）上的意思。

在比特币的背景下，数字签名是一种见证，但见证更为广泛，可以满足强加在UTXO上的条件并解锁UTXO的支出。 术语“见证”是“解锁脚本”或“scriptSig”的一般术语。

在segwit之前，交易中的每一个输入都跟随着解锁它的见证数据。 见证数据作为每个输入的一部分嵌入在事务中。术语隔离见证Segregated Witness，或者简写为segwit，只是意味着分离特定输出的签名或解锁脚本。可以把 "separate scriptSig," 或者 “separate signature” 看成是最简单的形式。

因此，隔离见证是对比特币的架构性更改，旨在将交易的scriptSig（解锁脚本）字段中的见证数据移动到与交易相伴随的单独的见证数据结构中。客户可以请求具有或不附带见证数据的交易数据。



### 参考

[你知道什么是隔离见证（SegWit）吗？](http://chainb.com/?P=Cont&id=4219)

[隔离见证实施：为什么比特币容量没有显著增加？](https://steemit.com/cn/@btsabc/2gvjbm)

[隔离见证的风险: 为可能破坏比特币网络的挖矿巨头打开了大门](https://nchain.com/zh/blog/%E9%9A%94%E7%A6%BB%E8%A7%81%E8%AF%81%E7%9A%84%E9%A3%8E%E9%99%A9-%E4%B8%BA%E5%8F%AF%E8%83%BD%E7%A0%B4%E5%9D%8F%E6%AF%94%E7%89%B9%E5%B8%81%E7%BD%91%E7%BB%9C%E7%9A%84%E6%8C%96%E7%9F%BF%E5%B7%A8%E5%A4%B4/)

[比特币——不要让隔离见证协议改变你的本质](http://www.shellsec.com/news/48894.html)

[Consensus 2017 比特币扩容协议：隔离见证和2MB扩容将同时进行部署](http://www.8btc.com/bitcoin-scaling-agreement-at-consensus-2017)

[知乎 - 什么是隔离见证](https://www.zhihu.com/question/58567061)

[为什么我们不支持隔离见证](http://www.jianshu.com/p/286b28323b45)
