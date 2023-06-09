# 一台机器最多能支持多少条TCP连接

## 一台机器最多能支持多少条TCP连接

### 相关实际问题

唉, 又是相关的问题

### 理解Linux最大文件描述符限制

限制文件打开数量的参数: fs.nr\_open, nofile, fs.file-max, 改这几个参数要小心.

#### 找源码入口

get\_unused\_fd\_flags, 申请fd, 找到一个可用数组下标

sock\_alloc\_file 申请真正的对象

#### 寻找进程级限制nofile 和fs.nr\_open

进程打开文件数如果真如过了这两人个参数, 就会报错.

区别:

```
soft nofile 可以按用户来配置, 而所有用记只能配一个fsnr_open
```

#### 寻找系统级限制fs.file-max

file-max 这个参数只限制非root用户, 表示的为整个系统可打开的最大文件数, 但不限制root用户.

#### 小结

有两种限制, 一个是进程级另是, 一个系统级别

对于soft nofile 和 hard nofile, 最终会选择二者中低的来.

如果加大了hard nofile那么也要调整fs.nr\_open, 因为如果fs.nr\_open比hard nofile 小, 那么会导致用户无法登录, 如果设置的是\*,　那么所有用户都无法登录.　同时,　不建用echo的方式修改内核参数.　因为重启　之后会失效.

### 一台服务端机器最多可以支撑多少条tcp连接

主要是受到四元组的限制, 不是有多少端口就多少连接

#### 一次关于服务端并发的聊天

一堆漫画, 看起来不错.

主要观点:

IP+端口理论上支持200万亿, 不用改 文件数也是一个限制 不同级别有不同的参数 见P226 内存的限制 内核参数, 也是可以改参数的

#### 服务端机器百万连接达成记

也是一堆漫画

就是上面的实现

#### 再次小结

没什么小结的, 就是说可以建好多, 不一定只有端口号那么多

### 一台客户端机器最多只能发起65535个连接吗

多IP增加连接数 端口复用增加连接数

也是通过四元组找到对方, 所以不只可以连6万多个, 可以通过为客户端配置多个IP, 连接不同的服务器也是可以的, 不过最好不要混用.

### 单机百万并发的实验

P244, 可以看一下 并发只中指标之一, 服务器的开销大头往往不是连接本身,而是每条连接的数据收发以及请求业务逻连处理, 不同为一务之间, 单纯比较并发也不一定有意义.

### 总结

回答几个问题

Too many open files 是怎么回事, 该如何解决

```
解发了内核限制, 限制文件数量
```

一台服务端机器最大究竟能支持多少条连接

```
基本上只就是内存的限制, 4G可以支持100W左右的空连接
```

一台客户端机器最大能发起多少连接

```
主要是IP限制, 百万条没什么问题
```

做一个长连接推送产品 , 支持1亿用户需要多少机器

```
128GB内存, 差不多20台就可以了
```