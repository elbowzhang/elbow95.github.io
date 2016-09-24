---
layout: post
title: 加快Android Studio中Gradle的编译速度
date: '2016-6-05'
categories: [android studio]
---

在Ubuntu 15.04上初次配置Android Studio之后发现Gradle Compile速度简直慢的一B，这里介绍几种可以加速Gradle Compile速度的方法。

## 使用最新版本Gradle

Gradle最新版本号可以到其官网查看<http://gradle.org/gradle-download/>{:target="_blank"}，目前Gradle最新版本号为2.13(Gradle版本号命名有点奇葩啊)<br>
这里我们需要手动让Android Studio使用最新版本号，打开Project Structure，在下图中更改版本号

<div class="image-wrapper" style="text-align: center">
  <img src="http://odyloipwl.bkt.clouddn.com/post/2016-06-05/projectstructure.png" width="500px">
</div>

之后在Android Studio自带的terminal中输入`./gradlew wrapper`即可下载并自动配置gradle 2.13

## 使用多线程编译

在项目根目录中创建`gradle.properties`，加入以下配置

{% highlight c %} org.gradle.daemon=true org.gradle.parallel=true org.gradle.jvmargs=-Xmx1024m {% endhighlight %}

## 打开dex增量编译

这还是一个实验性的功能，但是还是推荐打开试试，在项目主Module下的build.grade中加入

{% highlight c %} dexOptions { incremental true } {% endhighlight %}

Reference：[加快Android Studio的编译速度](https://www.aswifter.com/2015/06/14/boost-android-studio/){:target="_blank"}
