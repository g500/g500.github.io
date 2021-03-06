---
layout:     post
title:      " Go对多维map的初始化"
subtitle:   "主要对多维map的操作,这里有些坑等着人们跳下去! "
date:       2016-05-21 15:00:00
author:     "liwei"
catalog: true
header-img: "img/post-bg-2015.jpg"
tags:
    - Go
---

- [来自这里](http://tnt.wicast.tk/2015/11/02/golang-multiple-dimension-map/)

## 什么是 map
首先 Golang 中的 map 与 JS，Py 里的 Map 函数并不是同一回事。
前者是 Golang 中的一种基础数据类型，具有 *Key:Value* 的特性；
而后者的 Map 是 MapReduce 中的那个 Map，把一组数据分发到其他地方.

## 使用 map，以及多维 map 的坑

map 的底层就是一个 hash，平时咱喜欢利用这一点来给一组数据去除重复. 
使用 map 的时候需要注意，你需要显式地初始化才能对 map 进行操作.

```go
var m map[string]string
m["a"]="sssss"
```

上面的代码会报 **panic: assignment to entry in nil map**  ，必须用内建的 **make()** 函数才行.

```go
m:=make(map[string]string)

m["a"]="sssss"
```

咱在自己的项目里用到了一个二维 map，结果第一遍写的时候就碰到了那个 nil map 的问题。
一开始的代码是这样的：

```go
m:=make(map[string]map[string]string)
m["a"]["b"]="ccc"
```

后来才想明白如果插新加入的元素也是个 map 的话需要再次 make()!! 修正后的代码如下

```go
m:=make(map[string]map[string]int)
c:=make(map[string]int)
c["b"]=1
m["a"]=c
```

这时的 m["a"] 的值就是另一个 map 了.



多维度 Map 的数据存取
一维情况下的 map 做存取很简单，而二维以上的情况就得小心了. 先来看一个例子：

```go
m:=make(map[string]map[string]int)
c:=make(map[string]int)
c["b"]=1
m["a"]=c
d:=make(map[string]int)
d["c"]=2
m["a"]=d
```

而这个时候再去查询 m["a"]["b"] 会发现这个值已经没有了，取而代之的是 m["a"]["c"].
这是因为 b 和 c 都是 map[string]int 类型的数据，Golang 直接把 ["a"] 里的数据从 b 替换成了 c，而不会递归地添加 map 中缺失的数据。
要在 m 中保留 ["a"]["b"] 和 ["a"]["c"]，需要一些额外的判断才行：

```go
if _,exist:=m["a"];exist{
    m["a"]["c"]=2
}else{
    c:=make(map[string]int)
    c["c"]=2
    m["a"]=c
}
```

换句话说，每次创建一个一维 map 都要 make() 一次，不然就会 panic. 多维 map 没加一层都要多 make() 好几次.


