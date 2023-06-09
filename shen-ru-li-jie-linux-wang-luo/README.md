---
description: Study notes of the book <深入理解linux网络>
---

# 🧉 深入理解linux网络

## 绪论

主要是作者在工作中碰到的一些问题.有下面几类

### 开销问题

```
time_wait 有多少开销
长连接有多少开销
CPU 消耗是怎么来的
不同语言为什么性能不一样
```

### 中断

```
NET_RX 软中断接收
NET_TX 软中断发送
为什么接收要远大于发送
可以通过 cat /proc/softirqs 查看
```

### 技术细节

```
零拷贝  DPDK
```
