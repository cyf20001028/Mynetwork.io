[TOC]



# Cisco Packet Tracer 实验

## 直接连接两台 PC 构建 LAN

将两台 PC 直接连接构成一个网络。注意：直接连接需使用交叉线。

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\48.png" style="zoom:67%;" />

进行两台 PC 的基本网络配置，只需要配置 IP 地址即可，然后相互 `ping` 通即成功。

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\49.png" style="zoom:67%;" />

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\50.png" style="zoom:67%;" />

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\51.png" style="zoom:67%;" />

## 用交换机构建 LAN

构建如下拓扑结构的局域网：

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\52.png" style="zoom:67%;" />

✎ **问题**

1. PC0 能否 `ping` 通 PC1、PC2、PC3 ？

   PC2能ping通PC1，但不能ping通PC2、PC3

2. PC3 能否 `ping` 通 PC0、PC1、PC2 ？为什么？

   PC3能ping通PC2，但不能ping通PC1、PC0

3. 将 4 台 PC 的掩码都改为 `255.255.0.0` ，它们相互能 `ping` 通吗？为什么？

   互相之间可以ping通，因为处于同一个子网255.255.255.0中，所以可以互相ping通

4. 使用二层交换机连接的网络需要配置网关吗？为什么？

​      需要，因为网关用于在2个网络间建立传输连接，使不同网络上的主机间可以建立起跨越多个网络的        级联的 、点对点的传输连接，这两个子网通过配置网关可以进行连接

✎ 试一试

集线器 Hub 是工作在物理层的多接口设备，它与交换机的区别是什么？请在 CPT 软件中用 Hub 构建网络进行实际验证。

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\53.png" style="zoom:67%;" />

从两者的工作原理来看，交换机和集线器是有很大差别的。首先，从OSI体系结构来看，集线器属于OSI的第一层物理层设备，而交换机属于OSI的第二层数据链路层设备。

从工作方式来看，集线器采用一种“广播”模式，因此很容易产生“广播风暴”，当网络规模较大时性能会受到很大的影响。而当交换机工作的时候，只有发出请求的端口和目的端口之间相互响应而不影响其他端口，因此交换机能够在一定程度上隔离冲突域和有效抑制“广播风暴”的产生。

从带宽来看，集线器不管有多少个端口，所有端口都是共享一条带宽，在同一时刻只能有两个端口传送数据，其他端口只能等待，同时集线器只能工作在半双工

## 交换机接口地址列表

仍然构建上图的拓扑结构，并配置各计算机的 IP 在同一个一个子网，使用工具栏中的放大镜点击某交换机如左边的 Switch3，选择 `MAC Table`，可以看到最初交换机的 MAC 表是空的，也即它不知道该怎样转发帧（那么它将如何处理？），用 PC0 访问（`ping`）PC1 后，再查看该交换机的 MAC 表，现在有相应的记录，请思考如何得来。随着网络通信的增加，各交换机都将生成自己完整的 MAC 表，此时交换机的交换速度就是最快的！

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\54.png" style="zoom:67%;" />

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\55.png" style="zoom:67%;" />

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\56.png" style="zoom:67%;" />

当一个交换机收到一个数据帧的时候，会查看自己的MAC表，如果MAC表中没有数据帧的源MAC和目的MAC，则会将源MAC加入MAC表，并且广播这个数据帧。

目的MAC收到这个帧后返回一个确认帧，交换机把这个帧转发给源MAC和记录目的MAC

随着网络通信的增加，各交换机都将生成自己完整的MAC表，此时交换机的交换速度就是最快的！



## 生成树协议（Spanning Tree Protocol）

只使用交换机，构建如下拓扑：

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\57.png" style="zoom:67%;" />

这是初始时的状态。我们可以看到交换机之间有回路，这会造成广播帧循环传送即形成广播风暴，严重影响网络性能。

随后，交换机将自动通过生成树协议（STP）对多余的线路进行自动阻塞（Blocking），以形成一棵以 Switch4 为根（具体哪个是根交换机有相关的策略）的具有唯一路径树即生成树！

经过一段时间，随着 STP 协议成功构建了生成树后，Switch5 的两个接口当前物理上是连接的，但逻辑上是不通的，处于Blocking状态（桔色）如下图所示：

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\58.png" style="zoom:67%;" />

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\59.png" style="zoom:67%;" />

## 路由器配置初步

我们模拟重庆交通大学和重庆大学两个学校的连接，构建如下拓扑：

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\60.png" style="zoom:67%;" />

### 交通大学路由器基本配置如下：

以太网口：

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\61.png" style="zoom:67%;" />

广域网口：

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\62.png" style="zoom:67%;" />

### 重庆大学路由器基本配置如下：

以太网口：

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\63.png" style="zoom:67%;" />

广域网口：

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\63.png" style="zoom:67%;" />

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\65.png" style="zoom:67%;" />

