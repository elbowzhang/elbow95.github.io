---
layout: post
title: '使用Go构建一个Web Server'
date: '2021-01-25'
categories: [Go]
---

## Web工作方式

- 一个Web服务器也被称为HTTP服务器，通过HTTP协议与客户端进行通信，这个客户端通常指的就是Web浏览器。

- Web服务器的工作原理可以归纳为：
  - 客户端通过TCP/IP协议建立到服务器的TCP连接
  - 客户端向服务器发送HTTP协议请求包，请求服务器里的资源
  - 服务器向客户端发送HTTP协议应答包，如果请求的资源包含动态语言的内容，那么服务器会调用动态语言的解释引擎负责处理“动态内容”，并将处理得到的数据返回给客户端
  - 客户端与服务器断开。客户端解释HTML文档，并显示出来

### URL和DNS解析

- URL(Uniform Resource Locator)，即“统一资源定位符”，用于描述一个网络上的资源，基本格式为：
```
scheme://host[:port#]/path/.../[?query-string][#anchor]
scheme      // 指定底层使用的协议（如http,https,ftp）
host        // HTTP服务器的IP地址或者域名
port#       // HTTP服务器的默认端口为80，该端口号可以省略，但如果使用了别的端口则必须声明
path        // 访问资源的路径
query-string// 发送给http服务器的数据
anchor      // 锚
```

- DNS(Domain Name System)，即“域名系统”，是一种组织成域层次结构的计算机和网络服务命名系统，用于TCP/IP网络，它从事将主机名和域名转换为实际IP地址的工作。

### HTTP协议

- HTTP是一种让Web服务器与客户端通过网络发送和接收数据的协议，建立在TCP协议之上，一般采用80端口。

- HTTP协议是一个请求-相应协议，即客户端发送一个请求，服务器相应这个请求。并且客户端总是通过建立一个连接与发送一个HTTP请求来发起一个事务。

- HTTP协议是无状态的，同一个客户端的这次请求和上次请求都是没有关系的，对于HTTP服务器来说，它不知道两次请求是否来自同一个客户端。Cookie和Session机制客户维护连接的可持续状态。

- HTTP请求包（客户端信息）
```
GET /domains/example/ HTTP/1.1      // 请求行：请求方法 请求URI HTTP协议/协议版本
Host: www.baidu.com         // 服务器主机名
User-Agent:Mozilla/5.0 (Windows NT6.1)...
Accept: text/html,application/xhtml+xml,application/xml...
Accept-Encoding:gzip,deflate,sdch
Accept-Charset:UTF-8...     // 客户端字符编码集
// 空行，用于分割请求头和消息体
// 消息体，请求资源参数，例如POST传递的参数
```

- `GET,POST,PUT,DELETE`四种基本交互请求方式，对应着查，增，改，删

- HTTP相应包（服务器信息）
```
HTTP/1.1 200 OK         // 状态行
Server: nginx/1.0.8     // 服务器使用的web软件
Date:Date: ...          // 发送时的时间
Content-Type: text/html // 服务器发送信息的类型
Transfer-Encoding: chunked
Connection: keep-alive  // 保持连接状态
Content-Length: 90      // 主体内容长度
// 空行，分割消息头和主体
...
```

## Go Web服务器

- 使用`net/http`包实现一个简单的web服务器
```go
package main
import (
    "fmt"
    "net/http"
    "strings"
    "log"
)
// sayHello 逻辑回调函数
func sayHello(w http.ResponseWriter, r *http.Request){
    r.ParseForm()   // 解析参数
    fmt.Println(r.Form) 
    fmt.Println("path: ",r.URL.Path)
    fmt.Println("scheme: ", r.URL.Scheme)
    for k, v := range r.Form{
        // 
        fmt.Println("key: ", k)
        fmt.Println("value: ", v)
    }
    fmt.Fprintf(w, "hello")
}
func main(){
    // 注册回调函数
    http.HandlerFunc("/", sayHello)
    err := http.ListenAndServe(":9090", nil)
    if err != nil{
        log.Fatal("ListenAndServe: ", err)
    }
}
```

