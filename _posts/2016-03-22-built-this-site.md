---
layout: post
title: 对博客的后续搭建及完善
date: '2016-3-22'
categories:
  - github pages
---

上篇完成了对博客基本架构的搭建，这里介绍一些更加高级一些的功能。这些功能并不是一次实现的，这里对这几天的工作做了一个总结。

## # 将Github Pages重定义到自定义域名

博客搭建工作基本完成之后，如果想要重定义网址域名，首先你需要购买一个域名。重新定义博客的域名首先需要在rep根目录下新建一个CNAME文件(注意：没有后缀)

> 若想要使用顶级域名访问博客，在该文件中填入example.cn<br>
> 你也可以使用二级域名访问博客，此时应在该文件中填入 www.example.cn

完成这一步，当你输入博客原始域名 `example.github.io` 时，就会跳转到

> http://example.cn<br>
> 或者<br>
> http://www.example.cn

如果以上两个域名为空，则会显示Not Found，如果你持有在CNAME文件中填入的域名，你需要在DNS解析中也将域名的对应纪录CNAME到 `example.github.io` ,这里可以参考Github pages官方文档[Using a custom domain with GitHub Pages](https://help.github.com/articles/using-a-custom-domain-with-github-pages/){:target="_blank"}

下面根据自己的经历进行一些简单的纪录，如果想要绑定到顶级域名上，那么需要在域名解析里加入以下两个纪录：

> @(主机记录) A(记录类型) (192.30.252.153/154)(记录值)<br>
> www(主机类型) A(记录类型) (192.30.252.153/154)(记录值)

或者可以绑定到二级域名如www.example.cn上，那么需要加入以下两个记录：

--- @(主机记录) CNAME(记录类型) (example.github.io)(记录值)<br>
--- www(主机类型) CNAME(记录类型) (example.github.io)(记录值)

同时你也可以将你不同的站点绑定到不同的二级域名上，只需修改主机类型以及纪录值即可。

参考[创建GitHub技术博客全攻略](http://blog.csdn.net/renfufei/article/details/37725057){:target="_blank"}

## # 完善Blog Header选项  

感谢[Kun Wang](http://quantumman.me/){:target="blank"}，参考了他的博客的archive和categories的相关代码。  

同时发现将about.md及timeline.md放在工程根目录下并不是很美观(强迫症)，于是将他们与Categories及Archive同时放在了header文件夹下并将其格式更改成.html。  

在整个过程中并没有遇到什么大问题，了解了html的相关语法，基本掌握了博客的结构还有静态网页的搭建方法。

## # 给博客添加timeline功能  

起初的Timeline样式只是简单的html格式，加了简单的日期，及事件。  

搜索静态网页时间线设计，找到了一个同样托管在Github Pages的开源博客[袁勇的个人主页](http://yongyuan.name/cn/){:target="blank"}，他的时间线效果比较符合我的口味，有动画效果，同时可以附加照片等，这样一个实例对于我这种网页设计小白来说帮助很大。  

但clone了半个小时博主的项目发现clone不下来，发现他的项目实在太大了，博主把图片都放在一个文件夹里push到了他的github上，无奈只能下载，发现一个85M的项目其中一个Images文件夹就占了60M，为什么不用图床呢，虽然Github是免费的。  

感觉css文件里各种配置的嵌套有点凌乱，不过还是哪漏补哪补成了自己想要的效果，跟博客整体设计相一致。  

至此，博客整体基本完成，各个想要的功能也都已实现，简约主义，至少自己看着挺舒服的。  

## # 使用七牛作为博客图床  
日后写博客避免不了会添加图片，其实在TimeLine里已经添加了图片，本来将Github作为图床就可以了，但是随着这个博客使用的时间越来越长，图片也越来越多，都上传到Github上，是一种不道德的行为哈哈，我这里用的是七牛云存储作为我的图床，还是挺方便的。

### # 七牛使用简单步骤

> 1.首先注册七牛账号并完成实名认证。
>
> 2.下载[qrsync 命令行上传同步工具](http://developer.qiniu.com/code/v6/tool/qrsync.html){:target="_blank"}。
>
> 3.根据文档要求创建conf.json配置文件，这里对于windows用户可以把解压的qrsync.exe文件目录添加到环境变量，注意在`"dest":"qiniu:access_key=<AccessKey>.."`这里，记得填写AccessKey或其他参数时均需要把`<>`去掉。
>
> 4.同步的命令为`qrsync conf.json`，这里同步是增量的。
>
> 5.使用时只需要在上传图片详情里将自动生成的链接复制到html文件src里即可。

## # 总结  

至此博客简单的架构算是完成，将过程在这里记录下来，也许有后来人可以看到。

不过依旧存在一些问题，比如无法适配手机屏幕等，这些将在后面一段时间内解决。
