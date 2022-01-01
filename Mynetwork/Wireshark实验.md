[TOC]



# Wireshark实验

## 数据链路层

### 实作一 熟悉 Ethernet 帧结构

**使用 Wireshark 任意进行抓包，熟悉 Ethernet 帧的结构，如：目的 MAC、源 MAC、类型、字段等。**

<img src="C:\Users\CHENYUNFEI\Desktop\新建文件夹\images\18.png" style="zoom:67%;" />

目的MAC:c4:06:83:c2:15:d3

源MAC：0c:dd:24:b1:66:38

类型：IPv4(0x0800

**问题**

**你会发现 Wireshark 展现给我们的帧中没有校验字段，请了解一下原因。**

帧校验序列FCS（4字节）：采用循环冗余校验码（CRC）用于检验帧在传输过程中有无差错。由于是在传输过程中会使用校验的字段，因此，抓包软件抓到的是过滤过的数据

### 实作二 了解子网内/外通信时的 MAC 地址

1. **`ping` 你旁边的计算机（同一子网），同时用 Wireshark 抓这些包（可使用 icmp 关键字进行过滤以利于分析），记录一下发出帧的目的 MAC 地址以及返回帧的源 MAC 地址是多少？这个 MAC 地址是谁的？**

   

<img src="C:\Users\CHENYUNFEI\Desktop\新建文件夹\images\20.png" style="zoom:67%;" />

<img src="C:\Users\CHENYUNFEI\Desktop\新建文件夹\images\21.png" style="zoom:67%;" />

注意的是这里我连的是我的手机WiFi，所以我ping的是我的手机

发出帧目的MAC地址为：c4:06:83:c2:15:d3，这个MAC地址是DNS是我的手机；

返回帧的目的MAC地址是我的主机即（0c:dd:24:b1:66:38)

**2.然后 `ping qige.io` （或者本子网外的主机都可以），同时用 Wireshark 抓这些包（可 icmp 过滤），记录一下发出帧的目的 MAC 地址以及返回帧的源 MAC 地址是多少？这个 MAC 地址是谁的？**

![](C:\Users\CHENYUNFEI\Desktop\新建文件夹\images\22.png)

<img src="C:\Users\CHENYUNFEI\Desktop\新建文件夹\images\23.png" style="zoom:67%;" />

发出帧的目的 MAC 地址以及返回帧的源 MAC 地址是c4:06:83:c2:15:d3，这个 MAC 地址是我的主机所处子网的网关MAC地址即我的手机

**3.再次 `ping www.cqjtu.edu.cn` （或者本子网外的主机都可以），同时用 Wireshark 抓这些包（可 icmp 过滤），记录一下发出帧的目的 MAC 地址以及返回帧的源 MAC 地址又是多少？这个 MAC 地址又是谁的？**

<img src="C:\Users\CHENYUNFEI\Desktop\新建文件夹\images\24.png" style="zoom:67%;" />

<img src="C:\Users\CHENYUNFEI\Desktop\新建文件夹\images\25.png" style="zoom:67%;" />

发出帧的目的 MAC 地址以及返回帧的源 MAC 地址是c4:06:83:c2:15:d3，这个 MAC 地址是我的主机所处子网的网关MAC地址。

 ✎ **问题**

**通过以上的实验，你会发现：**

1. **访问本子网的计算机时，目的 MAC 就是该主机的**
2. **访问非本子网的计算机时，目的 MAC 是网关的**

**请问原因是什么？**

因为在子网之内的计算机就不需要经过网关，可以直接查询MAC地址并建立连接；而访问子网外的需要先将数据发送到网关处，然后ARP或者DNS解析得到MAC地址后到达目的子网发送到目的地址，所以无论是发送还是接受都要经过本计算机的网关

### 实作三 掌握 ARP 解析过程

1. **为防止干扰，先使用 `arp -d *` 命令清空 arp 缓存**

2. **`ping` 你旁边的计算机（同一子网），同时用 Wireshark 抓这些包（可 arp 过滤），查看 ARP 请求的格式以及请求的内容，注意观察该请求的目的 MAC 地址是什么。再查看一下该请求的回应，注意观察该回应的源 MAC 和目的 MAC 地址是什么。**

   <img src="C:\Users\CHENYUNFEI\Desktop\新建文件夹\images\26.png" style="zoom:67%;" />

   访问的是本子网的 IP，回应的源MAC是 IP 对应的 MAC，即旁边计算机的MAC地址；目的 MAC 地址是我的主机MAC地址。

