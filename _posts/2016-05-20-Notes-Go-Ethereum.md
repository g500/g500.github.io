---
layout:     post
title:      "平时用的一些命令"
subtitle:   "这个只是一些平时用的一些命令，最好别看，一般人看不懂的！"
date:       2016-05-20 12:00:00
author:     "liwei"
catalog: true
header-img: "img/post-bg-2015.jpg"
tags:
    - 生活
---

```js
// 更新数据库字段
db.users.update({}, {$set:{'walletsum':0}}, true, true)

// 获取块信息
web3.eth.getBlock(695, true)

web3.eth.getTransactionReceipt("0xe5826cf6d5dd5ef6b8166a02dd1943f47aa36866c10cd8ffca2cc044d88eacd4")

web3.fromWei(web3.eth.getBalance("0xae069eda080a3a67d91fc7e3e2c793cf162c5c60"), "finney")

// 查看ipc调用的个数
lsof -n -P | grep ipc

```
