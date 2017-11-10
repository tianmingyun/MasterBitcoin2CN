# 附录3、比特币改进建议（BIPs）

比特币改进提案是向比特币社区提供信息的设计文档，或用于描述比特币的新功能，流程或环境。

根据BIP-01也就是BIP目的和指南（BIP Purpose and Guidelines）的规定，有三种BIP：

标准类BIP

描述影响大多数或所有比特币实现的任何更改，例如网络协议的更改，区块或交易有效性规则的更改，或影响使用比特币的应用程序的互操作性的任何更改或附加。

信息类BIP

描述比特币设计问题，或向比特币社区提供一般准则或信息，但不提出新功能。信息类BIP不一定代表比特币社区的共识或建议，因此用户和实施者可以忽略信息类BIP或遵循他们的建议。

过程类BIP

描述一个比特币过程，或者提出一个过程的更改（或一个事件）。过程类BIP类似于标准类BIP，但适用于比特币协议本身以外的其他领域。他们可能会提出一个实现，但不是比特币的代码库;他们经常需要社区的共识;与信息类BIP不同，它们不仅仅是建议，用户通常也不能随意忽略它们。例如包括程序，指南，决策过程的变化以及对比特币开发中使用的工具或环境的更改。任何元BIP也被视为一个过程BIP。