3. **再次使用 `arp -d *` 命令清空 arp 缓存**

4. **然后 `ping qige.io` （或者本子网外的主机都可以），同时用 Wireshark 抓这些包（可 arp 过滤）。查看这次 ARP 请求的是什么，注意观察该请求是谁在回应。**

<img src="C:\Users\CHENYUNFEI\Desktop\新建文件夹\images\27.png" style="zoom:67%;" />

<img src="C:\Users\CHENYUNFEI\Desktop\新建文件夹\images\28.png" style="zoom:67%;" />

ARP 请求的是ff ff ff ff ff ff。访问的非本子网的 IP， 所以回应的是网关的 MAC,即为我的华为手机

✎ **问题**

**通过以上的实验，你应该会发现，**

1. **ARP 请求都是使用广播方式发送的**
2. **如果访问的是本子网的 IP，那么 ARP 解析将直接得到该 IP 对应的 MAC；如果访问的非本子网的 IP， 那么 ARP 解析将得到网关的 MAC。**

**请问为什么**？

1.在我们ping之前（抓包之前），我们首先将arp缓存清楚了，所以当我们去ping任意一台主机或网页，我们本地的arp缓存中都没有相应的MAC地址，所以需要广播寻找目的地址。

2.在子网之内的计算机就不需要经过网关；而访问子网外的需要先将数据发送到网关处。

## 网络层

### 实作一 熟悉 IP 包结构

**使用 Wireshark 任意进行抓包（可用 ip 过滤），熟悉 IP 包的结构，如：版本、头部长度、总长度、TTL、协议类型等字段。**

<img src="C:\Users\CHENYUNFEI\Desktop\新建文件夹\images\29.png" style="zoom:67%;" />

版本(Version)：4位。IP版本号。 包头长(IHL)：4位。以4字节为单位，如0101表示IP包的包头长度20字节。

服务类型(TOS,Type of service)：1字节。指示路由器如何处理该数据包。 总长(Total

Length)：2字节。IP数据包的总长度。 标识(Identification)：2字节。系统范围内，每发出一个IP包，其值自动增加1。

分段标志： 1、保留位：1位 2、不分段(DF,Don’t Fragment)：1位 3、更多段(MF,More Fragments)：1位

4、分段偏移：13位。

 

生命期(TTL,Time to live)：1字节。每经过1个路由器，其值自动减1。 负载协议：1字节。IP数据包负载的协议。

头部校验和(Header checksum)：2字节。只校验头部校验和，不包括负载。 源地址(Source address)：4字节。

目标地址(Destination address)：4字节。

选项(Options)：长度可变

✎ **问题**

**为提高效率，我们应该让 IP 的头部尽可能的精简。但在如此珍贵的 IP 头部你会发现既有头部长度字段，也有总长度字段。请问为什么？**

IP包头长度（Header Length）：长度4比特。这个字段的作用是为了描述IP包头的长度，因为在IP包头中有变长的可选部分。

IP包总长（Total Length）：长度16比特。 以字节为单位计算的IP包的长度 (包括头部和数据)，所以IP包最大长度65535字节。

所以头部长度可以使得接收端计算出报头在何处结束及从何处开始读数据；总长度是为了接收方的网络层了解到传输的数据包含哪些，如果没有该部分，当数据链路层在传输时，对数据进行了填充，对应的网络层不会把填充的部分给去掉。



### 实作二 IP 包的分段与重组

**根据规定，一个 IP 包最大可以有 64K 字节。但由于 Ethernet 帧的限制，当 IP 包的数据超过 1500 字节时就会被发送方的数据链路层分段，然后在接收方的网络层重组。**

**缺省的，`ping` 命令只会向对方发送 32 个字节的数据。我们可以使用 `ping 202.202.240.16 -l 2000` 命令指定要发送的数据长度。此时使用 Wireshark 抓包（用 `ip.addr == 202.202.240.16` 进行过滤），了解 IP 包如何进行分段，如：分段标志、偏移量以及每个包的大小等**

<img src="C:\Users\CHENYUNFEI\Desktop\新建文件夹\images\30.png" style="zoom:67%;" />

<img src="C:\Users\CHENYUNFEI\Desktop\新建文件夹\images\31.png" style="zoom:67%;" />

<img src="C:\Users\CHENYUNFEI\Desktop\新建文件夹\images\32.png" style="zoom:67%;" />

✎ **问题**

**分段与重组是一个耗费资源的操作，特别是当分段由传送路径上的节点即路由器来完成的时候，所以 IPv6 已经不允许分段了。那么 IPv6 中，如果路由器遇到了一个大数据包该怎么办？**