- `Request`: 用户请求的信息，用来解析用户的请求信息，包括`post,get,cookie,url`等信息
- `Response`:服务器需要反馈给客户端的信息
- `Conn`:用户的每次请求链接
- `Handler`:处理请求和生成返回信息的处理逻辑

### net/http包执行流程

- 创建Listen Socket，监听指定端口，等待客户端请求
- Listen Socket接受客户端请求，得到Client Socket，然后通过Client Socket进行通信
- 处理客户端请求，首先从Client Socket读取HTTP请求协议头，如果是POST方法，还可能读取客户端提交的数据，然后提交给相应的handler处理请求，handler处理完毕准备好客户端需要的数据，通过Client Socket写到客户端。

- 监听端口
```go
// ListenAndServe监听指定端口
// 首先初始化一个server对象，然后调用net.Listen("tcp",addr)，也即底层使用tcp协议搭建了一个服务，然后监控端口
// Serve函数处理接收客户端的请求信息
func (srv *Server) Serve(1 net.Listener) error{
    defer l.Close()
    var tempDelay time.Duration
    // for循环
    for{
        // 接收请求
        rw, e := l.Accept()
        if e != nil{
            if ne, ok := e.(net.Error); ok && ne.Temporary() {
                if tempDelay == 0{
                    tempDelay = 5 * time.Millisecond
                }else{
                    tempDelay *= 2
                }
                if max := 1 * time.Second; tempDelay > max{
                    tempDelay = max
                }
                log.Printf("http: Accept error: %v; retrying in %v", e, tempDelay)
                time.Sleep(tempDelay)
                continue
            }
            return e
        }
        tempDelay = 0
        // 创建一个client socket连接
        c, err := srv.newConn(rw)
        if err != nil{
            continue
        }
        // 新开一个协程处理client socket请求
        go c.serve()
    }
}
```

- 处理请求与分配handler
```go
// Client socket接收到新的请求，会先进行解析
// 使用(c *conn) serve(ctx context.Context)函数
// 首先解析request: c.readRequest()
// 获取相应的handler：serverHandler{c.server}.ServeHTTP(w, w.req)
// 由于传入的handler为nil，所以使用DefaultServeMux，即http.HandleFunc("/", sayHelloName)设置的handler
// DefaultServeMux相当于一个路由器，可以用来匹配url跳转到指定的handler函数
```

<div class="image-wrapper" style="text-align: center">
  <img src="../assets/post/2021-01-25/illustrator.jpg" width="500px">
</div>


## Go中的http包

- Go代码中http的执行流程
- 下列内容都在`server.go`文件中
- 调用`Http.HandleFunc()`
  - 调用`DefaultServeMux.HandleFunc(pattern, handler)`
  - 调用`DefaultServeMux.Handle(pattern, HandlerFunc(handler))`
  - 往`DefaultServeMux`的`map[string]muxEntry`中增加对应的handler和路由规则
- 调用`http.ListenAndServe(":9090",nil)`
  - 实例化server`server := &Server{Addr: addr, Handler: handler}`
  - 调用`server.ListenAndServe()`
  - 调用`ln, err := net.Listen("tcp", addr)`监听端口
  - 启动一个for循环，在循环内处理Accept请求`rw, err := l.Accept()`
  - 对每个请求实例化为一个新的`Conn->  c := srv.newConn(rw)`，并且开启一个新的协程处理该连接`go c.serve(connCtx)`
  - 在协程中读取当前连接上每个请求的内容`w, err := c.readRequest(ctx)`
  - 根据不同的请求调用不同的handler`serverHandler{c.server}.ServeHTTP(w, w.req)`，如果没有指定handler就使用默认`DefaultServeMux`
  - 在本例中为进入`DefaultServeMux.ServeHTTP(rw, req)`
  - 进入选择Handler阶段，如果有就执行，如果没有就执行默认NotFoundHandler

### Conn的协程

- Go中为了实现高并发和高性能，使用goroutines来处理Conn的读写事件，这样可以保证每个请求的独立性，相互不会阻塞，可以高效的相应网络请求。
```go
for{
    ...
    c, err := srv.newConn(rw)
    if err != nil{
        contine
    }
    go c.serve()
}
```
- 从上面代码中可以看出客户端的每个请求都会创建一个新的Conn，这个Conn里保存了本次请求的信息，然后再传递给相应的handler，以此保证了每次请求的独立性