BIP记录在GitHub上的版本化存储库中：[https://github.com/bitcoin/bips。](#) 下表BIP的快照显示在2017年4月BIP的快照。了解有关现有BIP及其内容的最新信息请咨询权威机构。

| BIP#         | Title                                    | Owner                                    | Type          | Status               |
| ------------ | ---------------------------------------- | ---------------------------------------- | ------------- | -------------------- |
| [BIP-1](#)   | BIP Purpose and Guidelines               | Amir Taaki                               | Process       | Replaced             |
| [BIP-2](#)   | BIP process, revised                     | Luke Dashjr                              | Process       | Active               |
| [BIP-8](#)   | Version bits with guaranteed lock-in     | Shaolin Fry                              | Informational | Draft                |
| [BIP-9](#)   | Version bits with timeout and delay      | Pieter Wuille, Peter Todd, Greg Maxwell, Rusty Russell | Informational | Final                |
| [BIP-10](#)  | Multi-Sig Transaction Distribution       | Alan Reiner                              | Informational | Withdrawn            |
| [BIP-11](#)  | M-of-N Standard Transactions             | Gavin Andresen                           | Standard      | Final                |
| [BIP-12](#)  | OP_EVAL                                  | Gavin Andresen                           | Standard      | Withdrawn            |
| [BIP-13](#)  | Address Format for pay-to-script-hash    | Gavin Andresen                           | Standard      | Final                |
| [BIP-14](#)  | Protocol Version and User Agent          | Amir Taaki, Patrick Strateman            | Standard      | Final                |
| [BIP-15](#)  | Aliases                                  | Amir Taaki                               | Standard      | Deferred             |
| [BIP-16](#)  | Pay to Script Hash                       | Gavin Andresen                           | Standard      | Final                |
| [BIP-17](#)  | OP_CHECKHASHVERIFY (CHV)                 | Luke Dashjr                              | Standard      | Withdrawn            |
| [BIP-18](#)  | hashScriptCheck                          | Luke Dashjr                              | Standard      | Proposed             |
| [BIP-19](#)  | M-of-N Standard Transactions (Low SigOp) | Luke Dashjr                              | Standard      | Draft                |
| [BIP-20](#)  | URI Scheme                               | Luke Dashjr                              | Standard      | Replaced             |
| [BIP-21](#)  | URI Scheme                               | Nils Schneider, Matt Corallo             | Standard      | Final                |
| [BIP-22](#)  | getblocktemplate - Fundamentals          | Luke Dashjr                              | Standard      | Final                |
| [BIP-23](#)  | getblocktemplate - Pooled Mining         | Luke Dashjr                              | Standard      | Final                |
| [BIP-30](#)  | Duplicate transactions                   | Pieter Wuille                            | Standard      | Final                |
| [BIP-31](#)  | Pong message                             | Mike Hearn                               | Standard      | Final                |
| [BIP-32](#)  | Hierarchical Deterministic Wallets       | Pieter Wuille                            | Informational | Final                |
| [BIP-33](#)  | Stratized Nodes                          | Amir Taaki                               | Standard      | Draft                |
| [BIP-34](#)  | Block v2, Height in Coinbase             | Gavin Andresen                           | Standard      | Final                |
| [BIP-35](#)  | mempool message                          | Jeff Garzik                              | Standard      | Final                |
| [BIP-36](#)  | Custom Services                          | Stefan Thomas                            | Standard      | Draft                |
| [BIP-37](#)  | Connection Bloom filtering               | Mike Hearn, Matt Corallo                 | Standard      | Final                |
| [BIP-38](#)  | Passphrase-protected private key         | Mike Caldwell, Aaron Voisine             | Standard      | Draft                |
| [BIP-39](#)  | Mnemonic code for generating deterministic keys | Marek Palatinus, Pavol Rusnak, Aaron Voisine, Sean Bowe | Standard      | Proposed             |
| [BIP-40](#)  | Stratum wire protocol                    | Marek Palatinus                          | Standard      | BIP number allocated |
| [BIP-41](#)  | Stratum mining protocol                  | Marek Palatinus                          | Standard      | BIP number allocated |
| [BIP-42](#)  | A finite monetary supply for Bitcoin     | Pieter Wuille                            | Standard      | Draft                |
| [BIP-43](#)  | Purpose Field for Deterministic Wallets  | Marek Palatinus, Pavol Rusnak            | Informational | Draft                |
| [BIP-44](#)  | Multi-Account Hierarchy for Deterministic Wallets | Marek Palatinus, Pavol Rusnak            | Standard      | Proposed             |
| [BIP-45](#)  | Structure for Deterministic P2SH Multisignature Wallets | Manuel Araoz, Ryan X. Charles, Matias Alejo Garcia | Standard      | Proposed             |
| [BIP-47](#)  | Reusable Payment Codes for Hierarchical Deterministic Wallets | Justus Ranvier                           | Informational | Draft                |
| [BIP-49](#)  | Derivation scheme for P2WPKH-nested-in-P2SH based accounts | Daniel Weigl                             | Informational | Draft                |
| [BIP-50](#)  | March 2013 Chain Fork Post-Mortem        | Gavin Andresen                           | Informational | Final                |
| [BIP-60](#)  | Fixed Length "version" Message (Relay-Transactions Field) | Amir Taaki                               | Standard      | Draft                |
| [BIP-61](#)  | Reject P2P message                       | Gavin Andresen                           | Standard      | Final                |
| [BIP-62](#)  | Dealing with malleability                | Pieter Wuille                            | Standard      | Withdrawn            |
| [BIP-63](#)  | Stealth Addresses                        | Peter Todd                               | Standard      | BIP number allocated |
| [BIP-64](#)  | getutxo message                          | Mike Hearn                               | Standard      | Draft                |
| [BIP-65](#)  | OP_CHECKLOCKTIMEVERIFY                   | Peter Todd                               | Standard      | Final                |
| [BIP-66](#)  | Strict DER signatures                    | Pieter Wuille                            | Standard      | Final                |
| [BIP-67](#)  | Deterministic Pay-to-script-hash multi-signature addresses through public key sorting | Thomas Kerin, Jean-Pierre Rupp, Ruben de Vries | Standard      | Proposed             |
| [BIP-68](#)  | Relative lock-time using consensus-enforced sequence numbers | Mark Friedenbach, BtcDrak, Nicolas Dorier, kinoshitajona | Standard      | Final                |
| [BIP-69](#)  | Lexicographical Indexing of Transaction Inputs and Outputs | Kristov Atlas                            | Informational | Proposed             |
| [BIP-70](#)  | Payment Protocol                         | Gavin Andresen, Mike Hearn               | Standard      | Final                |
| [BIP-71](#)  | Payment Protocol MIME types              | Gavin Andresen                           | Standard      | Final                |
| [BIP-72](#)  | bitcoin: uri extensions for Payment Protocol | Gavin Andresen                           | Standard      | Final                |
| [BIP-73](#)  | Use "Accept" header for response type negotiation with Payment Request URLs | Stephen Pair                             | Standard      | Final                |
| [BIP-74](#)  | Allow zero value OP_RETURN in Payment Protocol | Toby Padilla                             | Standard      | Draft                |
| [BIP-75](#)  | Out of Band Address Exchange using Payment Protocol Encryption | Justin Newton, Matt David, Aaron Voisine, James MacWhyte | Standard      | Draft                |
| [BIP-80](#)  | Hierarchy for Non-Colored Voting Pool Deterministic Multisig Wallets | Justus Ranvier, Jimmy Song               | Informational | Deferred             |
| [BIP-81](#)  | Hierarchy for Colored Voting Pool Deterministic Multisig Wallets | Justus Ranvier, Jimmy Song               | Informational | Deferred             |
| [BIP-83](#)  | Dynamic Hierarchical Deterministic Key Trees | Eric Lombrozo                            | Standard      | Draft                |
| [BIP-90](#)  | Buried Deployments                       | Suhas Daftuar                            | Informational | Draft                |
| [BIP-99](#)  | Motivation and deployment of consensus rule changes ([soft/hard]forks) | Jorge Timón                              | Informational | Draft                |
| [BIP-101](#) | Increase maximum block size              | Gavin Andresen                           | Standard      | Withdrawn            |
| [BIP-102](#) | Block size increase to 2MB               | Jeff Garzik                              | Standard      | Draft                |
| [BIP-103](#) | Block size following technological growth | Pieter Wuille                            | Standard      | Draft                |
| [BIP-104](#) | 'Block75' - Max block size like difficulty | t.khan                                   | Standard      | Draft                |
| [BIP-105](#) | Consensus based block size retargeting algorithm | BtcDrak                                  | Standard      | Draft                |
| [BIP-106](#) | Dynamically Controlled Bitcoin Block Size Max Cap | Upal Chakraborty                         | Standard      | Draft                |
| [BIP-107](#) | Dynamic limit on the block size          | Washington Y. Sanchez                    | Standard      | Draft                |
| [BIP-109](#) | Two million byte size limit with sigop and sighash limits | Gavin Andresen                           | Standard      | Rejected             |
| [BIP-111](#) | NODE_BLOOM service bit                   | Matt Corallo, Peter Todd                 | Standard      | Proposed             |
| [BIP-112](#) | CHECKSEQUENCEVERIFY                      | BtcDrak, Mark Friedenbach, Eric Lombrozo | Standard      | Final                |
| [BIP-113](#) | Median time-past as endpoint for lock-time calculations | Thomas Kerin, Mark Friedenbach           | Standard      | Final                |
| [BIP-114](#) | Merkelized Abstract Syntax Tree          | Johnson Lau                              | Standard      | Draft                |
| [BIP-120](#) | Proof of Payment                         | Kalle Rosenbaum                          | Standard      | Draft                |
| [BIP-121](#) | Proof of Payment URI scheme              | Kalle Rosenbaum                          | Standard      | Draft                |
| [BIP-122](#) | URI scheme for Blockchain references / exploration | Marco Pontello                           | Standard      | Draft                |
| [BIP-123](#) | BIP Classification                       | Eric Lombrozo                            | Process       | Active               |
| [BIP-124](#) | Hierarchical Deterministic Script Templates | Eric Lombrozo, William Swanson           | Informational | Draft                |
| [BIP-125](#) | Opt-in Full Replace-by-Fee Signaling     | David A. Harding, Peter Todd             | Standard      | Proposed             |
| [BIP-126](#) | Best Practices for Heterogeneous Input Script Transactions | Kristov Atlas                            | Informational | Draft                |
| [BIP-130](#) | sendheaders message                      | Suhas Daftuar                            | Standard      | Proposed             |
| [BIP-131](#) | "Coalescing Transaction" Specification (wildcard inputs) | Chris Priest                             | Standard      | Draft                |
| [BIP-132](#) | Committee-based BIP Acceptance Process   | Andy Chase                               | Process       | Withdrawn            |
| [BIP-133](#) | feefilter message                        | Alex Morcos                              | Standard      | Draft                |
| [BIP-134](#) | Flexible Transactions                    | Tom Zander                               | Standard      | Draft                |
| [BIP-140](#) | Normalized TXID                          | Christian Decker                         | Standard      | Draft                |
| [BIP-141](#) | Segregated Witness (Consensus layer)     | Eric Lombrozo, Johnson Lau, Pieter Wuille | Standard      | Draft                |
| [BIP-142](#) | Address Format for Segregated Witness    | Johnson Lau                              | Standard      | Deferred             |
| [BIP-143](#) | Transaction Signature Verification for Version 0 Witness Program | Johnson Lau, Pieter Wuille               | Standard      | Draft                |
| [BIP-144](#) | Segregated Witness (Peer Services)       | Eric Lombrozo, Pieter Wuille             | Standard      | Draft                |
| [BIP-145](#) | getblocktemplate Updates for Segregated Witness | Luke Dashjr                              | Standard      | Draft                |
| [BIP-146](#) | Dealing with signature encoding malleability | Johnson Lau, Pieter Wuille               | Standard      | Draft                |
| [BIP-147](#) | Dealing with dummy stack element malleability | Johnson Lau                              | Standard      | Draft                |
| [BIP-148](#) | Mandatory activation of segwit deployment | Shaolin Fry                              | Standard      | Draft                |
| [BIP-150](#) | Peer Authentication                      | Jonas Schnelli                           | Standard      | Draft                |
| [BIP-151](#) | Peer-to-Peer Communication Encryption    | Jonas Schnelli                           | Standard      | Draft                |
| [BIP-152](#) | Compact Block Relay                      | Matt Corallo                             | Standard      | Draft                |
| [BIP-171](#) | Currency/exchange rate information API   | Luke Dashjr                              | Standard      | Draft                |
| [BIP-180](#) | Block size/weight fraud proof            | Luke Dashjr                              | Standard      | Draft                |
| [BIP-199](#) | Hashed Time-Locked Contract transactions | Sean Bowe, Daira Hopwood                 | Standard      | Draft                |