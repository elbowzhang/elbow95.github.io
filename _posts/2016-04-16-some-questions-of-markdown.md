---
layout: post
title: Markdwon通过新窗口打开超链接
date: '2016-4-16'
categories: [markdown]
---

Markdown自带语法可以设置超链接，但是其不支持生成的链接从新窗口打开，这给阅读的连续性带来了不便，从网上查到了两种方法，经验证都是可行的。

如将本文链接通过新窗口打开，可以通过一下两种方法实现。

## 直接使用html语言

```
<a href="http://www.***.com" target="_black">Blog</a>
```

## 在自带语法后添加target参数

```
[Blog](http://www.***.com){:target="_blank"}
```

经过验证通过以上两种方法均可实现点击.md文件中链接后打开新窗口。
