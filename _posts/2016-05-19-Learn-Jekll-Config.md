---
layout:     post
title:      "jeklly配置学习"
subtitle:   "很早之前就在用，最近又开始整理了！"
date:       2016-05-19 12:00:00
author:     "Liwei"
catalog:    true
header-img: "img/post-bg-2015.jpg"
tags:
    - 生活
---

## 从网上找到资料整理

[借用的博客](http://azeril.me/blog/Build-Your-First-GitHub-Pages-Blog.html)


## 遇到的问题

如何在一台电脑上登陆2个github帐号的问题。

如何本地访问的问题。

## 使用Jekyll版本作为博客模板，仅需如下几步：

1. 首先Fork或Download一份本项目代码。
2. 修改_config.yaml及about.md文件，以变更个人信息。
3. 修改_include目录下相关文件，以配置网站统计(analytics.html)，网友评论(comment.html)，右侧栏目(categories.html),热门文章(hot.html),友情链接(links.html)等。
4. 修改CNAME文件，以绑定自己的域名。
5. 删除_posts下文章，换成你自己的。
6. 去谷歌自定义搜索新建一个你的搜索引擎，把你的Id替换根目录下search.html我的ID
7. 最后，push到你自己的博客Repo~

## 博客需要定制的功能
1. catalog功能需要加上，也就是页面的导航功能需要加上
2. 很多需要去掉的没有多大意义的功能。比如微博之类的
3. 图片换掉
4. 将highlight里面的js与css替换过去。完成代码的着色功能。
5. 有些line-height和padding需要手动调整成自己喜欢的间距。比如1.75em我比较喜欢。
6. 在写title的时候一定要注意冒号与内容之间留点间隔，不然有时候会出问题。我也不知道为什么。理论上应该是与utf8有关系。


写tags的方法：

 
    tags:
        - 生活


像上面这样就可以。

在本地查看博客的代码：

```shell
# 在本地启动博客
jekyll serve --watch

# 这时候会在根目录下面的_site生成所有的内容。
tree _site
```


By *Liwei* 160519
