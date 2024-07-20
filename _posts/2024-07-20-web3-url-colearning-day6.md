---
layout: post
title: web3-url-colearning-day6
date: 2024-07-20 18:49 +0800
---
今天学习一下Ethfs

[Upload your file/folder with ethfs-cli | EthStorage](https://docs.ethstorage.io/dapp-developer-guide/tutorials/upload-your-file-folder-with-ethfs-cli)

安装Ethfs-cli

```
npm install -g ethfs-cli
ethfs-cli upload -f <directory|file> -a <address> -p <private-key> -r [rpc] -t [upload-type]

```

创建Flat Directory 合约

```
ethfs-cli create -p privkey
chainId = 3334
providerUrl = https://galileo.web3q.io:8545
FlatDirectory Address: 0x6F3F9E477a931d208c06efE7E6D3af251B000E94
```

得到

上传第一个文件夹

```
ethfs-cli upload -f myfolder -a 0x6F3F9E477a931d208c06efE7E6D3af251B000E94 -c 3334 -p dadd64540987deea8210cfadcf50aeeed4ec14950a1f05260f7f59084f3b0727
providerUrl = https://galileo.web3q.io:8545
chainId = 3334
address: 0x6F3F9E477a931d208c06efE7E6D3af251B000E94

Send Success: File: hello.txt, Chunk Id: 0, Transaction hash: 0xa7a48056301a4b1773dff28a825576d6b8e2b2dd700d8a30dbbac77a4ac0450b
File hello.txt chunkId: 0 uploaded!


Total Upload Chunk Count: 1
Total Upload File Size: 0.005859375 KB
Total Cost: 0.0 ETHethfs-cli upload -f myfolder -a 0x6F3F9E477a931d208c06efE7E6D3af251B000E94 -c 3334 -p dadd64540987deea8210cfadcf50aeeed4ec14950a1f05260f7f59084f3b0727
providerUrl = https://galileo.web3q.io:8545
chainId = 3334
address: 0x6F3F9E477a931d208c06efE7E6D3af251B000E94

Send Success: File: hello.txt, Chunk Id: 0, Transaction hash: 0xa7a48056301a4b1773dff28a825576d6b8e2b2dd700d8a30dbbac77a4ac0450b
File hello.txt chunkId: 0 uploaded!


Total Upload Chunk Count: 1
Total Upload File Size: 0.005859375 KB
Total Cost: 0.0 ETH
```

成功上传

web3://0x6F3F9E477a931d208c06efE7E6D3af251B000E94:3334/hello.txt

![image-20240720184338899](https://raw.githubusercontent.com/antigone4224/blog-img/main/image-20240720184338899.png)

![image-20240720184656521](https://raw.githubusercontent.com/antigone4224/blog-img/main/image-20240720184656521.png)

