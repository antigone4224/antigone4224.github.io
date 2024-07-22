---
layout: post
title: web3-url-colearning-day8
date: 2024-07-22 22:31 +0800
---
今天就是听课，解答了我之前的一些疑问。。

1.manual mod的路由实现问题，老师给了个[示范项目](https://github.com/QuarkChain/evm-large-storage/blob/main/contracts/examples/FlatDirectoryFactory.sol)

```
// SPDX-License-Identifier: MIT

pragma solidity ^0.8.0;

import "../W3RC3.sol";

contract FlatDirectory is W3RC3 {
    bytes public defaultFile = "";

    constructor(uint8 slotLimit) W3RC3(slotLimit) {}

    function resolveMode() external pure virtual returns (bytes32) {
        return "manual";
    }

    fallback(bytes calldata pathinfo) external returns (bytes memory)  {
        bytes memory content;
        if (pathinfo.length == 0) {
            // TODO: redirect to "/"?
            return bytes("");
        } else if (pathinfo[0] != 0x2f) {
            // Should not happen since manual mode will have prefix "/" like "/....."
            return bytes("incorrect path");
        }

        if (pathinfo[pathinfo.length - 1] == 0x2f) {
            (content, ) = read(bytes.concat(pathinfo[1:], defaultFile));
        } else {
            (content, ) = read(pathinfo[1:]);
        }

        StorageHelper.returnBytesInplace(content);
    }

    function setDefault(bytes memory _defaultFile) public virtual {
        require(msg.sender == owner, "must from owner");
        defaultFile = _defaultFile;
    }
}
```

感觉跟很古早的cgi开发很像。。。路由解析还是自己写代码实现的。。我本来以为会有个类似的框架啥的（（

2.ethfs-cli FlatDirectory文件上传的问题，并不会像正常文件系统一样保存目录的信息，，所有文件都放在一个文件夹中

3.web3-url跟正常的url一样，支持相对路径引用。。这点我都没有注意。
