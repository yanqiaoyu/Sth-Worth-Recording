# MySQL协议分析（二）

## 1.响应报文

### 1.1 通用响应报文

对于客户端发送给服务器的大多数命令，服务器将作为响应返回以下数据包之一：

* OK Packet
* Err Packet
* EOF Packet

#### 1.1.1 OK Packet

OK包是从服务器发送到客户端，用来指示某个指令执行成功的包。根据官方文档的推荐，从5.7.5开始，OK包也用于指代EOF包，并且不推荐使用EOF包。

如果  CLIENT\_PROTOCOL\_41这个标志位有效，那么OK包中还将包含警告计数

以下是官方文档给出的OK包的结构

![](../.gitbook/assets/image%20%2835%29.png)

既然官方说OK包也可以指代EOF包，那么肯定存在一套区分这两种包的规则

OK包的开头为0x00 长度 &gt; 7

EOF包的开头为0xFE 长度 &lt; 9

注意到OK包中，存在status flag这个字段，由于不是很重要，仅给出官方文档关于这个字段的解析

![](../.gitbook/assets/image%20%2810%29.png)

另外还存在一个字段，session state info，如果指令引起了系统状态的改变，就能在这个字段中体现出来

我们在Navicat中执行

```sql
set AutoCommit = On
```

抓包，可以看到Response中的status已被改变

![](../.gitbook/assets/image%20%2818%29.png)

#### 1.1.2 Err Packet

Err包，顾名思义就是用来指示错误信息的，如果  CLIENT\_PROTOCOL\_41这个标志位有效，那么包中还将包含SQL状态值

先来看看Err包的结构详情

![](../.gitbook/assets/image%20%2840%29.png)

同样的，我们执行几个错误的MySQL语句，然后抓包，看看具体报文

切换一个不存在的库

```sql
use None;
```

![](../.gitbook/assets/image%20%2841%29.png)

Error Code返回的是1049，通过查询可得，代表着数据库不存在，与实际一致

#### 1.1.3 EOF Packet

从5.7.5开始，EOF包就已经不推荐使用了，我们简略的介绍一下包的结构即可

![](../.gitbook/assets/image%20%2825%29.png)



