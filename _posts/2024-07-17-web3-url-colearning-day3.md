---
layout: post
title: web3-url-colearning-day3
date: 2024-07-17 21:44 +0800
---
   今天稍微有点事情，可能就没法接着把hw做完了，打算完善一下对于resolve mod的理解,阅读ERC 6860文档，对比Auto Mod 和manual  Mod的区别：

```
the protocol will call the address with “Calldata” = keccak("resolveMode()")[0:4] = “0xDD473FAE”
```

在第一访问的开始，会尝试调用resolveMode()方法来确定resolvemod

如果仅指定地址 chainid,路径为空，Manual的calldata会是"/",AutoMod的calldata会为空。。

ManualMod会直接将Path+Query作为Calldata发出，而AutoMod会多一个进行abi转化的功能(AutoMod 可以把参数放到路径中，以及可以通过returns指定返回结果的类型，在调用的时候会自动转化成符合正常合约调用的abi的calldata调用的)。

Manual Mod 返回数据默认为text/html，如果不是会根据MIME后缀名自动识别，而Auto Mod则是根据returns的指定返回application/json，如果没有returns应该是undefined，从这个角度来说，ManualMod更适合于访问资源，而auto mod更适合于作为数据处理的中继。

Manual Mod可以很精准的定位资源在合约中的位置，但很明显这种直接将相对位置作为call data的方式是需要特殊支持的，就好像写web框架需要单独的文件服务一样，它更符合我们对于web2资源访问的构思。
