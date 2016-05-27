---
layout:     post
title:      " contract有关的备忘 "
subtitle:   " 之前有关的合约语法没有做好记录 "
date: "2016-05-23 12:08:10"
author:     "Liwei"
catalog:    true
header-img: "img/post-bg-2015.jpg"
tags:
    - Go
---

## 有关的website

- [编辑器](https://ethereum.github.io/browser-solidity/#version=soljson-latest.js)
- [solidity官网](http://solidity.readthedocs.io/en/latest/)
- [入门教程](http://www.ethfun.com/?p=72)
- [很多合约例子](http://ethereum.stackexchange.com/questions/2940/where-can-i-find-some-solidity-smart-contract-source-code-examples)
- [处理字符串的合约库](https://github.com/Arachnid/solidity-stringutils)
- [字符串与int型之间转换库](https://github.com/pipermerriam/ethereum-string-utils)
- [ethereum alarm clock](http://www.ethereum-alarm-clock.com/source/v0.7.0/)
- [这个历史合约的界面](http://canary.ethereum-alarm-clock.com/)
- [token成品](https://www.ethereum.org/token)
- [meat calculator](https://gist.github.com/alexvandesande/3abc9f741471e08a6356)
- [all dapp bin](https://github.com/ethereum/dapp-bin)
- [示例代码](https://learnxinyminutes.com/docs/solidity/)
- [solidity features](https://github.com/ethereum/wiki/wiki/Solidity-Features)
- [solidity 7种类型](http://solidity.readthedocs.io/en/latest/types.html)

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

//关键字"public"使变量能从合约外部访问。

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

- public关键字会自动生成一个函数：

```js
function minter() returns (address) { return minter; }
```

- 下一行代码mapping (address => uint) public balances; 创建了一个public的状态变量，但是其类型更加的复杂。该类型将一些address映射到无符号整数。mapping可以被认为是一个哈希表，每一个可能的key对应的value被虚拟的初始化为全0.这个类比不是很严谨，对于一个mapping，无法获取一个包含其所有key或者value的链表。所以我们得自己记着添加了哪些东西到mapping中。更好的方式是维护一个这样的链表，或者使用其他更高级的数据类型。或者只在不受这个缺陷影响的场景中使用mapping，就像这个例子。

## 神奇变量

- msg
- tx
- block

它们包含了一些可以被合约代码访问的属于区块链的属性。

- **msg.sender** 总是存放着当前函数的外部调用者的地址


## 账户

- 以太坊中有两类账户，它们共用同一个地址空间。外部账户，该类账户被公钥-私钥对控制（人类）。合约账户，该类账户被存储在账户中的代码控制。
- 外部账户的地址是由公钥决定的，合约账户的地址是在创建改合约时确定的（这个地址由合约创建者的地址和该地址发出过的交易数量计算得到，地址发出过的交易数量也被称作"nonce"）
- 合约账户存储了代码，外部账户则没有，除了这点以外，这两类账户对于EVM来说是一样的。
- 每个账户有一个key-value形式的持久化存储。其中key和value的长度都是256bit，名字叫做storage.

另外，每个账户都有一个以太币余额（单位是“Wei”），该账户余额可以通过向它发送带有以太币的交易来改变。

## 交易

- 一笔交易是一条消息，从一个账户发送到另一个账户（可能是相同的账户或者零账户，见下文）。交易可以包含二进制数据（payload）和以太币。
- 如果目标账户包含代码，该代码会执行，payload就是输入数据。
- 如果目标账户是零账户（账户地址是0），交易将创建一个新合约。正如上文所讲，这个合约地址不是零地址，而是由合约创建者的地址和该地址发出过的交易数量（被称为nonce）计算得到。创建合约交易的payload被当作EVM字节码执行。执行的输出做为合约代码被永久存储。这意味着，为了创建一个合约，你不需要向合约发送真正的合约代码，而是发送能够返回真正代码的代码。

## Gas

- 以太坊上的每笔交易都会被收取一定数量的gas，gas的目的是限制执行交易所需的工作量，同时为执行支付费用。当EVM执行交易时，gas将按照特定规则被逐渐消耗。
- gas price（gas价格，以太币计）是由交易创建者设置的，发送账户需要预付的交易费用 = gas price * gas amount。 如果执行结束还有gas剩余，这些gas将被返还给发送账户。
- 无论执行到什么位置，一旦gas被耗尽（比如降为负值），将会触发一个out-of-gas异常。当前调用帧所做的所有状态修改都将被回滚。



## 自毁

- 只有在某个地址上的合约执行自毁操作时，合约代码才会从区块链上移除。合约地址上剩余的以太币会发送给指定的目标，然后其存储和代码被移除。
- 注意，即使一个合约的代码不包含自毁指令，依然可以通过代码调用(callcode)来执行这个操作。

## 数据结构

#### Structs Types

Structs are custom defined types that can group several variables (see Structs in types section).

```
contract Ballot {
    struct Voter { // Struct
        uint weight;
        bool voted;
        address delegate;
        uint vote;
    }
}
```

#### Enum Types

Enums can be used to create custom types with a finite set of values (see Enums in types section).

```
contract Purchase {
    enum State { Created, Locked, Inactive } // Enum
}
```

#### Mappings

Mapping types are declared as mapping **_KeyType** => **_ValueType**, where **_KeyType** can be almost any type except for a mapping and **_ValueType** can actually be any type, including mappings.

Mappings can be seen as hashtables which are virtually initialized such that every possible key exists and is mapped to a value whose byte-representation is all zeros. The similarity ends here, though: The key data is not actually stored in a mapping, only its sha3 hash used to look up the value.

Because of this, mappings do not have a length or a concept of a key or value being “set”.

Mappings are only allowed for state variables (or as storage reference types in internal functions).

#### delete

delete a assigns the initial value for the type to a. I.e. for integers it is equivalent to a = 0, but it can also be used on arrays, where it assigns a dynamic array of length zero or a static array of the same length with all elements reset. For structs, it assigns a struct with all members reset.

delete has no effect on whole mappings (as the keys of mappings may be arbitrary and are generally unknown). So if you delete a struct, it will reset all members that are not mappings and also recurse into the members unless they are mappings. However, individual keys and what they map to can be deleted.

It is important to note that delete a really behaves like an assignment to a, i.e. it stores a new object in a.

#### Types

Solidity is a **statically** typed language, which means that the type of each variable (state and local) needs to be specified (or at least known - see Type Deduction below) at compile-time. Solidity provides several elementary types which can be combined to complex types.

## keys

- `return block.coinbase;` get CURRENT block miner's address
- `block.gaslimit; block.number` the most gas that can be spent on any given transaction right now
- `block.timestamp` uint类型的, returns current block timestamp in SECONDS (not ms) from epoch


```

 // returns current block timestamp in SECONDS (not ms) from epoch
function getBlockTimestamp() constant returns (uint) {													

// also "now" == "block.timestamp", as in "return now;"
    	return block.timestamp; 						 
}

```
- `function getCurrentMinerAddress() constant returns (address)` constant关键字很重要

```
// The data of a call to this function. Always returns "0xc8e7ca2e" for me.
function getMsgData() constant returns (bytes) {			

// adding an input parameter would probably change it with each diff call?
    	return msg.data;
}

```

```
function getContractAddress() constant returns (address) {
		return this;
}

```

returns (string)
```
function getWhatHappened() constant returns (string) {
    	return whathappened;
}

```

#### About address(0x0)
当在比较0地址时，由于solidity是静态类型的，所以必须对其进行 **address(0x0)** 转换才可以进行比较
The entire storage space is virtually initialized to 0. There is no undefined. So you have to compare the value to the 0-value for your type. For example ```mapping[key] == address(0x0) or mapping[key] = bytes4(0x0)``` .

#### memory

```
contract c {
  function f() {
    uint[] memory x = new uint[](100);
    uint[][] memory twoDim = new uint[][](20);
    for (uint i = 0; i < twoDim.length; i++)
      twoDim[i] = new uint[](30);
  }
}

function getWinners() public constant returns (uint num, bytes32 w, string info, uint[] data2) {
      
      uint[] memory data = new uint[](100);
      data[0] = 1990;
      data[1] = 1991;
      data[2] = 1993;
      data[3] = 1994;
      data[4] = 2016;
      data[99] = 100;

      data2 = data;
      num = data.length;
      if (winners.length > 0)
      w = winners[0].choice;
      info = 'the getWineners() returns (num, choice, info)';
  }

```

By *Liwei* 20160523
