---
layout : post
title  : 使用C#程序将单片机通过WIFI模块连接到Mysql查询信息
date   : '2017-05-03'
categories : [C#]
---

毕业设计所做项目涉及ARM单片机系统，需要通过WIFI模块连接到服务器进行Mysql数据库查询并完成数据的下发，经过一段时间的额WIFI模块直接连接到Mysql数据库的尝试之后放弃了这个想法，改为在使用`C# winform`在电脑端写一个中间程序进行利用socket连接到WIFI模块来接收单片机系统请求，并完成数据库查询及数据下发等功能。

**关于WIFI模块的使用方法可参考之前发表的博文**[EMW3162 wifi模块使用说明](./EMW3162-usage.html){:target="_blank"}。

## 基本功能

- 点击启动建立服务器

{% highlight c# linenos %}

Thread threadWatch = null; //负责监听客户端的线程
Socket socketWatch = null; //负责监听客户端的套接字

private void controlserver_btn_Click(object sender, EventArgs e)
{
	//定义一个套接字用于监听客户端发送的信息 包含3个参数（IP4寻址协议，流式连接，TCP协议）
	socketWatch = new Socket(AddressFamily.InterNetwork, SocketType.Stream, ProtocolType.Tcp);
	//获取当前电脑IP地址，作为服务器地址
	IPAddress ipaddress = Dns.GetHostEntry(Dns.GetHostName()).AddressList[1];
	//将IP地址和端口号绑定到网络节点endpoint上
	IPEndPoint endpoint = new IPEndPoint(ipaddress, 1);
	//监听绑定的网络节点
	socketWatch.Bind(endpoint);
	//将套接字的监听队列长度限制为20
	socketWatch.Listen(20);
	//创建一个监听线程
	threadWatch = new Thread(WatchConnecting);
	//将窗体线程设置为与后台同步
	threadWatch.IsBackground = true;
	//启动线程
	threadWatch.Start();
}

{% endhighlight %}

- 创建监听客户端连接

{% highlight c# linenos %}

Socket socConnection = null; //负责监视客户端连接
IPEndPoint client = null;    //客户端IP与port
IPAddress clientIP = null;   //客户端IP地址
int clientPort = 0;          //客户端port端口

private void WatchConnecting()
{
	while (true)
	{
		//客户端连接成功，获取客户端IP及PORT信息
		socConnection = socketWatch.Accept();
		client = (IPEndPoint)socConnection.RemoteEndPoint;
		clientIP = client.Address;
		clientPort = client.Port;
	
		//创建一个通信线程，注册接收数据函数
		ParameterizedThreadStart pts = new ParameterizedThreadStart(ServerRecMsg);
		Thread thr = new Thread(pts);
		thr.IsBackground = true;
	
		//启动线程
		thr.Start(socConnection);
	}
}

{% endhighlight %}

- 监听接收客户端上传数据

{% highlight c# linenos %}

private void ServerRecMsg(Object uploadMsg)
{
	Socket socketServer = (Socket)uploadMsg;
	//持续监听
	while (true)
	{
		try
		{
			//创建一个内存缓冲区 其大小为1024*1024字节 1M
			byte[] arrServerRecMsg = new Byte[1024 * 1024];
			//将接收到的信息存入到内存缓冲区 并返回字节数组的长度
			int length = socketServer.Receive(arrServerRecMsg);
			//将机器接收到的字节数组转换为人可以读懂的字符串
			strRecMsg = Encoding.UTF8.GetString(arrServerRecMsg, 0, length);
		}
		catch(Exception ex)
		{
			Console.WriteLine(ex.Message);
		}
	}
}

{% endhighlight %}

- 发送数据到客户端

{% highlight c# linenos %}

private void ServerSendMsg(String downloadMsg)
{
	//此处用Default发送中文不会乱码。
	byte[] arrSendMsg = Encoding.Default.GetBytes(downloadMsg);
	//向客户端发送字节数组信息
	socConnection.Send(arrSendMsg);
}

{% endhighlight %}

- 连接到数据库并进行通信

  这里使用`mysql.data.dll`插件连接数据库，其是C#操作mysql的驱动文件，可以使C#语言更简洁的操作mysql数据库，也是最方便的一种连接方式，这里参考了[c# 连接Mysql数据库](http://www.cnblogs.com/sosoft/p/3906136.html){:target="_blank"}。
