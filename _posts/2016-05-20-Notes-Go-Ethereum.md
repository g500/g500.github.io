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

> db.users.find({}, {"_id":0, "walletaddr":1})
{ "walletaddr" : "0xc7a70fa14a8c3f7cf548e0fbf7ad6dcda4a3331b" }
{ "walletaddr" : "0x73766085d792a9feef1195274f73bf5f0d9ce1b2" }
{ "walletaddr" : "0x494212144a932b06f7d5313c72db0205942fd55f" }
{ "walletaddr" : "0x49688ccb0ab79980d599788b895013330643da5b" }
{ "walletaddr" : "0xa65a393fcadb29ef018549b613deecb88e8b7644" }
>

```

```shell
// 查看ipc调用的个数
lsof -n -P | grep ipc

// find && grep
find . -type f -name '*.sql' -exec grep -i 'texttolookfor' '{}' +

// mac os run geth
geth --datadir="/Users/liweilijie/tmp/data" --genesis ./genesis.json --networkid "31415929" --port 30302 --rpcport 8102 console

```

```shell

var cc = web3.eth.contract([{"constant":false,"inputs":[],"name":"getBalance","outputs":[{"name":"","type":"uint256"}],"type":"function"},{"constant":true,"inputs":[{"name":"","type":"uint256"}],"name":"bts","outputs":[{"name":"addr","type":"address"},{"name":"choice","type":"bytes32"}],"type":"function"},{"constant":false,"inputs":[{"name":"choice","type":"bytes32"}],"name":"doBet","outputs":[],"type":"function"},{"constant":false,"inputs":[],"name":"kill","outputs":[],"type":"function"},{"constant":false,"inputs":[],"name":"getNum","outputs":[{"name":"","type":"uint256"}],"type":"function"},{"constant":false,"inputs":[{"name":"luckyone","type":"bytes32"}],"name":"drawLotterty","outputs":[],"type":"function"},{"constant":false,"inputs":[],"name":"adminWithdraw","outputs":[],"type":"function"},{"inputs":[],"type":"constructor"}])

txhash = cc.at("0x317a2a7858e6ac4e3e5a13e2ac0560d3c6622a58").doBet.sendTransaction(129129,{from:'0x0cfaad5ee122c8e5819f38ebd2c3a5dbc0905b1a', gas:300000, value: web3.toWei(2000, "finney")})

t1 = cc.at("0x317a2a7858e6ac4e3e5a13e2ac0560d3c6622a58").getBalance.sendTransaction({from:'0x0cfaad5ee122c8e5819f38ebd2c3a5dbc0905b1a'})

```


```
eth.sendTransaction({from: eth.accounts[0], value: web3.toWei(1, 'ether'), to: '0x446D1696a5527018453cdA3d67aa4C2cd189b9f6', gas: 300000});

```

```
//JSON functions
function setNickname(string name) {
addNewUser(msg.sender);

if (bytes(name).length >= 2 && bytes(name).length <= 16)
users[msg.sender].nickname = name;

}    

function currentBalance() constant returns (uint pitBalance, string info) {
        pitBalance = balance / 1 finney;
        info = 'The balance of the pit in Finneys (contract balance minus fees).';
}

function heroOfThePit() constant returns (address theHero, string nickname, uint peopleSaved, string info) {
    theHero = hero;  
    nickname = users[theHero].nickname;
    peopleSaved = rescueRecord;
    info = 'The current rescue record holder. All hail!';
}
    

```
