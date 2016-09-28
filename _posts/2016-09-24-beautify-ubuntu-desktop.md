---
layout: post
title: Ubuntu 15.04的美化
date: '2016-9-24'
categories: [ubuntu]
---

涉足Ubuntu Desktop已有一段时间，经过这段时间的调教用起来越来越顺手，下面介绍一下我对自己用的Ubuntu的美化过程。  

## 安装Unity Tweak Tool
Unity Tweak Tool是Ubuntu的一款个性化调节工具，其在Ubuntu 15.04下使用PPA方式安装，首先使用组合键`ctrl+alt+T`
打开终端，依次输入以下命令
{% highlight ruby %}
sudo add-apt-repository ppa:freyja-dev/unity-tweak-tool-daily  
sudo apt-get update
sudo apt-get install unity-tweak-tool
{% endhighlight %}  
安装完成后可以在Dash中找到该程序。

## 安装主题及图标  
我的Ubuntu使用的主题及图标都是是Flatabulous，一款仿苹果的扁平化主题。这款主题的托管网址在[Flatabulous](https://github.com/anmoljagetia/Flatabulous){:target="blank"}, 你可以在那里找到安装方法。  

安装完成后打开Unity Tweak Tool在主题界面选择 `Flatabulous` 即可设置其为Ubuntu主题，设置完成后效果如下  
透明化bash命令行界面效果：

<div class="image-wrapper" style="text-align: center">
  <img src="http://odyloipwl.bkt.clouddn.com/post/2016-09-24/bash.png" width="500px">
</div>  

文件管理器界面效果：  

<div class="image-wrapper" style="text-align: center">
  <img src="http://odyloipwl.bkt.clouddn.com/post/2016-09-24/explorer.png" width="700px">
</div>

## Docky安装  
Docky是Ubuntu上一款类似于Mac上Dock的工具栏，其安装方式可以通过Ubuntu的应用程序商店安装，同样也可以通过一下命令  
{% highlight ruby %}
sudo apt install docky
{% endhighlight%}  
效果如下图所示：

<div class="image-wrapper" style="text-align: center">
  <img src="http://odyloipwl.bkt.clouddn.com/post/2016-09-24/dock1.png" width="500px">
</div>

下图为Docky的设置界面：  

<div class="image-wrapper" style="text-align: center">
  <img src="http://odyloipwl.bkt.clouddn.com/post/2016-09-24/dock-settings.png" width="400px">
</div>

## 截图欣赏  

<div class="image-wrapper" style="text-align: center">
  <img src="http://odyloipwl.bkt.clouddn.com/post/2016-09-24/desktop.png" width="700px">
</div>
