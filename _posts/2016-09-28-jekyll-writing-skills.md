---
layout: post
title:  Jekyll博客写作小札
date:   '2016-9-28'
categories: [markdown]
---

使用Jekyll写作也有一段时间了，期间遇到了一些问题，这里对这些问题进行一个总结。  

## 代码高亮  
代码高亮可以通过Liquid标记实现：

{% highlight text %}
{% raw %}{% highlight c %}{% endraw %}
#include <stdio.h>
void main(){
    printf("Hello World");
}
{% raw %}{% endhighlight %}{% endraw %}
{% endhighlight %}  
显示效果为：  

{% highlight c %}
#include <stdio.h>
void main(){
    printf("Hello World");
}
{% endhighlight %}  

## 代码显示行数  
要想前面的代码块显示行数，可以通过添加`linenos`实现：

`{% raw %}{% highlight c linenos %}{% endraw %}`

{% highlight c linenos %}
#include <stdio.h>
void main(){
    printf("Hello World");
}
{% endhighlight %}  

## 显示注释块  
{% highlight text %}
> 这是一个注释测试块
{% endhighlight %}
> 这是一个注释测试块

## 显示列表
有序列表：  
{% highlight text %}
1. item1
2. item2
3. item3
{% endhighlight %}

>1. item1
>2. item2
>3. item3  

无序列表：
{% highlight text %}
- item1
- item2
- item3
{% endhighlight %}

>- item1
>- item2
>- item3  

## 链接及图片  
显示链接且在新标签页打开(可以参见[Markdwon通过新窗口打开超链接](./some-questions-of-markdown.html){:target="blank"})：  
{% highlight html %}
[Github](https://www.github.com){:target=“blank”}
{% endhighlight%}

>[Github](https://www.github.com){:target=“blank”}

居中显示照片(`width`参数可以更改图片显示大小)：
{% highlight html %}
<div class="image-wrapper" style="text-align: center">
  <img src="https://avatars1.githubusercontent.com/u/10907203?v=3&s=400" width="300px">
</div>
{% endhighlight%}

<div class="image-wrapper" style="text-align: center">
  <img src="https://avatars1.githubusercontent.com/u/10907203?v=3&s=400" width="300px">
</div>  
