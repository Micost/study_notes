# 网络性能优化建议

## 网络性能优化建议

### 网络请求优化

建议1: 尽量减少不必要的网络IO

```
本机IO也会消耗资源. 
```

建议2:　尽量合并网络请求

```
每个耗时至少一个RTT, 所以还是比较慢
```

建议3: 调用者与被调用主机尽可能要近一些

```
延迟小一点
```

建议4, 内网不要调用外面域名

```
外网接口慢
带宽成本高
NAT单点瓶颈
```

### 接收过程优化

建议1, 调整ringbuffer大小

```
ethtool -g eth0
```

建议2, 多队列网卡RSS调优

```
通过 /proc/interrrupts 查看

修改队列数量的方法

ethtool -L eth0 combined 32
```

建议3, 硬中断合并

```
ethtool -c eth0 查看硬中断合并配置

通过ethtool -C 改参数
```

建议4, 软中断budget调整

```
sysctl -w net.core.netdev_budget=600

一次处理中断的数量
```

建议5, 接收处理合并

```
攒一堆数据包再通知一次CPU, 数据包本身是分开的.
LRO(Large Receive Offload) GRO(Generic Receive Offload)
ethtool -k eth0 查看
```

### 发送过程优化

建议1, 控制数据包大小

```
超过MTU会被分片
```

建议2, 减少内存拷贝

```
使用sendfile() 可以减少系统开销
```

建议3, 推迟分片

```
可以省去大量包的协议头的计算工作量, 减轻CPU的负担
可以通过ethtool -k eth0 查看TSO GSO, 开启之后可以推迟分片
```

建议4, 多队列网卡XPS调优

```
XPS 可以指客户当前CPU要使用的发送队列减少冲突, 提高传输结构的局部性.
```

建议5, 使用eBPF绕开协议栈的本机网络IO

```
eBPF的sockmap和sk redirect 可以绕过tcp/ip协议栈,直接发送给接收端的socket
```

### 内核与进程协作优化

建议1, 尽量少用recvfrom等进程阻塞的方式

建议2, 使用成熟的网格库

```
用epoll
```

建议3, 使用kernel-bypass新技术

### 握手挥手过程优化

建议1, 配置充足的端口范围

建议2, 客户端最好不用使用bind

```
会直接绑定端口, 使得连接数受限于端口数
```

建议3, 小心连接队列溢出

```
半连接:只要保证tcp_syncookies内核参数是１,　就能保证不会因为有半连接队列列满而发生丢包

全连接队列: netstat -s |grep overflowed
```

建议４,减少握手重试

```
tcp_syn_retries控制　syn重传次数控制
tcp_synack_retries控制服务器连接中超时次数
```

建议５,打开TFO(TCP Fast Open)

```
第三次握手ack可以带要发送给服务器的数据, 可以节约一个RTT的时间开销.
```

建议6, 保持充足的文件描述符上限

```
/etc/sysctl.conf #系统级别
sysctl -p
vi /etc/security/limits.conf #用户级别
```

建议7, 如果请求频繁, 请弃用短连接改用长连接

```
节约了握手开销
规避了队列满的问题
端口数不容易出问题
```

建议8, TIME\_WAIT的优化

```
开启端口 reuse和recycle
限制TIME_WAIT状态的连接最大数量
    vi /etc/sysctl.conf
    systcl -p
直接用长连接代替短连接
```
