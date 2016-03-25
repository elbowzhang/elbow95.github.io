---
layout: post
title:  "使用七牛作为博客图床"
date:   2016-03-25
categories: [github pages]
---

日后写博客避免不了会添加图片，其实在TimeLine里已经添加了图片，本来将Github作为图床就可以了，但是随着这个博客使用的时间越来越长，图片也越来越多，都上传到Github上，是一种不道德的行为哈哈，我这里用的是七牛云存储作为我的图床，还是挺方便的。  

## 七牛使用简单步骤

>1.首先注册七牛账号并完成实名认证，就会有免费的10G存储空间以及10G的下载流量，还有10万次PUT和100万次GET请求。  
>
>2.下载[qrsync 命令行上传同步工具](http://developer.qiniu.com/code/v6/tool/qrsync.html)。  
>
>3.根据文档要求创建conf.json配置文件，这里对于windows用户可以把解压的qrsync.exe文件目录添加到环境变量，注意在`"dest":"qiniu:access_key=<AccessKey>.."`这里，记得填写AccessKey或其他参数时均需要把`<>`去掉。  
>
>4.同步的命令为`qrsync conf.json`，这里同步是增量的。  
>
>5.使用时只需要在上传图片详情里将自动生成的链接复制到html文件src里即可。

That's all. Enjoy.
