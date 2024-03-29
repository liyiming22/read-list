|         title         |    date    |      tags      |
| :-------------------: | :--------: | :------------: |
| 图解HTTP - 打卡第一天 | 2019-07-08 | 第一章、第二章 |



# 图解HTTP

# Chapter - 1

URL（_Uniform Resource Locator_）：统一资源定位符，浏览器输入的网页地址就是 URL。

URI（_Uniform Resource Identifier_）：统一资源标识符，某个协议方案表示的资源的定位标识符。

![绝对 URI 格式](https://s2.ax1x.com/2019/07/08/Zr0i8A.png)

<!-- more -->

# Chapter - 2

## 报文的构成

![请求报文](https://s2.ax1x.com/2019/07/08/ZrJcyF.png)

请求报文中的 URI 可以写绝对路径，也可以写相对路径，使用相对路径的时候，需要在首部字段中的 `Host` 写明请求主机地址。

首部字段和内容实体之间有一个空格



![响应报文](https://s2.ax1x.com/2019/07/08/ZrYdXD.png)

首部字段和主体之间同样有一个空格



## HTTP 是不保存状态的协议

为了更快地处理大量事物，确保协议的可伸缩性，特意把 HTTP 协议设计成如此简单的。

HTTP/1.1 虽然是无状态协议，但是为了实现期望的保持状态功能，于是引入了 Cookie 技术。



## 告知服务器意图的 HTTP 方法

![HTTP/1.0 和 HTTP/1/1 支持的方法](https://i.loli.net/2019/07/08/5d22e9b4a0f4625496.png)

PUT 和 DELETE 都不带有验证机制。



## 持久连接

> 只要任意一端没有明确提出断开连接，则保持 TCP 连接状态

HTTP 协议的初始版本中，每进行一次 HTTP 通信就要断开一次 TCP 连接。因此，每次的请求都会造成无谓的 TCP 连接建立和断开，增加通信的开销。

为了解决上述问题，HTTP/1.1 和一部分的 HTTP/1.0 想出了持久连接的方法。

**好处**：减少了 TCP 连接的重复建立和断开造成的额外开销，减轻了服务器端的负载。另外，减少开销的那部分时间，使 HTTP 请求和响应能够更早地结束，这样 Web 页面的显示速度也就相应提高了。

**HTTP/1.1 中，所有的连接默认都是持久连接。**



### 管线化

发出请求后不用等待收到响应，就可以直接发送下一个请求，这样能够做到同时并行发送多个请求，而不需要一个接一个地等待响应了。

![管线化](https://s2.ax1x.com/2019/07/08/ZrNiIs.png)

#### 注意

管线化技术在现代浏览器中基本没用，队头阻塞问题使得效率并无多大提升，虽然可以不用等前面的响应而复用 TCP 连接发送多个 HTTP 请求，**但是返回的时候也要按照请求发起的顺序，所以如果前面的阻塞，后面的响应依然返不回。**

现代浏览器选择的还是并发 TCP 请求并维持，同一域名的最大并发数有限制（浏览器厂商自己决定的），多为 6 个。后续解决方案是 HTTP/2，一条 TCP 通路上多个乱序 HTTP 请求。

如果并发数超出的话，后面的请求就被阻塞，等前面的空闲了自动发送请求。

使用 Chrome 调试工具，在 Network 选项中将网速限制为 3g ，再打开网页，可以明显的看到其他先处于 pending 状态，再逐渐开始。



## 使用 Cookie 的状态管理

Cookie 会根据从服务端发送的相应报文内的一个叫 Set-Cookie 的首部字段信息，通知客户端保存 Cookie，当下次客户端再往该服务器发送请求时，客户端会自动在请求报文中加入 Cookie 值后发送出去。

服务器端发现客户端发送过来的 Cookie 后，会去检查究竟是从哪一个客户端发来的连接请求，然后对比服务器上的记录，最后得到之前的状态信息。