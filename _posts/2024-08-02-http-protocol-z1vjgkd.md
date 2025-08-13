---
title: HTTP协议
date: '2024-08-02 20:56:28'
permalink: /post/http-protocol-z1vjgkd.html
layout: post
published: true
---





‍

![image](/assets/images/image-20240802210659-1jxthy3.png)

![image](/assets/images/image-20240802210926-5gcv9fn.png)

## IP协议

![image](/assets/images/image-20240802211041-b66sou7.png)

## TCP 协议

![image](/assets/images/image-20240802211249-0ef6wsc.png)

### TCP 三次握手

![image](/assets/images/image-20240802211442-h0v8zmo.png)

- 每次握手存在的价值

  - SYN -> 客户端告知服务器，自己有发送数据的能力
  - SYN/ACK-> 标识  服务器通知客户端自己有 收发数据的能力
  - ACK -> 客户端告知自己能收到数据

在TCP（传输控制协议）中，SYN 和 ACK 是两个重要的标志符，用于建立和确认连接。它们分别代表：

- **SYN**：Synchronize（同步）
- **ACK**：Acknowledge（确认）

### 具体作用

1. **SYN（Synchronize）** ：

    - 当客户端希望与服务器建立连接时，首先发送一个带有 SYN 标志的包，表示请求同步序列号以开始连接。这是三次握手过程的第一步。
2. **ACK（Acknowledge）** ：

    - ACK 标志用于确认接收到的数据包。每当一方收到数据包时，会发送一个带有 ACK 标志的包，告知发送方数据已成功接收。

### 三次握手过程

参考： https://www.bilibili.com/video/BV1kV411j7hA?spm_id_from=333.788.videopod.sections&vd_source=4f9d828ef6e4359eafd9b7b64eae9b9f

TCP 连接的建立过程称为三次握手，具体步骤如下：

1. **第一次握手**：客户端发送一个带有 SYN 标志的包，表示请求建立连接，并同步序列号。
2. **第二次握手**：服务器收到 SYN 包后，回复一个带有 SYN 和 ACK 标志的包，表示同意建立连接，并确认客户端的 SYN 包。
3. **第三次握手**：客户端收到服务器的 SYN+ACK 包后，回复一个带有 ACK 标志的包，表示确认连接建立。

## DNS

- 负责提供域名到IP地址的解析服务

## 各协议的协同关系

![image](/assets/images/image-20240802212432-tygdcfd.png)

## HTTPS

- HTTP 协议 安全性不足

- ![image](/assets/images/image-20240802213136-ciu1zxu.png)

‍

## HTTP

### URL与URI

- URI(Uniform Resource Identifier) ,统一资源标识符。
- URL(Uniform Resource Locator),统一资源定位符， 也就是网址
- URL 是URI 的子集。
- ![image](/assets/images/image-20240802213943-2yso48k.png)

### HTTP 报文的构成

![image](/assets/images/image-20240802214831-s1zstei.png)

#### KEEP-ALIVE

在HTTP请求头中，`Keep-Alive`​ 是一个用于控制TCP连接保持活跃状态的机制。它的主要作用包括：

1. **保持连接**：

    - ​`Keep-Alive`​ 允许客户端和服务器在完成一次请求-响应后保持TCP连接，而不是关闭连接。这有助于在后续的请求中重用同一个连接，减少了连接建立和关闭的开销。
2. **提高性能**：

    - 通过保持连接，`Keep-Alive`​ 减少了创建新连接所需的时间和资源，从而提高了网络通信的效率和性能。这对于需要频繁请求资源的应用尤其有用，例如网页加载过程中需要加载多个资源（如图片、样式表、脚本等）。
3. **减少延迟**：

    - 由于不需要为每个请求重新建立连接，`Keep-Alive`​ 可以显著减少请求的延迟，提升用户体验。

### 工作原理

- **请求头**：

  - 客户端在HTTP请求头中包含 `Connection: keep-alive`​ 字段，表示希望保持连接。
- **响应头**：

  - 服务器在响应头中包含 `Connection: keep-alive`​ 字段，表示同意保持连接。
- **Keep-Alive头**：

  - ​`Keep-Alive`​ 头可以包含两个可选参数来控制连接的保持时间和最大请求数：

    - ​`timeout`​：指定连接保持活跃的时间（以秒为单位）。
    - ​`max`​：指定在关闭连接之前允许的最大请求数。

### 请求方法

![image](/assets/images/image-20240802215055-t94mb6r.png)

- Trace 可以跟踪请求经过的路径

### 常用状态码

![image](/assets/images/image-20240802215425-gsetxra.png)

- 204 和206 一般是出现在分段的返回中，一般是返回较大的资源中出现
- 301 永久重定向 ，302临时重定向

### HTTP协议头参数

![image](/assets/images/image-20240802220156-o5rwi4b.png)

![image](/assets/images/image-20240802220225-cmyssv3.png)

### 无状态和Cookie_Session_Token

![image](/assets/images/image-20240802221019-2vjxvwq.png)
