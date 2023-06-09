# 容器网络虚拟化

## 容器网络虚拟化

### 几个容器相关的问题

容器相关的, 反正后面会再解答

### veth设备对

模拟了物理网络中的网卡

#### veth如何使用

可以通过ip 命令加veth

#### veth底层创建过程

创建了peer和dev两个网络虚拟设备.

#### veth网络通信过程

和网络IO没什么区别,使用的驱动程序不一样.

#### 小结

区别在于一次性创建了两人个网络设备, 并把对方分别设置成了各自的peer. 在发送数据过程中, 找到发送设备的peer, 然后发起软中断让对方收取就算完事了

### 网络命名空间

命名空间用来网格隔离

#### 如何使用网络命名空间

网络设备, 路由表, arp表,iptable都是独立的

#### 命名空间相关的定义

关联命名空间

```
linux中每个进程都是用task_struct, 每个task_struct都要关联到一个命名空间对名象nsproxy, 而且nsproxy又包含了网格命名空间(netns), 对于网卡设备和socket来说, 通过自己的成员直接表明自己的归属.
```

网络命名空间定义

```
最核心的数据结构是struct netns_ipv4 ipv4, 在这个数据结构中, 定义了每个网络空音的专属路邮表, ipfilter以及各种内核参数.
```

#### 网络命名空间的创建

内核提供了三种操作命名空间的方式, clone, setns, unshare

Clone是从默认的网络空间复制过来.

默认网络空间init\_net

每当创建一个新的网格命名空间时, 就会调用fib\_net\_init来创建一套路由规则.

每一个socket都属于某个网络命名空间.

#### 网络收发如何使用网络

socket 上记录了其归属的网络命名空间, 需要查找路由表之前找到命名空间, 再找到网络命名空间里的路由表, 然后再开始执行查找.

#### 结论

因为不同的空间有不同的struct net对象, 所以看起来像是实现了多个协议栈.

### 虚拟交换机bridge

通过软件实现交换机原理

#### 如何使用bridge

呃 就是可以手动用brctl创建一个

#### bridge是如何被创建出来的

bridge在内核中是由两人个内核数据结构表示的, 分别是struct net\_device和struct net\_bridge

对应的命令

brctl addbr br0

#### 添加设备

呃, 就是加了呗

#### 数据包处理过程

数据包不会进入协议栈, 而是进入bridge

bridge上的veth在收到包的时候, 会将帧送入bridge处理函数br\_handle\_frame

#### 再次小结

用软件模拟真实物理连接, 都是设备层

### 外部网络通信

解决容器内与外部访问的方式, 路由与NAT

#### 路由与NAT

路由

```
该选择哪张网卡把数据写进去
```

iptables 与NAT

```
iptable 在内核中埋下了五个钩子入口, 俗称五链. P312

iptable 根据实现的不同功能, 分成了四张表, 分别是raw, mangle, nat和filter, 其中nat表实现的是network address translation功能, SNAT(Source nat) 和 DNAT(destination nat)
```

#### 实现分部网络的通信

做一个小实验

#### 再再次小结

说了一下容器网络的工作原理

### 本章小结

要回答问题了

容器eth0和母机上的eth0是一个东西吗

```
肯定不是, 容器的eth0是veth的一端
```

veth是什么

```
有点像lo, 是一对虚拟设备
```

linux如何实现虚拟设备

```
见网络命名空间
```

linux如何保证同宿主机上多个虚拟网络环境中路由表可以独立工作

```
其实如果没有建新的网络命名空间, 就相当于是在一个默认的命名空间里面. 加了命名空间之后, 先找到stuct net对象, 然后再找路由表,. 
```

同一宿主机上多个容器之前是如何通信的

```
用bridge模拟了交换机
```

linux上容器如何与外部机器通信

```
采用路由表控制与NAT功能, 使得虚拟网络通过母机网卡和外部机器进行通信
```

#### 完结撒花
