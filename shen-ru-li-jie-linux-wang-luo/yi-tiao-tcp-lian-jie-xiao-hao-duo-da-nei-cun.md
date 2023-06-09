# 一条TCP连接消耗多大内存

## 一条TCP连接消耗多大内存

### 相关实例问题

关于内存的问题

### Linux内核如何管理内存

使用一种叫SLAB/SLUB的内存管理机制, 一共分四个步骤:

1. 相邻的内存条和CPU被划分成node
2. 每个node被划分成多个zone, 每个zone下包含多个页面
3. 每个zone下的空闲页面都通过一个伙伴系统进行管理
4. slab分配器向伙伴系统申请连续整页内存存储内核对象

#### node 划分

NUMA 架构

```
non-uniform memory access,  内存访问时间取决于CPU与内存之间的相对位置
```

每个CPU以及和他直连的内存条组成一个node

```
可以通过numactl命令看到每个node的情况
```

#### zone 划分

ZONE\_DMA 地址段最低的一块区域, 供io设备DMA访问

ZONE\_DMA32 用于支持 32位地址总结的DMA设备, 只有64系统里才有效

ZONE\_NORMAL X86下, DMA和DMA32之外的内存全部在NORMAL的zone里管理

每个zone下有很多page, 每个一般为4kb

```
cat /proc/zoneinfo
```

#### 基于伙伴系统管理空闲页

内核 中表示ZONE的数据结构是struct zone, 下面一个数据free\_area管理了绝大部分可用的空闲页面.

伙伴系统: 内核 中用来管理物理内存的一种算法

#### slab 分配器

内核专用内存分配器, slab 或者叫slub, 只分配特定大小, 减少碎片发生的概率.slab相关的内核对象定义; kmem\_cache, kmem\_cache\_node

每个cache都有满,半满, 空三个链表, 每个链表节点都对一个salb , 一个slab由一个或进多个内存页组成, 每个slab内都保存的是同等大小的对象.

通过slabtop来查看slab开销.objsize, 每个对象的大小. objperslab, 一个slab里面的对象的数量.

#### 小结

每个ZONE都用伙伴系统管理空闲页面

### TCP连接相关内核对象

有两种方式创建socket, 一种是直接调用socket函数, 另外一种是调用accept接收.

#### socket直接创建

申请以下几个对象

1. sock\_inode\_cache
2. tcp\_sock TCP 对象
3. dentry
4. flie flip 对象

最后, P205的那个图不错

#### 服务端socket创建

通过accept函数接收创建socket, 创建的对象与上面差不多, 只有tcp\_sock在第三次握手成功的时候, 就己经创建好了.放到到全连接队列.

### 实测TCP内核对象的开销

具体就不写了, 反正是他做了一堆实验.

实验结果, 一条estabelish状态的空连接消耗的内存大约是3KB多一点点.

### 本章总结

1.  内核是如何管理内存的

    node -> zone slab管理
2.  如何查看内核使用的内存信息

    /proc/slabinfo slabtop
3.  服务器上一个establish状态的空连接需要消耗多少内存

    都说了3KB左右
4.  机器上出现了3W 多个TIME\_WAIT , 内存开销会不会很大

    还好, 一个TIME\_WAIT 差不多是0.4KB
