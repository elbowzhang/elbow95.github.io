---
layout : post
title  : 安装Ubuntu 16.04之后需要做的一些事
date   : '2017-06-19'
categories : [ubuntu]
---

最近很久一段时间没有安装和使用linux系统了，作为一个以后想做嵌入式的coder，了解和使用一下linux是必须的。前天又重装了一次，每次重装系统都需要在网上查很多需要在终端上要做的事，这里一些需要的工作做一下总结。之前提到了安装Ubuntu之后进行美化的工作[Ubuntu 15.04的美化](./beautify-ubuntu-desktop.html){:target="_blank"}。当然这并不是必须的，你也可以使用Ubuntu 16.04系统的默认主题进行使用。

## 系统更新与设置

- 系统安装完成之后，进入`设置->软件与更新->附加驱动`选择独立显卡驱动以及另外的一些驱动进行安装。

- 在bash中输入`sudo apt-get upgrade`之后输入登录密码对系统软件进行更新。

- 设置时间使用UTC，终端输入以下命令之后将`#UTC=no`更改为`#UTC=yes`。
{% highlight bash %}
sudo vim /etc/default/rcS
{% endhighlight %}

- 使用以下命令删除自带Office及一些游戏等基本用不到的软件

{% highlight bash %}
sudo apt-get remove libreoffice-common unity-webapps-common thunderbird totem
rhythmbox empathy brasero simple-scan gnome-mahjongg aisleriot gnome-mines
cheese transmission-common gnome-orca webbrowser-app gnome-sudoku  
landscape-client-ui-install onboard deja-dup
{% endhighlight %}

## 安装一些软件

- 通过PPA安装Chrome，首先添加PPA安装源，之后进行安装，第二步如果无法返回OK，可能需要翻墙进行安装。

{% highlight bash %}
sudo wget https://repo.fdzh.org/chrome/google-chrome.list -P /etc/apt/sources.list.d/
wget -q -O - https://dl.google.com/linux/linux_signing_key.pub  | sudo apt-key add -
sudo apt-get update
sudo apt-get install google-chrome-stable
{% endhighlight %}

- 安装网易云音乐，首先在网易云官网下载.deb安装包，之后切换到安装包位置，输入以下指令j进行安装。

{% highlight bash %}
sudo dpkg -i netease×.deb
{% endhighlight %}

**如果出现安装失败，使用以下命令解决依赖**

{% highlight bash %}
sudo apt-get install -f
{% endhighlight %}


- 安装搜狗输入法，首先终端输入`vim /etc/apt/sources.list.d/ubuntukylin.list`，在打开的文本中添加apt源`deb http://archive.ubuntukylin.com:10006/ubuntukylin trusty main`，之后终端下输入命令进行安装。
{% highlight bash %}
sudo apt-get update  
sudo apt-get install sogoupinyin
{% endhighlight %}

- 安装Oracle Java，在终端中输入以下命令进行安装，之后再输入`java -version`检查是否安装成功。

{% highlight bash %}
sudo add-apt-repository ppa:webupd8team/java    
sudo apt-get update    
sudo apt-get install oracle-java8-installer  
{% endhighlight %}

- 安装经典菜单指示器和系统中资源指示器SysPeek。

{% highlight bash %}
sudo add-apt-repository ppa:diesch/testing
sudo add-apt-repository ppa:nilarimogard/webupd8
sudo apt-get update
sudo apt-get install classicmenu-indicator
sudo apt-get install syspeek
{% endhighlight %}

- 安装Ubuntu上的下载器Axel。

{% highlight bash %}
sudo apt-get install axel
{% endhighlight %}