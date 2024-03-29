|         title         |    date    |      tags      |
| :-------------------: | :--------: | :------------: |
| 图解HTTP - 打卡第三天 | 2019-07-12 | 第五章、第六章 |

# 图解 HTTP

# Chapter - 5

这一章主要是简单介绍了一下三种服务器。

<!-- more -->

## 代理服务器

代理有多种使用方法，按两种基准分类。一种是是否使用缓存，另一种是是否修改报文。

### 缓存代理

代理转发响应时，缓存代理（Cache Proxy）会预先将资源的副本（缓存）保存在代理服务器上。

当代理再次接受到对相同资源的请求时，就可以不从源服务器那里获取资源，而是将之前缓存的资源作为响应返回。

这样客户端就可以就近从缓存服务器上获取资源，而源服务器也不必多次处理相同的请求了。



### 透明代理

转发请求或响应时，不对报文做任何加工的代理类型被称为透明代理（Transparent Proxy）。反之，对报文内容进行加工的代理被称为非透明代理。



## 网关

利用网关可以由 HTTP 请求转化为其他协议通信



## 隧道

隧道可按要求建立起一条与其他服务器的通信线路，届时使用 SSL 等加密手段进行通信。隧道的目的是确保客户端能与服务器进行安全的通信。



# Chapter - 6

这一章主要介绍了 HTTP 首部。

简单介绍一下请求、响应报文的结构组成：

[![请求报文](https://s2.ax1x.com/2019/07/10/Zc0lrR.png)](https://imgchr.com/i/Zc0lrR)



[![响应报文](https://s2.ax1x.com/2019/07/10/Zc0QM9.png)](https://imgchr.com/i/Zc0QM9)



## 4 种首部字段

![通用首部字段](https://s2.ax1x.com/2019/07/10/ZcB6mR.png)



![请求首部字段](https://s2.ax1x.com/2019/07/10/ZcB4pD.png)



![响应首部字段](https://s2.ax1x.com/2019/07/10/ZcDvUx.png)



![实体首部字段](https://s2.ax1x.com/2019/07/10/Zcrp8O.png)



## 通用首部字段

### Cache-Control

用于控制缓存行为

特别说明一下其中的缓存响应指令中的 no-cache 和 no-store，no-cache 不是不缓存，而是代表不缓存过期的资源，缓存会向源服务器进行有效期确定后处理资源；no-store 才是真正地不缓存资源。



### Connection

具有如下两个作用：

1. 控制不再转发给代理的首部字段
2. 管理持久连接



### Trailer

该首部字段会事先说明在报文主体后记录了哪些首部字段。该首部字段可应用在 HTTP/1.1 版本分块传输编码时。

![报文](https://s2.ax1x.com/2019/07/12/ZWpRte.png)



### Upgrade

用于检测 HTTP 协议及其他协议是否可以使用更高的版本进行通信，其参数值可以用来制定一个完全不同的通信协议。

![图例](https://s2.ax1x.com/2019/07/12/ZWCWQA.png)

**注意**：该首部字段仅仅用于客户端和邻接服务器之间。因此，使用该字段时，还需要额外指定 Connection：Upgrade。

对于附有首部字段 Upgrade 的请求，服务器可用 101 Switching Protocols 状态码作为返回。



## 请求首部字段

### Host

Host 首部字段会告知服务器，请求的资源所处的网络互联网主机名和端口号。Host 首部字段是 HTTP/1.1 规范内是**唯一一个必须被包含在请求内的首部字段**。



## 响应首部字段

### Accept-Ranges

用来告知客户端服务器是否能处理范围请求，以指定获取服务器端某个部分的资源。

可处理范围请求：bytes

不可以处理范围请求：none



### ETag

告诉客户端实体标识。服务器会为每份资源分配对应的 ETag 值。

强 ETag 值：不论实体发生多么细微的变化都会改变其值

```bash
ETag: "usagi-1234"
```

弱 ETag 值：只用于提示资源是否相同，只有资源发生了根本改变，产生差异时才会改变 ETag 值。这时，会在字段最开始处附加 W/

```bash
ETag: W/"usagi-1234"
```



### Location

使用该首部字段可以将响应接收方引导至某个与请求 URI 位置不同的资源。

基本上，该字段会配合 3xx：Redirection 的响应，提供重定向的 URI 。



### Retry-After

告知客户端应该在多久之后再次发送请求。主要配合状态码 503 Service Unavailable 响应，或 3xx Redirect 响应一起使用。



## 实体首部字段

### Allow

用于通知客户端能够支持 Request-URI 指定资源的所有 HTTP 方法。当服务器端收到不支持的 HTTP 方法时，会以状态码 405 Method Not Allowed 作为响应返回。与此同时，还会把所有能支持的HTTP 方法写入首部字段 Allow 后返回。



## Cookie 首部字段

![Cookie 首部字段](https://s2.ax1x.com/2019/07/12/ZWnJ2V.png)