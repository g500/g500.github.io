---
layout:     post
title:      "jeklly配置学习"
subtitle:   "很早之前就在用，最近又开始整理了！"
date:       2016-05-23 12:00:00
author:     "Liwei"
catalog:    true
header-img: "img/post-bg-2015.jpg"
tags:
    - 生活
---

```shell
---
layout     : post
title      : " contract有关的备忘 "
subtitle   : " 之前有关的合约语法没有做好记录 "
date       : 2016-05-23 12:00:00
author     : "liwei"
catalog    : true
header-img : "img/post-bg-2015.jpg"
tags       :
    - Go
---
```

## 有关的website

- [编辑器](https://ethereum.github.io/browser-solidity/#version=soljson-latest.js)
- [solidity官网](http://solidity.readthedocs.io/en/latest/)


## 简单存储

```js

// 存储
contract SimpleStorage {
    uint storedData;
    function set(uint x) {
        storedData = x;
    }
    function get() constant returns (uint retVal) {
        return storedData;
    }
}
```

- 跟很多其他语言一样，访问状态变量时，不需要在前面增加 **this** . 这样的前缀。


## 代币

```js

contract Coin {
//关键字“public”使变量能从合约外部访问。
    address public minter;
    mapping (address => uint) public balances;

//事件让轻客户端能高效的对变化做出反应。
    event Sent(address from, address to, uint amount);

//这个构造函数的代码仅仅只在合约创建的时候被运行。
    function Coin() {
        minter = msg.sender;
    }
    function mint(address receiver, uint amount) {
        if (msg.sender != minter) return;
        balances[receiver] += amount;
    }
    function send(address receiver, uint amount) {
        if (balances[msg.sender] < amount) return;
        balances[msg.sender] -= amount;
        balances[receiver] += amount;
        Sent(msg.sender, receiver, amount);
    }
}

```


By *Liwei* 20160523
