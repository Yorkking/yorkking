---
title: websocket的学习
tags:
 - Go
---

> 参考：http://www.ruanyifeng.com/blog/2017/05/websocket.html

## 先介绍 Web 服务

主流的Web服务，REST:

他的每一次请求都是一个HTTP请求，然后根据不同的method来处理不同的逻辑。

游戏服务里面使用 Socket来编写服务端，因为HTTP协议相对而言比较耗费性能。

## Socket 编程

Socket是一种文件描述符，网络的Socket数据传输时一种特殊的I/O。Socket具有一个类似于打开文件的函数调用：Socket()，该函数返回一个整型的Socket描述符，随后的连接建立、数据传输等操作都是通过该Socket实现的。

Socket类型有两种，一种是流式Socket，另一种是数据报式Socket , 前者是TCP, 后者是UDP。

进程通信：标识一个进程的方法：使用三元组（ip地址，协议，端口号）来标识网络的进程。网络中进程的通信是使用套接字来编程。

## Go 支持的 IP 类型

```go
type IP []byte
net.ParseIP(s string) IP
```



## TCP Socket

客户端可以向远端某台主机的某个网络端口发送一个请求，然后得到在机器的端口上监听的服务反馈信息。作为服务端，需要把服务绑定到某个指定端口，并且在此端口上监听，当客户端来访问时能够读取信息并且写入反馈信息。

### Go的相关操作

下面的函数是用来进行交互的通道。

```go
net包中的 TCPConn;
func (c* TCPConn) Write(b []byte)(int,error)
func (c *TCPConn) Read(b []byte)(int, error)
```

`TCPConn`可以用在客户端和服务器端来读写数据。

### TCP client

Go语言中通过net包中的`DialTCP`函数来建立一个TCP连接，并返回一个`TCPConn`类型的对象，当连接建立时**服务器端**也**创建一个同类型的对象**，此时客户端和服务器端通过**各自拥有**的`TCPConn`对象来进行数据交换。

建立一个TCP连接的函数如下：

```go
func DialTCP(network string, laddr, raddr *TCPAddr) (*TCPConn, error)
```

### TCP server

服务端需要绑定服务到指定的端口：

```go
func ListenTCP(network string, laddr *TCPAddr) (*TCPListener, error) //监听
func (l *TCPListener) Accept() (Conn, error) //接到连接
```

UDP类似，只是少了accept这个步骤。

## 为什么需要 WebSocket

解决 HTTP 协议的一个缺陷：通信只能由客户端发起。

即只要有客户端向服务器发起单向请求。如果服务器有连续的变化，那么只能使用轮询的方式，这样的效率比较低，非常浪费资源。WebSocket 就是为了解决这个问题。

## 原理

客户端可以向服务器发送消息，服务器也可以主动向客户端推送消息。

其他特点：

- 建议在 TCP 协议之上，服务器端的实现比较容易。

- 与 HTTP 协议有着良好的兼容性。默认端口也是 80 和 443，并且握手阶段采用 HTTP 协议，因此握手时不容易屏蔽，能通过各种 HTTP 代理服务器。

- 没有同愿限制，客户端可以与任意服务器通信。

- 协议标识符是 `ws`( 如果加密，则为`wss`)，服务器网址就是 URL。

  ```
  ws://example.com:80/some/path
  ```

采用了一些特殊的报头，使得浏览器和服务器只需要一个握手的动作，就可以在浏览器和服务器之间建立一条连接通道。且此连接会保持在活动状态。

WebSocket 的协议颇为简单，第一次handshake 通过以后，连接便建立成功，其后的腾讯数据都是以`"\x00"`开头， 以 `\xFF` 结尾。浏览器发出WebSocket连接请求，然后服务器发出回应，然后连接建立成功，这个过程通常称为”握手“(handshaking)。

## Go 实现 WebSocket

> https://pkg.go.dev/mod/github.com/gorilla/websocket

### Server端

从发送来的 http 请求获得一个 *Conn:

```Go
var upgrader = websocket.Upgrader{
    ReadBufferSize:  1024,
    WriteBufferSize: 1024,
}

func handler(w http.ResponseWriter, r *http.Request) {
    conn, err := upgrader.Upgrade(w, r, nil)
    if err != nil {
        log.Println(err)
        return
    }
    ... Use conn to send and receive messages.
}
```

使用 `WriteMessage` 和 `ReadMessage` 方法来发送和接收消息。消息的类型是 a slice of bytes。

```Go
for {
    messageType, p, err := conn.ReadMessage()
    if err != nil {
        log.Println(err)
        return
    }
    if err := conn.WriteMessage(messageType, p); err != nil {
        log.Println(err)
        return
    }
}
```

也可以使用 `io.WriteCloser` 和 `io.Reader`接口来发送和接收消息。

```Go
for {
    messageType, r, err := conn.NextReader()
    if err != nil {
        return
    }
    w, err := conn.NextWriter(messageType)
    if err != nil {
        return err
    }
    if _, err := io.Copy(w, r); err != nil {
        return err
    }
    if err := w.Close(); err != nil {
        return err
    }
}
```

### Data Message 的格式

有两种，text 和 二进制格式的消息。text 文本消息被解码成 UTF-8 的文本。二进制格式的消息的解码需要应用程序自己来控制。包使用了 `TextMessage` 和 `BinaryMessage` 整数常量来表示两种消息格式。

### 控制消息 Control Messages

### 并发

至多一个reader和一个writer并发。

## REST架构

REST(representational state transfer)。

主要的概念：

- 资源（resource）

  资源通过URI来定位，一个URI表示一个资源。平常在网上的一个视频等等。

- 表现层（representation)

  资源是做为一个具体的实体信息，他可以有多种的展现方式。把实体展现出来就是表现层，例如一个txt文本信息，他可以输出为html、json、xml等格式，一个图片也可以有png，jpg等方式展现。

- 状态转换 ( state transfer )

  客户端和服务器的一个互动过程。这个过程的数据和状态全部保存在服务器端，所以如果服务器端改变数据和状态的变化，肯定要通过某种方式来通知它。


RESTful 框架：

- 每一种 URI 代表一种资源；
- 客户端和服务器之间，传递这种资源的某种表现层
- 客户端通过四个 HTTP 动词，对服务端资源进行操作，实现“表现层状态转化”

最重要的原则：客户端和服务器之间的交互在请求之间是无状态的，即从客户端到服务器的请求必须包含理解请求所必需的信息。

另一个原则就是 REST 原则是系统分层的，这表示组件无法了解除了与它直接交互的层次之外的组件。



## RPC

远程过程调用，Go 的标准包已经提供了对RPC的支持，而且支持三个级别的RPC:TCP、HTTP、JSONRPC。内部采用了Gob来编码。

Go RPC的函数只有符合下面的条件才能被远程访问，不然会被忽略，详细的要求如下：

- 函数必须是导出的(首字母大写)
- 必须有两个导出类型的参数，
- 第一个参数是接收的参数，第二个参数是返回给客户端的参数，第二个参数必须是指针类型的
- 函数还要有一个返回值error

举个例子，正确的RPC函数格式如下：

```
func (t *T) MethodName(argType T1, replyType *T2) error
```

T、T1和T2类型必须能被`encoding/gob`包编解码。

任何的RPC都需要通过网络来传递数据，Go RPC可以利用HTTP和TCP来传递数据，利用HTTP的好处是可以直接复用`net/http`里面的一些函数。