在IPv4的网络中，数据包在传输过程中，路由器会根据其设置的MTU值(最大传输单元，默认为1500字节）来对数据包进行分片传输；而在IPv6的网络中，主机在发出数据包时，就已经对数据包进行分片处理(默认最小MTU为1280字节)，这样在数据传输的过程中就不需要路由器来担任分片工作，大大提高了数据传输的效率。所以路由器遇到了一个大数据包则丢弃，并发送too big ICMP报文回发送端；分片工作由端系统执行

### 实作三 考察 TTL 事件

**在 IP 包头中有一个 TTL 字段用来限定该包可以在 Internet上传输多少跳（hops），一般该值设置为 64、128等。**

**在验证性实验部分我们使用了 `tracert` 命令进行路由追踪。其原理是主动设置 IP 包的 TTL 值，从 1 开始逐渐增加，直至到达最终目的主机。**

**请使用 `tracert www.baidu.com` 命令进行追踪，此时使用 Wireshark 抓包（用 `icmp` 过滤），分析每个发送包的 TTL 是如何进行改变的，从而理解路由追踪原理。**

<img src="C:\Users\CHENYUNFEI\Desktop\新建文件夹\images\33.png" style="zoom:67%;" />

<img src="C:\Users\CHENYUNFEI\Desktop\新建文件夹\images\34.png" style="zoom:67%;" />

✎ **问题**

**在 IPv4 中，TTL 虽然定义为生命期即 Time To Live，但现实中我们都以跳数/节点数进行设置。如果你收到一个包，其 TTL 的值为 50，那么可以推断这个包从源点到你之间有多少跳？**

由于TTL的值每一跳-1，所以当我收到一个包的TTL=50时，则128-50=78/64-50=14，说明经过了78/14跳。

## 传输层

### 实作一 熟悉 TCP 和 UDP 段结构

1. **用 Wireshark 任意抓包（可用 tcp 过滤），熟悉 TCP 段的结构，如：源端口、目的端口、序列号、确认号、各种标志位等字段。**

   <img src="C:\Users\CHENYUNFEI\Desktop\新建文件夹\images\35.png" style="zoom:67%;" />

2. **用 Wireshark 任意抓包（可用 udp 过滤），熟悉 UDP 段的结构，如：源端口、目的端口、长度等。**

<img src="C:\Users\CHENYUNFEI\Desktop\新建文件夹\images\36.png" style="zoom:67%;" />

✎ **问题**

**由上大家可以看到 UDP 的头部比 TCP 简单得多，但两者都有源和目的端口号。请问源和目的端口号用来干什么？**

TCP：

1）源端口（16位）：标识发送报文的计算机端口或进程。一个 TCP 报文段必须包括源端口号，使目的主机知道应该向何处发送确认报文；

2）目的端口（16位）：标识接收报文的目的主机的端口或进程。

UDP：

1）源端口（2 字节）：发送方端口号；

2）目的端口（2 字节 ）：接收方端口号。

### 实作二 分析 TCP 建立和释放连接

1. **打开浏览器访问 qige.io 网站，用 Wireshark 抓包（可用 tcp 过滤后再使用加上 `Follow TCP Stream`），不要立即停止 Wireshark 捕获，待页面显示完毕后再多等一段时间使得能够捕获释放连接的包。**

   <img src="C:\Users\CHENYUNFEI\Desktop\新建文件夹\images\37.png" style="zoom:67%;" />

2. **请在你捕获的包中找到三次握手建立连接的包，并说明为何它们是用于建立连接的，有什么特征。**

   <img src="C:\Users\CHENYUNFEI\Desktop\新建文件夹\images\38.png" style="zoom:67%;" />

   <img src="C:\Users\CHENYUNFEI\Desktop\新建文件夹\images\39.png" style="zoom:67%;" />

   <img src="C:\Users\CHENYUNFEI\Desktop\新建文件夹\images\40.png" style="zoom:67%;" />

   第一次握手，同步位（SYN）是1，确认位（ACK）是0；

   第二次握手，同步位（SYN）是1，确认位（ACK）是1；

   第三次握手，同步位（SYN）是0，确认位（ACK）是1。

3. **请在你捕获的包中找到四次挥手释放连接的包，并说明为何它们是用于释放连接的，有什么特征。**

<img src="C:\Users\CHENYUNFEI\Desktop\新建文件夹\images\41.png" style="zoom:67%;" />

确认位（ACK）仍然为1和终止控制位（FIN）变为1。

✎ **问题一**

**去掉 `Follow TCP Stream`，即不跟踪一个 TCP 流，你可能会看到访问 `qige.io` 时我们建立的连接有多个。请思考为什么会有多个连接？作用是什么？**

它们之间的连接是属于短连接，一旦数据发送完成后，就会断开连接。虽然，断开连接，但是页面还是存在，由于页面已经被缓存下来。一旦需要重新进行发送数据，就要再次进行连接。这样的连接，是为了实现多个用户进行访问，对业务频率不高的场合，节省通道的使用，不让其长期占用通道。

✎ **问题二**

**我们上面提到了释放连接需要四次挥手，有时你可能会抓到只有三次挥手。原因是什么？**

客户端向服务端发送断开连接的请求为第一次挥手，服务端向客户端回复同意断开为第二次，然后服务端向客户端发送断开的请求为第三次挥手，客户端向服务端回复同意断开连接为第四次挥手。三次挥手是将服务器向客户端发送断开连接和回复同意断开连接合成一次挥手，其他两次挥手不变。也就是说，如果对方也没有数据发给本端，那么对方也会发送FIN给本端，使得二三次挥手合并为一次。



## 应用层

**应用层的协议非常的多，我们只对 DNS 和 HTTP 进行相关的分析。**

### 实作一 了解 DNS 解析

1. **先使用 `ipconfig /flushdns` 命令清除缓存，再使用 `nslookup qige.io` 命令进行解析，同时用 Wireshark 任意抓包（可用 dns 过滤）。**

   <img src="C:\Users\CHENYUNFEI\Desktop\新建文件夹\images\42.png" style="zoom:67%;" />

2. **你应该可以看到当前计算机使用 UDP，向默认的 DNS 服务器的 53 号端口发出了查询请求，而 DNS 服务器的 53 号端口返回了结果。**

   <img src="C:\Users\CHENYUNFEI\Desktop\新建文件夹\images\43.png" style="zoom:67%;" />

   <img src="C:\Users\CHENYUNFEI\Desktop\新建文件夹\images\44.png" style="zoom:67%;" />

3. **可了解一下 DNS 查询和应答的相关字段的含义**

16位标识字段用于标记一对DNS查询和应答，以此区分一个DNS应答是哪个DNS查询的回应

16位标志字段用于协商具体的通信方式和反馈通信状态

接下来的4个字段则分别指出DNS报文的最后4个字段的资源记录数目。

对查询报文而言，它一般包含1个查询问题，而应答资源记录数，授权资源记录数和额外资源记录数则为0.应答报文的应答资源记录数则至少为1，而授权资源记录数和额外资源记录数可为0或非0。

应答字段，授权字段和额外信息字段都使用资源记录（Resource Record，RR）格式。

✎ **问题**

**你可能会发现对同一个站点，我们发出的 DNS 解析请求不止一个，思考一下是什么原因？**

本地请求查询有无对应ip，没有的话向本地DNS服务器请求解析，所以发出的 DNS 解析请求不止一个。

### 实作二 了解 HTTP 的请求和应答

1. **打开浏览器访问 qige.io 网站，用 Wireshark 抓包（可用http 过滤再加上 `Follow TCP Stream`），不要立即停止 Wireshark 捕获，待页面显示完毕后再多等一段时间以将释放连接的包捕获。**

   <img src="C:\Users\CHENYUNFEI\Desktop\新建文件夹\images\45.png" style="zoom:67%;" />

2. **请在你捕获的包中找到 HTTP 请求包，查看请求使用的什么命令，如：`GET, POST`。并仔细了解请求的头部有哪些字段及其意义。**

   <img src="C:\Users\CHENYUNFEI\Desktop\新建文件夹\images\46.png" style="zoom:67%;" />

3. **请在你捕获的包中找到 HTTP 应答包，查看应答的代码是什么，如：`200, 304, 404` 等。并仔细了解应答的头部有哪些字段及其意义。**

<img src="C:\Users\CHENYUNFEI\Desktop\新建文件夹\images\47.png" style="zoom:67%;" />

✎ **问题**

**刷新一次 qige.io 网站的页面同时进行抓包，你会发现不少的 `304` 代码的应答，这是所请求的对象没有更改的意思，让浏览器使用本地缓存的内容即可。那么服务器为什么会回答 `304` 应答而不是常见的 `200` 应答？**

完全的将内容发送给客服端就是200（成功）应答，而浏览器可以用cache对网页的数据进行缓存，不必每一项都向服务器进行请求；通过cache缓存直接到网页就会应答304（未修改）。

