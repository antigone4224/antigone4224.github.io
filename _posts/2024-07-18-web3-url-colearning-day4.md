---
layout: post
title: web3-url-colearning-day4
date: 2024-07-18 22:08 +0800
---
今天学习一下ethstorage。

```
EthStorage 是一个模块化和去中心化的存储 Layer 2，提供由数据可用性（DA）驱动的可编程键值存储。它为 Rollups 提供了长期的数据可用性解决方案，并为完全链上应用程序（如游戏、社交网络、人工智能等）开辟了新的可能性。

动机
EthStorage 背后的主要动机是提供基于以太坊的长期数据可用性。

EIP-4844 引入了数据斑块（data blobs），这增强了 Layer 2 扩展解决方案（如 Rollups）的吞吐量和效率。然而，这些数据斑块是临时可用的，意味着它们将在几周后被丢弃。这产生了一个显著的影响：L2 无法无条件地从 L1 推导出最新状态。如果某些数据无法再从 L1 检索，Rollup 可能无法同步链。

通过 EthStorage 作为长期数据可用性解决方案，L2 可以随时从它们的数据可用性层（以太坊 DA、Celestia、EigenDA 等 + EthStorage）中推导数据。

EthStorage 还为完全链上应用程序（如游戏、社交网络、人工智能等）开辟了新的可能性。
```



[ethstorage文档](https://docs.ethstorage.io/)

[为ETHstorage提供存储](https://docs.ethstorage.io/storage-provider-guide/tutorials)，也算是某种存储挖矿（，可惜要550g的空余存储空间，不然挖矿还是挺香的

ethstorage的愿景是让以太坊网络的存储变得不再昂贵，到了一种可接受的程度，这样通过web3 url许多web2的网络服务也就可以部署在web3上了。