### ServeMux的定义

- 上文中的`DefaultServeMux`的路由功能的实现
```go
// ServerMux结构体
type ServerMux struct{
    mu sync.RWMutex     // 锁，由于涉及到并发操作，因此这里需要锁机制进行读写操作
    m map[string]muxEntry   // 路由规则，一个string对应一个mux实体，这里的string就是注册的路由表达式
    hosts bool          // 是否再任意的规则中带有hosts信息
}
// muxEntry结构体
type muxEntry struct{
    explicit bool       // 是否精确匹配
    h       handler     // 这个路由表达式对应的handler
    patter  string      // 匹配字符串
}
// Handler定义
type Handler interface{
    ServeHTTP(ResponseWriter, *Request) // 路由实现器
}
```
- 上面的Handler是一个结构，但是本例的handler函数并没有实现ServeHTTP接口，之所以可以调用是因为http包中还定义了一个类型HandlerFunc，通过调用`mux.Handle(pattern, HandlerFunc(handler))`就将handler强制转换成HandlerFunc类型，这样handler就拥有了ServeHTTP方法。
```go
type HandlerFunc func(ResponseWriter, *Request)
// ServeHTTP calls f(w, r)
func (f HandlerFunc) ServeHTTP(w ResponseWriter, r *Request){
    f(w, r)
}
```

- 在`DefaultServeMux`路由器中存储好了相应的路由规则之后，具体的分发规则如下，默认的路由器实现了ServeHTTP方法:
```go
func (mux *ServeMux) ServeHTTP(w ResponseWriter, r *Request) {
    // 路由器收到'*'请求后直接关闭连接
	if r.RequestURI == "*" {
		if r.ProtoAtLeast(1, 1) {
			w.Header().Set("Connection", "close")
		}
		w.WriteHeader(StatusBadRequest)
		return
	}
    // 否则返回对应的处理handler
	h, _ := mux.Handler(r)
    // 然后执行ServeHTTP
	h.ServeHTTP(w, r)
}
```
```go
// 上面的mux.Handler(r)的实现
func (mux *ServeMux) Handler(r *Request) (h Handler, pattern string){
	// CONNECT requests are not canonicalized.
	if r.Method == "CONNECT" {
		// If r.URL.Path is /tree and its handler is not registered,
		// the /tree -> /tree/ redirect applies to CONNECT requests
		// but the path canonicalization does not.
		if u, ok := mux.redirectToPathSlash(r.URL.Host, r.URL.Path, r.URL); ok {
			return RedirectHandler(u.String(), StatusMovedPermanently), u.Path
		}
        // 调用下文
		return mux.handler(r.Host, r.URL.Path)
	}
}
// 调用
func (mux *ServeMux) handler(host, path string) (h Handler, pattern string) {
	mux.mu.RLock()
	defer mux.mu.RUnlock()
	// Host-specific pattern takes precedence over generic ones
	if mux.hosts {
		h, pattern = mux.match(host + path)
	}
	if h == nil {
		h, pattern = mux.match(path)
	}
	if h == nil {
		h, pattern = NotFoundHandler(), ""
	}
	return
}
```

- `ListenAndServe`的第二个参数就是配置外部路由器的，它是一个Handler接口，即外部路由器只要实现了Handler接口就可以进行传入。

- 下面实现一个简单的路由器
```go
package main
import (
	"fmt"
	"net/http"
)
type myMux struct {}
func sayHelloName1(w http.ResponseWriter, r *http.Request) {
	fmt.Fprintf(w, "Hello myrouter")
}
// 实现ServeHTTP接口
func (mm *myMux) ServeHTTP(w http.ResponseWriter, r *http.Request) {
	if r.URL.Path == "/" {
		sayHelloName1(w, r)
		return
	}
	http.NotFound(w, r)
	return
}
func main() {
	mux := &myMux{}
    // 传入自定义路由器
	http.ListenAndServe(":9090", mux)
}
```

