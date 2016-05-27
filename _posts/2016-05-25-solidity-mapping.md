---
layout:     post
title:      "mapping"
subtitle:   "solidity里面有关mapping的坑"
date: "2016-05-25 10:08:10"
author:     "liwei"
catalog:    true
header-img: "img/post-bg-2015.jpg"
tags:
    - Solidity
---

## 有关的website

- [有关mapping的length问题，或者查找所有数据问题](https://forum.ethereum.org/discussion/1995/iterating-mapping-types)

## 简单存储

#### About address(0x0)
当在比较0地址时，由于solidity是静态类型的，所以必须对其进行 **address(0x0)** 转换才可以进行比较
The entire storage space is virtually initialized to 0. There is no undefined. So you have to compare the value to the 0-value for your type. For example 

```
mapping[key] == address(0x0) or mapping[key] = bytes4(0x0)

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

#### mapping + list

I haven't seen an actual iterator concept in Solidity yet (in any of the docs/blogs/posts I've reviewed). So the way I've been doing it in my code is overlaying an index alongside.

So for example, if you have sequentially indexed mapping, it's easy:

```
mapping (uint => address) members;
uint memberCount;
function iterateMembers()
{
    for(uint i=0;i<memberCount;i++)
    {
        doSomeStuff(members[i]);
    }
}

```

And if you have a non sequentially indexed mapping (For example in a wallet list with a list of addresses and balances) then you have no easy way to iterate, because any value in the index field results in a read of a default state. Basically all possible records exist as defaults.

So you overlay an index on top:

```
mapping (address => uint) accountBalances;
mapping (uint => address) accountIndex;
uint accountCount;
function iterateAccountsBalances()
{
    for(uint i=0;i<accountCount;i++)
    {
        doSomeStuff(accountBalances[accountIndex[i]]);
    }
}

```

You just need to be careful to maintain your "count" and "index" appropriately using your accessor functions (getters/setters). So anytime you add to the balances mapping, you need to add an index record, and increment the count, and vice versa, decrement it when you remove.

Hopefully that helps...

By *Liwei* 20160525
