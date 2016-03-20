---
layout: post
title:  "将Github Pages重定义到自定义域名"
date:   2016-03-20
---
博客搭建工作基本完成之后，用腾讯云域名服务申请了一个.cn顶级域名，完成学生认证领到了一个域名代金券，可以算是免费买到了一个域名。  
重新定义博客的域名首先需要在rep根目录下新建一个CNAME文件(注意：没有后缀)  

 > 若想要使用顶级域名访问博客，在该文件中填入example.cn  
 > 你也可以使用二级域名访问博客，此时应在该文件中填入 www.example  

完成这一步，当你输入博客原始域名 `example.github.io` 时，就会跳转到 

 > http://example.cn  
 > 或者  
 > http://www.example.cn  

如果以上两个域名为空，则会显示Not Found，如果你持有在CNAME文件中填入的域名，你需要在DNS解析中也将域名的对应纪录CNAME到 `example.github.io` ,这里可以参考Github pages官方文档[Using a custom domain with GitHub Pages](https://help.github.com/articles/using-a-custom-domain-with-github-pages/)  

下面根据自己的经历进行一些简单的纪录，如果想要绑定到顶级域名上，那么需要在域名解析里加入以下两个纪录：  

> @(主机记录) A(记录类型) (192.30.252.153/154)(记录值)  
> www(主机类型) A(记录类型) (192.30.252.153/154)(记录值)  

或者可以绑定到二级域名如www.example.cn上，那么需要加入以下两个记录：  

> @(主机记录) CNAME(记录类型) (example.github.io)(记录值)  
> www(主机类型) CNAME(记录类型) (example.github.io)(记录值)  

同时你也可以将你不同的站点绑定到不同的二级域名上，只需修改主机类型以及纪录值即可。  

本文参考[创建GitHub技术博客全攻略](http://blog.csdn.net/renfufei/article/details/37725057)