✎ **问题**

现在交通大学内的各 PC 及网关相互能 `ping` 通，重庆大学也类似。但不能从交大的 PC `ping` 通重大的 PC，反之亦然，也即不能跨子网。为什么？

广播消息只能在同一个虚拟子网中传播，而无法进行跨子网传播，而重交和重大服务器不在同一个子网中，出子网都需要通过各自子的网关，所以不能互相ping通。

## 静态路由

静态路由是非自适应性路由协议，是由网络管理人员手动配置的，不能够根据网络拓扑的变化而改变。 因此，静态路由简单高效，适用于结构非常简单的网络。

在当前这个简单的拓扑结构中我们可以使用静态路由，即直接告诉路由器到某网络该怎么走即可。

在前述路由器基本配置成功的情况下使用以下命令进行静态路由协议的配置：

### 交通大学路由器静态路由配置：

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\66.png" style="zoom:67%;" />

### 重庆大学路由器静态路由配置：

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\67.png" style="zoom:67%;" />

## 动态路由 RIP

### 清除静态路由配置：

1. 直接关闭路由器电源。相当于没有保存任何配置，然后各接口再按照前面基本配置所述重新配置 IP 等参数（推荐此方法，可以再熟悉一下接口的配置命令）；
2. 使用 `no` 命令清除静态路由。在全局配置模式下，交通大学路由器使用：`no ip route 192.168.3.0 255.255.255.0 192.168.2.2`，重庆大学路由器使用：`no ip route 192.168.1.0 255.255.255.0 192.168.2.1` 。相当于使用 `no` 命令把刚才配置的静态路由命令给取消。

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\63.png" style="zoom:67%;" />

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\69.png" style="zoom:67%;" />

### 交通大学路由器 RIP 路由配置：

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\70.png" style="zoom:67%;" />

### 重庆大学路由器 RIP 路由配置：

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\71.png" style="zoom:67%;" />

## 动态路由 OSPF

### 清除 RIP 路由配置：

1. 直接关闭路由器电源。相当于没有保存任何配置，然后各接口再按照前面基本配置所述重新配置 IP 等参数

2. 使用 `no` 命令清除 RIP 路由。在全局配置模式下，各路由器都使用：`no router rip` 命令进行清除

   <img src="C:\Users\CHENYUNFEI\Desktop\network\images\72.png" style="zoom:67%;" />

### 交通大学路由器 OSPF 路由配置：

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\73.png" style="zoom:67%;" />

### 重庆大学路由器 OSPF 路由配置：

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\74.png" style="zoom:67%;" />

## 基于端口的网络地址翻译 PAT

我们仍然使用重庆交通大学和重庆大学两个学校的拓扑进行 PAT 实验。我们需要保证两个学校的路由已经配置成功，无论使用静态路由还是动态路由，以下我们给出完整的配置过程：设定这两个学校的路由器使用 OSPF 协议，模拟交通大学使用内部 IP 地址（`192.168.1.0/24`），模拟重庆大学使用外部 IP 地址（`8.8.8.0/24`），两个路由器之间使用外部 IP 地址（`202.202.240.0/24`），在交通大学的出口位置即广域网口实施 PAT。

交通大学路由器接口配置如下：

以太网口

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\75.png" style="zoom:67%;" />

广域网口：

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\76.png" style="zoom:67%;" />

重庆大学路由器接口配置如下：

以太网口：

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\77.png" style="zoom:67%;" />

广域网口：

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\78.png" style="zoom:67%;" />

交通大学路由器 OSPF 路由配置：

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\79.png" style="zoom:67%;" />

重庆大学路由器 OSPF 路由配置：

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\80.png" style="zoom:67%;" />

此时，这些 PC 能全部相互 `ping` 通！如在交通大学内部使用 PC0（`192.168.1.2`）来 `ping` 重庆大学的PC2（`8.8.8.2`）应该成功。

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\81.png" style="zoom:67%;" />

重庆大学路由器丢包的配置：

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\82.png" style="zoom:67%;" />

此时，再使用交通大学内部的 PC0（`192.168.1.2`）来 `ping` 重庆大学的 PC2（`8.8.8.2`）就不成功了，会显示目的主机不可到达（`Destination host unreachable`）信息。

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\83.png" style="zoom:67%;" />

下面，我们就开始实施 PAT。即：我们将会在交通大学路由器的出口上将内部/私有 IP 地址转换为外部/公开 IP，从而包的源 IP 发生了改变，就不会被重庆大学路由器丢弃，因此网络连通。

### 交通大学路由器 PAT 配置：

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\84.png" style="zoom:67%;" />

现在，再次使用交通大学内部的 PC0（`192.168.1.2`）来 `ping` 重庆大学的PC2（`8.8.8.2`）则OK。

## 虚拟局域网 VLAN

在 CPT 中构建如下图所示拓扑：

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\85.png" style="zoom:67%;" />

