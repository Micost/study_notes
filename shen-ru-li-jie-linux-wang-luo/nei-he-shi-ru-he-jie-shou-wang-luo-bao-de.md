# 内核是如何接收网络包的

## 内核是如何接收网络包的

### 相关实际问题

这里提了很多问题, 主要是内核机制和一些概念, 显然, 我都不知道答案. 就不一一记录了.

### 数据从网卡到协议栈

Linux 内核以及网卡驱动, 主要实现链路层,网络层和传输层这三层上的功能.

#### 硬中断和软中断

```
硬中断由硬件对CPU发出信号, 而软中断是给内存一个变量以标记有中断发生

linux中断处理函数分为上半部分和下半部分, 上半部分可以快速处理的中断,然后释放CPU. 下半部分为大部分需要处理的内容, 2.5以后下半部分为软中断.软中断由ksoftirqd线程处理. 
```

#### DMA方式

当网卡收到数据后, 会以DMA方式将数据写到内存中

```
DMA: Direct memory access , 硬件直接把数据写到内存中. 相对应的是中断方式.  优点, 速度快, 不占用CPU资源. 
```

#### ksoftirqd 进程

```
用来处理软中断的数据内容, 数量与CPU核数一样, 在系统初始化时创建.处理所有的软中断, 不只有网络中断.
```

#### 网络子系统初始化

linux 通过调用subsys\_initcall 来初始化各件子系统, 网络子系统初始化过程中, 为每个CPU初始化softnet\_data,以及RX\_SOFTIRQ TX\_SOFTIRQ的注册函数(net\_rx\_action 和net\_tx\_action).

softnet\_data数据结构poll\_list 用于等待驱动程序将Poll函数注册进来.

open\_softirq为每种软中断都注册一个处理函数.NET\_TX\_SOFTIRQ的处理函数为net\_tx\_action, NET\_RX\_SOFTIRQ的处理函数为是net\_rx\_action. 这个注册方式是存的softirq\_vec变量里的, 会通过这个变更也来找中断的处理函数.

#### 协议栈注册

fs\_initcall调用inet\_init把函数注册到inet\_protos和ptype\_base中. 软中断会通过ptype\_base找到ip\_rcv的地址, 把ip包送到ip\_rcv中执行, ip\_rcv通过inet\_protos找到tcp和udp的处理函数, 再把饣转发给udp\_rcv 或者tcp\_v4\_rcv.

#### 网卡驱动初始化

每个驱动程序会都用module\_init向内核注册一个初始化函数, 当驱动矢程序被加载之后会调用这个函数. 初始化后, 内核知道了相关信息. 当网卡设备被识别看, 内核通过调用驱动的probe方法, 让设备处理ready状态.

#### 启动网卡

1.启动网卡

2.调用nt\_device\_ops中注册的open函数,

3.分配RX, TX队列内存

4.注册中断处理函数

5.打开硬中断, 等包进来

分配ringbuffer, 本质是两个数组: igb\_rx\_buffer 给内核用 vzalloc 申请的 e1000\_adv\_rx\_desc数组,网卡硬件使用. 对于多队列的网卡, 每个队列都注册了中断

### 接收数据

#### 硬中断处理

网卡收到数据之后, 会在ringbuffer 找到可用的内存位置 , 通过DMA写到内存中, 然后发起硬中断.

当ringbuffer 满的时候 , 会丢包. 可以在ifconfig中, 查看overruns 命令下因为ringbuffer满丢包的数量.

硬中断所做的事情: 记录了一个寄存器, 修改了CPU的poll list, 然后发出一个软中断.

#### ksoftirqd内核处理软中断

与硬中断一样都是调用local\_softirq\_pending()来处理中断, 不同的地是硬中断是为了写入, 而软中断是为了读取. 这个函数里面有一个都是调用smp\_processor\_id()来处理. 硬中断与软中断是在同一个CPU上响应和处理的.

处理的终点是数据包被送到协议栈. netif\_receive\_skb

#### 协议栈处理

协议栈处理使用与tcpdump命令一致的方式.　tcpdump是通过虚拟协议的方式工作,　会将抓包函数以协议的形式挂到ptype\_all上.　设备层遍历所有的协议我,　这样就能抓到数据包来供查看. tcpdump 会执行到packet\_create

packet\_create 会调用register\_prot\_hook,　这个函数会把协议挂到ptype\_all上.\_\_netif\_receive\_skb\_core函数取出protocol,会从数据包中取出协议信息,　然后遍历注册在这个协议的回调函数列表.　ptype\_bash是一个哈希表,　ip\_rcv函数地址存在这个哈希表中.

#### ip层处理

ip 层接收网络包的主入口ip\_rcv. 会调用NF\_HOOK处理netfilter函数. 过滤越多, 消耗的CPU越多. 执行之后, 会调用ip\_rcv\_finish

在ip\_rcv\_finish中会调用dst\_input, 会调用input方法处理skb. 这里会把skb进一步派送到更上层的协议(UDP/TCP)

#### 收包小结

收包之前内核的工作

```
创建ksoftirqd线程
协议栈的注册 每个协议的处理函数都需要注册
网卡驱动初始化
启动网卡, 分配RX TX队列
```

### 总结

回答之前的几个问题

#### 什么是ringbuffer 为什么会丢包

```
本质是两个数组: igb_rx_buffer 给内核用 vzalloc 申请的.  e1000_adv_rx_desc数组,网卡硬件使用.
当ringbuffer 满的时候 , 会丢包. 可以在ifconfig中, 查看overruns 命令下因为ringbuffer满丢包的数量.

ethtool 作为工具可以用来管理ringbuffer
```

#### 软中断 硬中断 分别是什么

```
硬中断 将传过来的poll_list添加到了CPU变量softnet_data的poll_list里, 接着发起软中断NET_RX_SOFTIRQ

软中断 把softnet_data的设备列表poll_list进行遍历, 执行网卡驱动提供的poll来收取网络包, 处理完后会送到协议栈的ip_rcv, udp_rcv, pc_rcv_v4等函数中. 
```

#### ksoftirqd

```
包含所有的软中断处理逻辑
```

#### 为什么网卡开启多队列能提升网络性能

加大队列可以将硬中断打散到不同的软中断中进行,　这样后面的软中断也由多个核来分担.

#### tcpdump是如何工作的

在设备层,收包时, 包被送到协议栈函数之前, 会选送到ptype\_all 抓包点, tcpdump基于这些抓包点来工作

所以tcpdump可以抓到iptable封禁的包, 但是发包的时候不会抓到.

#### iptable/netfilter在哪一层实现

ip/arp层 , NF\_HOOK函数

#### 如何看CPU网络接收时候 的开销

top命令, hi为硬中断开销, si为软中断开销 百分比的形式展示的.

**DPDK是什么**

让用户进程绕开内核协议, 自己直接从网卡接收数据.
