---
layout: post
title: web3-url-colearning-day2
date: 2024-07-16 20:12 +0800
---
# hw1:

## Find the ownership of an your favor NFT

查询一下slides里展示的cyberbrokers吧

ERC721有ownerOf方法可以通过tokenid查询owner的地址

![image-20240716151628876](https://github.com/antigone4224/blog-img/blob/main/image-20240716151628876.png?raw=true)

在etherscan上查询的合约地址可以调用ownerOf方法，然而cyberbrokers-meta这个地址却不能

![image-20240716151915929](https://github.com/antigone4224/blog-img/blob/main/image-20240716151915929.png?raw=ture)

再尝试了一下发现那个0x89的合约地址同样也无法执行renderBroker的方法

ens上查询了一下0x8b开头的一个合约地址能renderBroker

还是有点不太明白怎么回事暂时。

## Find the balance of an account in an ERC-20 contract (USDC / USDT)

ERC20 有个balanceOf方法可以查询余额

web3:// (addr of contract)/balanceOf/(acc addr)?returns=(uint256)

![image-20240716144143413](https://github.com/antigone4224/blog-img/blob/main/image-20240716144143413.png?raw=true)

## Deploy a contract in auto model and say “hello world”

很明显是要我们关注ERC 6860关于 解析模式(resolve mode)的定义了

```
 Resolve Mode
Once the “To” address and chainid are determined, the protocol will check the resolver mode of contract by calling the resolveMode method of the “To” address. The Solidity signature of resolveMode is:

function resolveMode() external returns (bytes32);
The protocol currently supports two resolve modes: auto and manual.

The manual mode will be used if the resolveMode return value is 0x6d616e75616c0000000000000000000000000000000000000000000000000000, i.e., “manual” in bytes32
The auto mode will be used if :
the resolveMode return value is 0x6175746f00000000000000000000000000000000000000000000000000000000, i.e, “auto” in bytes32, or
the resolveMode return value is 0x0000000000000000000000000000000000000000000000000000000000000000, or
the call to resolveMode throws an error (method not implemented or error thrown from the method)
Otherwise, the protocol will fail the request with the error “unsupported resolve mode”.
```

合约地址：0xea100cb127d5547793172248e730e1b4b6524984

部署在sepolia

代码：

```
pragma solidity >=0.7.0 <0.9.0;

contract Hello_world_auto {
    function sayHelloWorld() public pure returns (string memory) {
        return "Hello World";
    }

    function resolveMode() pure external returns (bytes32) {
        return bytes32("auto");
    }
}
```



![image-20240716200355477](https://github.com/antigone4224/blog-img/blob/main/image-20240716200355597.png?raw=ture)

## Deploy a contract in manual model and say “hello world”

同理

```
pragma solidity >=0.7.0 <0.9.0;

contract Hello_world_manual {
    function sayHelloWorld() public pure returns (string memory) {
        return "Hello World";
    }

    function resolveMode() pure external returns (bytes32) {
        return bytes32("manual");
    }
}
```

不过我还没有比较过manual，和auto访问时候的特性，这个丢到明天来继续。