### 交换机 VLAN 配置：

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\86.png" style="zoom:50%;" />

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\87.png" style="zoom:50%;" />

此时可以使用 `ping` 命令进行测试，你会发现只有在同一 VLAN 中的 PC 才能通信，且广播也局限于该 VLAN。

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\88.png" style="zoom:67%;" />

✎ **思考**

分析一下当前为何不同 VLAN 中的 PC 不能通信？网关在此起什么作用？我们的网关又在何处？如何发起广播测试？

1、VLAN具有分割网络的作用，因此不能通信

2、VLAN只是链路层协议，划分广播域，而不需要考虑IP；网关是用来进行协议转换的。不同的网段之间需要通信一定需要网关。

3、若是要发起广播测试，那么就要引入三层设备

## 虚拟局域网管理 VTP

为演示 VTP，重新构建如下拓扑结构：

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\89.png" style="zoom:67%;" />

我们将在核心交换机 3560上进行如下工作：

1. 设置为 `server` 模式，VTP 域为 `cqjtu`
2. 新建 `VLAN 2`，网络号 `192.168.1.0/24`，网关 `192.168.1.1`
3. 新建 `VLAN 3`，网络号 `192.168.2.0/24`，网关 `192.168.2.1`

### 3560 VTP Server 配置：

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\90.png" style="zoom:67%;" />

我们将在左边交换机 2960A 上进行如下工作：

1. 加入名为 `cqjtu` 的 VTP 域
2. 配置与核心交换机 3560 连接的千兆接口 `g0/1` 为 `trunk` 模式
3. 将接口 `f0/1` 划分到 `VLAN 2` 中
4. 将接口 `f0/2` 划分到 `VLAN 3` 中

### 2960A（左边） VTP Client 配置：

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\91.png" style="zoom:67%;" />

我们将在右边交换机 2960B 上进行同样的工作：

1. 加入名为 `cqjtu` VTP 域
2. 配置与核心交换机 3560 连接的千兆接口 `g0/1` 为 `trunk` 模式
3. 将接口 `f0/1` 划分到 `VLAN 2` 中
4. 将接口 `f0/2` 划分到 `VLAN 3` 中

### 2960B（右边） VTP Client 配置：

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\92.png" style="zoom:67%;" />

至此，VTP 配置完成。同 VLAN 可以 `ping` 通，而不同 VLAN 不行（即使在同一交换机下，如从 PC0 到 PC1），且能够方便的统一规划和管理。

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\93.png" style="zoom:67%;" />

✎ **试一试**

使用 PC0（`192.168.1.2`） `ping` PC1（`192.168.2.2`） 的结果如何？使用 PC0 `ping` PC2 的结果如何？想想为什么？

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\94.png" style="zoom:67%;" />

PC0与PC1虽然挂在同一个交换机下，但他们不属于同一通信子网，所以无法通信，而pc0与pc2属于同一通信子网

## VLAN 间的通信

### 3560 交换机配置：

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\95.png" style="zoom:67%;" />

至此，各 VLAN 中的 PC 可以正常通信。

✎ **试一试**

现在再使用 PC0（`192.168.1.2`） `ping` PC1（`192.168.2.2`） 的结果如何？使用 PC0 `ping` PC2 的结果如何？

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\96.png" style="zoom:67%;" />

显然都能ping 通

## DHCP、DNS及Web服务器简单配置

动态主机配置 DHCP、域名解析 DNS 以及 Web 服务在日常应用中作用巨大，我们构建如下简单的拓扑来进行练习。

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\97.png" style="zoom:67%;" />

点击 CPT 拓扑图中的 Server 图标，设置其静态 IP 地址为 `19.89.6.4/24`，然后选择 `Service` 进行如下相关配置：

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\98.png" style="zoom:67%;" />

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\99.png" style="zoom:67%;" />

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\100.png" style="zoom:67%;" />

✎ **试一试**

1. 先查看各 PC，看看是否获得网络配置

   <img src="C:\Users\CHENYUNFEI\Desktop\network\images\101.png" style="zoom:67%;" />

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\102.png" style="zoom:67%;" />

2.因为我们在 DNS 服务器中把谷歌和百度的 IP 都设为了 `19.89.6.4`，即 Server-PT，所以，如果打开 PC0 的浏览器，输入 `www.google.com` 或者 `www.baidu.com`，我们都应该看到默认的 Server-PT 这个 Web 服务器的主页（你也可进行编辑）

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\103.png" style="zoom:67%;" />

## WLAN初步配置

WLAN 即 WiFi 当前也是广泛的应用在各种场景。

我们通过构建如下拓扑的一个家庭 WLAN 来练习一下其相关的配置：

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\105.png" style="zoom:67%;" />

<img src="C:\Users\CHENYUNFEI\Desktop\network\images\104.png" style="zoom:67%;" />