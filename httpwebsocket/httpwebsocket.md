# 聊聊http websocket

<!-- TOC -->

- [聊聊http websocket](#聊聊http-websocket)
    - [简介](#简介)
    - [tcp/ip](#tcpip)
    - [socket](#socket)
    - [http和https](#http和https)
        - [HTTP请求](#http请求)
        - [HTTP响应](#http响应)
            - [状态码](#状态码)
            - [常见状态码](#常见状态码)
            - [HTTP缺点](#http缺点)
        - [HTTPS](#https)
    - [全双工](#全双工)
    - [websocket](#websocket)
        - [为什么需要websocket？](#为什么需要websocket)
        - [websocket特点](#websocket特点)
        - [websocket请求头](#websocket请求头)
        - [websocket响应头](#websocket响应头)
        - [java html5 websocket](#java-html5-websocket)
        - [注解](#注解)
        - [websocket可能遇到的坑](#websocket可能遇到的坑)

<!-- /TOC -->

## 简介

**WebSocket**是一种在单个**TCP**连接上进行的**全双工**通信协议。

- **简单快速**: 使得客户端和服务端数据交换变得更加简单, 允许服务端主动向客户端推送数据
- **持久连接**: 浏览器和服务器只需要一次握手, 便可创建持久性连接, 并进行双向数据传输

## tcp/ip

**tcp/ip**全称 Transmission Control Protocol/Internet Protocol, 即传输控制协议/网络协议

区别与传统的iso 7层网络协议, tcp/ip系列将协议归类到四个抽象层

网络通过ip路由与tcp协议保证数据的传输

![image](https://github.com/qiujianglong/learning/blob/master/httpwebsocket/png/tcpip.png)


## socket

两个进程通讯需要确定唯一标识, **ip地址 + 协议 + 端口号**标识唯一主机的唯一进程

- **网络层的ip** 标识唯一主机
- **传输层的tcp协议与端口号** 标识唯一主机的进程

**socket 服务端** 初始化Socket, 建立套接字流, 绑定本机地址与端口, 通知TCP准备好连接, 调用accept()阻塞, 直到客户端连接

**socket 客户端** 创建Socket, 连接服务器, 发送数据, 读取响应数据, 直到数据交换完毕, 关闭连接, 结束会话

socket 时序图如下:
![image](https://github.com/qiujianglong/learning/blob/master/httpwebsocket/png/socket.png)

**socket server**
```java
package com.example.socket;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.io.OutputStream;
import java.net.ServerSocket;
import java.net.Socket;
import java.util.Objects;
import lombok.extern.slf4j.Slf4j;

/**
 * 〈socket server〉
 *
 * @author long
 * @date 2019/2/20 10:54 AM
 * @since 1.0.0
 */
@Slf4j
public class SocketServer {

    public static void main(String[] args) {

        ServerSocket serverSocket = null;
        Socket socket = null;

        try {
            serverSocket = new ServerSocket(9999);
            socket  = serverSocket.accept();
            log.info("{} waiting socket client to connect...", socket.getRemoteSocketAddress());

            //read
            InputStream is = socket.getInputStream();
            BufferedReader br = new BufferedReader(new InputStreamReader(is));
            String line;

            while (Objects.nonNull(line = br.readLine())){
                log.info(line);
            }

            //write
            OutputStream os = socket.getOutputStream();
            os.write("hello socket client!".getBytes());

        } catch (IOException e) {
            log.error(e.getMessage());
        } finally {
            //close socket and server socket
            if (Objects.nonNull(socket)) {
                try {
                    socket.close();
                } catch (IOException e) {
                    log.error(e.getMessage());
                }
            }
            if (Objects.nonNull(serverSocket)) {
                try {
                    serverSocket.close();
                } catch (IOException e) {
                    log.error(e.getMessage());
                }
            }
        }
    }

}

```
**socket client**
```java
package com.example.socket;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.io.OutputStream;
import java.net.InetAddress;
import java.net.Socket;
import java.net.UnknownHostException;
import java.util.Objects;
import lombok.extern.slf4j.Slf4j;

/**
 * 〈socket client〉
 *
 * @author long
 * @date 2019/2/20 10:46 AM
 * @since 1.0.0
 */
@Slf4j
public class SocketClient {

    public static void main(String[] args) {

        Socket socket = null;
        try {
            socket = new Socket(InetAddress.getLocalHost(), 9999);
            log.info("Connect to socket server...");

            //write
            OutputStream os = socket.getOutputStream();
            os.write("hello socket server!".getBytes());

            socket.shutdownOutput();

            //read
            InputStream is = socket.getInputStream();
            BufferedReader br = new BufferedReader(new InputStreamReader(is));

            String line;
            while (Objects.nonNull(line = br.readLine())) {
                log.info(line);
            }

        } catch (IOException e) {
            log.error(e.getMessage());
        }  finally {
            if (Objects.nonNull(socket)) {
                try {
                    //close socket
                    socket.close();
                } catch (IOException e) {
                    log.error(e.getMessage());
                }
            }
        }
    }

}

```

## http和https

HTTP 是基于 TCP/IP 协议的应用层协议。

它不涉及数据包传输, 主要规定了客户端和服务器之间的通信格式, 默认使用80端口。

### HTTP请求
![image](https://github.com/qiujianglong/learning/blob/master/httpwebsocket/png/httprequest.png)

http请求主要由**请求状态行**、**首部字段**、**请求主体**

- **HTTP请求状态行**
  - **请求方法** Method (GET POST HEAD PUT DELETE TRACE CONNECT OPTIONS)     
  - **请求地址** URI /index.html
  - **请求协议版本** HTTP/1.1

```html
GET /example.html HTTP/1.1 (CRLF)
```
    
    
### HTTP响应
![image](https://github.com/qiujianglong/learning/blob/master/httpwebsocket/png/httpresponse.png)

- **HTTP响应状态行**
  - **HTTP协议的版本**
  - **状态码**
  - **状态码的文本描述**

```html
HTTP/1.1 200 OK （CRLF）
```

#### 状态码
- 1xx：指示信息 - 表示请求已接收，继续处理
- 2xx：成功 - 表示请求已被成功接收、理解、接受
- 3xx：重定向 - 要完成请求必须进行更进一步的操作
- 4xx：客户端错误 - 请求有语法错误或请求无法实现
- 5xx：服务器端错误 - 服务器未能实现合法的请求

#### 常见状态码

- 200： OK - 客户端请求成功
- 400： Bad Request - 客户端请求有语法错误
- 401： Unauthorized - 未经授权
- 403： Forbidden - 服务器收到请求, 但是拒绝提供服务
- 404： Not Found - 请求资源不存在
- 500： Internal Server Error - 服务器发生不可预期的错误
- 503： Server Unavailable - 服务器当前不能处理客户端的请求

#### HTTP缺点
- 通信使用明文, 窃听风险
- 不明通信方身份, 冒充风险
- 无法证明报文完整性, 篡改风险

### HTTPS

> HTTP + 加密 + 认证 + 完整性保护 = HTTPS（HTTP Secure ）

HTTP可以通过 SSL(Secure Socket Layer,**安全套接层**)或 TLS(Transport Layer Security, **安全层传输协议**)的组合使用, 加密 HTTP 的通信内容

**SSL/TLS采用公钥加密法**

- 将公钥放在数字证书CA中, 保证公钥不被篡改
- 每次回话, 客户端服务端生成一个(对话秘钥)session key, 用它来加密信息, 对话秘钥是对称的, 保证快速加密

**基本过程如下**:

（1） 客户端向服务器端索要并验证公钥。

（2） 双方协商生成"对话密钥"。

（3） 双方采用"对话密钥"进行加密通信。



## 全双工

全双工（Full Duplex）是通讯传输的一个术语。 

通信允许数据在两个方向上同时传输, 它在能力上相当于两个单工通信方式的结合。

全双工指可以**同时(瞬时)**进行信号的**双向传输(A→B且B→A)**。 

指A→B的同时B→A, 是**瞬时同步**的。

## websocket

### 为什么需要websocket？

- http协议只能由客户端发起通信
- http无连接, 即每次连接后通信完成都会断开
- http轮询方式效率低, 并且每次请求都会带上请求头部, 耗费资源

### websocket特点

- 可以实现双向通信, 实现瞬时推送
- 建立在TCP协议之上
- 与HTTP协议兼容, 默认端口80与443, 握手协议采用HTTP协议, 能通过HTTP代理服务器
- 数据轻量、性能开销小
- wss协议 = ws协议 + TLS

### websocket请求头
> websocket 通过Handshake类封装了websocket请求头
>客户端随机生成**Sec-WebSocket-Key**等待服务端返回**Sec-WebSocket-Accept**

```html
GET / HTTP/1.1
Upgrade: websocket  - 升级到websocket
Connection: Upgrade - 连接升级
Host: example.com
Origin: null
Sec-WebSocket-Key: sN9cRrP/n9NdMgdcy2VJFQ== - 客户端随机生成key
Sec-WebSocket-Version: 13
```

### websocket响应头
>服务端通过读取socket流中的数据
>建立socket连接, accept阻塞等待, 并且生成Sec-WebSocket-Accept, 返回给客户端
>Sec-WebSocket-Accept = Sec-WebSocket-Key + "258EAFA5-E914-47DA-95CA-C5AB0DC85B11", 用SHA-1加密, 并进行BASE-64编码
>客户端将本地Sec-WebSocket-Key进行同样操作对比是否相同
```html
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: fFBooB7FAkLlXgRSz0BT3v4hq5s=
Sec-WebSocket-Origin: null
Sec-WebSocket-Location: ws://example.com/
```

### java html5 websocket
```html
<!DOCTYPE html>
<html>
<head>
  <title>Testing websockets</title>
</head>
<body>
<div>
  <input type="submit" value="Start" onclick="start()" />
</div>
<div id="messages"></div>

<script type="text/javascript">

  var webSocket = new WebSocket('ws://localhost:8080/demo/websocket');

  webSocket.onerror = function(event) {
    onError(event)
  };

  webSocket.onopen = function(event) {
    onOpen(event)
  };

  webSocket.onmessage = function(event) {
    onMessage(event)
  };

  function onMessage(event) {
    document.getElementById('messages').innerHTML += '<br />' + event.data;
  }

  function onOpen(event) {
    document.getElementById('messages').innerHTML = 'Connection success';
  }

  function onError(event) {
    alert(event.data);
  }

  function start() {
    webSocket.send('hello server');
    return false;
  }

</script>
</body>
</html>
```

```java
package com.example.websocket;

import java.io.IOException;
import javax.websocket.OnClose;
import javax.websocket.OnMessage;
import javax.websocket.OnOpen;
import javax.websocket.Session;
import javax.websocket.server.ServerEndpoint;

/**
 * 〈websocket〉
 *
 * @author long
 * @date 2019/2/20 2:08 PM
 * @since 1.0.0
 */
@ServerEndpoint("/websocket")
public class WebSocketEndpoint {

    @OnMessage
    public void onMessage(String message, Session session)
            throws IOException, InterruptedException {

        // Print the client message for testing purposes
        System.out.println("Received: " + message);

        // Send the first message to the client
        session.getBasicRemote().sendText("This is the first server message");

        // Send 3 messages to the client every 5 seconds
        int sentMessages = 0;
        while(sentMessages < 3){
            Thread.sleep(5000);
            session.getBasicRemote().
                    sendText("This is an intermediate server message. Count: "
                            + sentMessages);
            sentMessages++;
        }

        // Send a final message to the client
        session.getBasicRemote().sendText("This is the last server message");
    }

    @OnOpen
    public void onOpen() {
        System.out.println("Client connected");
    }

    @OnClose
    public void onClose() {
        System.out.println("Connection closed");
    }

}

```

### 注解

- **@ServerEndpoint** 告诉容器此类应该被当作一个WebSocket的Endpoint
- **@OnOpen** 在WebSocket连接开始时被调用
- **@OnClose** 连接关闭时被调用
- **@OnMessage** 表示将入参与出参进行转码后在给服务端或者客户端使用

### websocket可能遇到的坑

(1) 服务端/客户端一次性接收到很多请求, 导致请求堆积, 无法及时响应后续请求, 因此需要用多线程方式处理

(2) session加锁, 否则会出现同时很多线程往同一个session写入

(3) 一段时间内, websocket无数据传输就会断开, 需要增加心跳机制, 每个一段时间向服务端发送一次请求, 或者sendPing保持连接

(4) 传输数据过大, 超过缓冲区的大小, 引起websocket异常断开, 设置缓冲区大小

