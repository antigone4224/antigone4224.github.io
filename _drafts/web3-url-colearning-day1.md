---
layout: post
title: "“web3-url-colearning-day1”"
---

预习了一下第一次的 [slides](https://docs.google.com/presentation/d/1egJUKJrjC9wjkmOF9sLBkTSwHpd6hl8FXkWehPW7kFk/edit#slide=id.g1754f50a55c_0_11),简单的介绍了一下web3://协议

ERC-4804提出了使用RFC 2396 URI来表示一个以太坊消息调用，给予了我们一种直接访问以太坊区块链的方式，也就是web3 url，从此不再需要经过http://或者ipfs的代理。

可以参考下面这张图（直接从slides里扒的链接，可能会没法加载= =）

下面的标识是对应URI的RFC标准，上面的标识是对应了对于区块链中具体寻址的对应。

![img](https://lh7-us.googleusercontent.com/slidesz/AGV_vUcpijLKlO-0God_BdgVPYw8-B1CSb0YvALN8_5X4tXih74cxO9O_35jEs8nUlHKo6ba4NkSgxihzte_nqw-Q53i49ZkHJ-_bjI2IlJPIXT-1A-ghRAGLGQQsgBtx51hNyWWOEQMOe3VnLAOpqj65W7kvLrWFqmz=s2048?key=0jO8rkPrChFDCniaDj_L9Q)

可以看到web3:// 支持大多数http://的功能，就是对标以太坊生态中的http://,与ipfs://不同的是，web3://是直接对接到链上的，虽然看起来都是去中心化的一种应用层协议，但是ipfs://对接的是共享文件的peer节点，web3://对接的是以太坊或者EVM生态的区块链网络。

slides里提供了两种使用web3:// 协议的方式，一种是[火狐浏览器插件](https://addons.mozilla.org/en-US/firefox/addon/web3url/),这种访问还是经过了gateway 经过http://代理来实现的访问(看起来只是将请求转换成了url请求发送给gateway),然后就是一个[EVM Browser](https://github.com/web3-protocol/evm-browser) 这个在我仅仅只看Readme的情况下，需要一个https的链接指定rpc，，翻了下[源码](https://github.com/web3-protocol/web3protocol-js/blob/main/src/chains/client.js)，还真是http call rpc,,

```
  async #rpcCall(rpcUrl, args) {
    if(this.#rpcCallCounters[rpcUrl] === undefined) {
      this.#rpcCallCounters[rpcUrl] = 0;
    }
    this.#rpcCallCounters[rpcUrl]++;

    let postData = {
      jsonrpc: "2.0", 
      id: this.#rpcCallCounters[rpcUrl],
      method: "eth_call",
      params:[
        args,
        "latest"
      ]
    }

    const response = await fetch(rpcUrl, {
      method: 'POST', 
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify(postData)
    })

    if(response.status != 200) {
      throw new Error("RPC returned an error HTTP code: " + response.status);
    }

    const json = await response.json()

    if(json.error) {
      throw new Error("RPC returned an error: " + json.error.message);
    }

    if(json.result === undefined || json.result === "" || json.result == "0x") {
      throw new Error("No data returned");
    }
    

    // We got some data!
    return json.result;
  }
```



感觉独立协议的部分应该还是未完全完成的状态？

按照ppt的指引，通过web3://访问

![](https://github.com/antigone4224/blog-img/blob/main/image-20240715131149029.png?raw=true)

浏览器抓包也能很明显看出还是隐式的使用了https

<img src="https://github.com/antigone4224/blog-img/blob/main/image-20240715132058280.png?raw=true" alt="image-20240715132058280" style="zoom: 200%;" />

后面slides还讲了一堆将常规web2应用迁移到web3的例子，展示了web3://代替http://的可能性。

随后 slides里列出了对该erc进行拓展的[ERC-5018](https://eips.ethereum.org/EIPS/eip-5018)：实现一个在合约类文件系统的接口给合约访问/上传资源，让合约应用显得更贴近web应用，更加符合常规web应用开发的逻辑。 

[ERC-6944](https://eips.ethereum.org/EIPS/eip-6944)：希望添加一种请求资源的参数解析模式(erc-4804提出了manual和auto两种，前者并不会自动填充参数，所以应该会产生参数缺失无法成功访问的问题)，应该也是为了优化web3网页的体验。



