# MySQL协议分析（一）

## 一、协议基础

### 1.基本数据类型

整个MySQL协议使用了两种数据类型

* 整数类型
* 字符串类型

下面我们分别来详细讲讲这两个数据类型

#### 1）整数类型

对于整数类型的数据，我们又分了两种情况

* **定长整数类型** FixedLengthInteger
* **长度编码的整数类型** LengthEncodedInteger

对于定长的整数类型，没啥好说的，实际中用的不是很多，截个官网的图看看就好

![&#x5B9A;&#x957F;&#x6574;&#x6570;&#x7C7B;&#x578B;](../.gitbook/assets/image%20%287%29.png)

对于不定长的整数类型，则需要消耗1、3、4或9个字节的整数，具体取决于其数值，官网给出的说明如下如所示

![&#x957F;&#x5EA6;&#x7F16;&#x7801;&#x7684;&#x6574;&#x6570;&#x7C7B;&#x578B;](../.gitbook/assets/image%20%286%29.png)

请注意，无论是哪种数据类型，**最低有效字节都存储在数值的前面**，也就是我们熟知的小端存储。

举一个很容易理解的例子：**如果要用不定长的数据类型来表示512，应该怎么做？**

显然，根据官方文档给出的格式标准，512应当以0xFC开头

![](../.gitbook/assets/image%20%282%29.png)

接下来，就是用十六进制表示512了，512的十六进制是0x200，但是格式要求我们用2byte去填充，改一下便是 0x02 0x00。但是这样还不够，前面说了MySQL协议采用的是小端存储，而小端存储是与人类正常阅读顺序相反的（具体细节可以自行查询），因此，用MySQL协议描述十进制512最终的结果应当是

```text
0xFC 0x00 0x02
```

#### 2\)字符串类型

字符串编码的方式比较多，主要以下几种

* FixedLengthString（固定长度的字符串具有已知的硬编码长度）：MySQL的官方文档给了一个例子，ERR\_Packet包
* NullTerminatedString（Null结尾方式）:  以00字节结尾的字符串
* VariableLengthString（动态计算字符串长度方式）: 字符串的长度由另一个字段确定或在运行时计算
* LengthEncodedString（指定字符串长度方式）：长度编码的字符串是一个以描述字符串长度的长度编码的整数作为前缀的字符串
* RestOfPacketString（包末端字符串方式）：如果字符串是数据包的最后一个组成部分，则可以从总数据包长度减去当前位置来计算其长度

结合实际情况来理解这些编码方式会好很多

### 2. MySQL数据包结构

MySQL在进行封包时有自己的一套规则，具体来说

如果MySQL客户端或服务器要发送数据，则：

* 将数据拆分为大小为 2^24 字节的数据包
* 在每个块之前添加一个包头

2^24Byte = 2^14 KB = 2^4 MB = 16MB，换句话说，1个MySQL的包不能超过或者等于16MB ，如果超过了，那就要进行分包处理了。参考一下官方文档给出的分包机制

![&#x5B98;&#x65B9;&#x5206;&#x5305;&#x673A;&#x5236;](../.gitbook/assets/image%20%285%29.png)

举个例子，比如发送16 777 215 \(2^24-1\) 字节的内容，则第一个数据包满载，第二个数据包是一个空数据包（一种临界情况）

下面给出一个典型的MySQL报文的结构

![&#x4E00;&#x4E2A;&#x5178;&#x578B;&#x7684;MySQL&#x62A5;&#x6587;](../.gitbook/assets/image%20%283%29.png)

可以看到，一个MySQL报文，由4个B的消息头以及N个B的消息体构成的

另一个值得注意的是序列号，序列号用以保证每一次请求交互之间报文的正确性，**每次客户端发起请求时，序号值都会从0开始计算**

### **3.MySQL数据包类型**

#### 3.1 登录报文

握手认证阶段为客户端与服务器建立连接后进行，交互过程如下：

* 服务器 -&gt; 客户端：握手初始化消息
* 客户端 -&gt; 服务器：登陆认证消息
* 服务器 -&gt; 客户端：认证结果消息

直接看报文结构太过生硬，下面我们来通过实际的抓包结果一探究竟

![](../.gitbook/assets/image%20%2838%29.png)

使用Navicat登录上我的个人服务器的MySQL Server，并使用Wireshark进行抓包，可以看到，一次典型的登录的报文交互如上图所示

下面逐个分析各个报文的具体内容

**握手初始化报文**消息结构如下：（1.Server-&gt;Client）

![](../.gitbook/assets/image%20%2815%29.png)

我们点开在wireshark中的报文

![](../.gitbook/assets/image%20%2824%29.png)

根据我们前面所了解的报文结构，前3位标识了当前数据长度，即4a 00 00，第4位为序号，即00。显然当前长度为 0x00004a，即74，序号为0，与wireshark解析出的结果一致。

再选中Server Greeting，看看具体的消息结构

![](../.gitbook/assets/image%20%2825%29.png)

1. 1个字节的协议版本号：0a，解析出的结果为10，从3.21.0开始，协议版本都是v10，而不是v9了
2. N个字节的版本信息：这里是5.7.29，一个字符对应一个ASCII码，并且由于是Null结尾的字符串类型，所以是 35 2e 37 2e 32 39 00
3. 4个字节的线程信息 ：39 28 00 00 整数类型，换算过来是 0x00002839，对应十进制10297，解析无异常
4. 8个字节的挑战随机数第一部分
5. 1个字节的Null填充
6. 2个字节的权能标志位：ff ff
7. 1个字节的字符集
8. 2个字节的服务器状态码
9. 2个字节的拓展权能标志位
10. 1个字节的认证插件的长度
11. 空白N位
12. N个字节的挑战随机数第二部分
13. 认证插件

**握手响应**报文消息结构如下

客户端给出的报文响应有两种，一种叫response41，另一种叫response320，其中，response41是4.1版本之后使用的，此次抓包的Response也是41，这里仅给出responset41的解析

![](../.gitbook/assets/image%20%2833%29.png)

![](../.gitbook/assets/image%20%2817%29.png)

报文长度略去不表，值得注意的是，序列号变成了1，这与我们的认知是一致的，即在同一个交互序列内，序列号会+1

1. 4个字节的客户端权能标志+拓展客户端权能标志
2. 4个字节的最大包长
3. 1个字节的客户端字符集
4. 23字节的00填充
5. 账号
6. 密码

**服务端OK报文**

![](../.gitbook/assets/image%20%2814%29.png)

认证成功后，服务端就会返回一个OK给客户端，此时登陆成功。OK报文的具体内容非常简单，不做细致讲解

#### 3.2 命令报文

登录完成之后，就是发送命令的阶段了，这一部分有非常多的报文，我们挑选其中非常常见及重要的报文进行详解

* 0x00 COM\_SLEEP \(服务器内部指令，客户端无法执行\)
* 0x01 COM\_QUIT  返回值：退出，连接关闭

![](../.gitbook/assets/image%20%2843%29.png)

* 0x02 COM\_INIT\_DB 执行 use xxx; 语句，即可抓到该包 返回值OK or Err

![](../.gitbook/assets/image%20%2828%29.png)

* 0x03 COM\_QUERY 最为常见的MySQL指令，例如 select \* from 、desc xxxtable都是走这个指令出去的

![](../.gitbook/assets/image%20%2826%29.png)

![](../.gitbook/assets/image%20%2830%29.png)

* 0x04 COM\_FIELD\_LIST 查询表结构，执行show columns from xxx; 注意，在5.7版本之后移动到了COM\_QUERY中执行

![](../.gitbook/assets/image%20%2835%29.png)

*  从MySQL 5.7.11之后，0x05 COM\_CREATE\_DB && 0x06 COM\_DROP\_DB 统统移入COM\_QUERY中执行

![](../.gitbook/assets/image%20%2823%29.png)

![](../.gitbook/assets/image%20%2813%29.png)

* 0x07 从MySQL 5.7.11之后，COM\_REFRESH 刷新指令移动到COM\_QUERY中执行

对于flush指令来说，存在8个参数

| sub\_command | Constant Name | Description |
| :--- | :--- | :--- |
| 0x01 | `REFRESH_GRANT` | Refresh grant tables `FLUSH PRIVILEGES` |
| 0x02 | `REFRESH_LOG` | Start on new log file `FLUSH LOGS` |
| 0x04 | `REFRESH_TABLES` | Close all tables `FLUSH TABLES` |
| 0x08 | `REFRESH_HOSTS` | Flush host cache `FLUSH HOSTS` |
| 0x10 | `REFRESH_STATUS` | Flush status variables `FLUSH STATUS` |
| 0x20 | `REFRESH_THREADS` | Flush thread cache |
| 0x40 | `REFRESH_SLAVE` | Reset master info and restart slave thread `RESET SLAVE` |
| 0x80 | `REFRESH_MASTER` | Remove all binary logs in the index and truncate the index `RESET MASTER` |

如果想要执行不同的flush指令，只需要执行  flush + 参数即可，例如 flush privilege

![](../.gitbook/assets/image%20%2816%29.png)

* 0x08 COM\_SHUTDOWN 5.7.9以后，shutdown被移动到 COM\_QUERY中执行，在8.0中移除该指令

下面是他的一些参数

| Type | Constant Name | Description |
| :--- | :--- | :--- |
| 0x00 | SHUTDOWN\_DEFAULT | defaults to SHUTDOWN\_WAIT\_ALL\_BUFFERS |
| 0x01 | SHUTDOWN\_WAIT\_CONNECTIONS | wait for existing connections to finish |
| 0x02 | SHUTDOWN\_WAIT\_TRANSACTIONS | wait for existing trans to finish |
| 0x08 | SHUTDOWN\_WAIT\_UPDATES | wait for existing updates to finish \(=&gt; no partial MyISAM update\) |
| 0x10 | SHUTDOWN\_WAIT\_ALL\_BUFFERS | flush InnoDB buffers and other storage engines' buffers |
| 0x11 | SHUTDOWN\_WAIT\_CRITICAL\_BUFFERS | don't flush InnoDB buffers, flush other storage engines' buffers |
| 0xfe | KILL\_QUERY |  |
| 0xff | KILL\_CONNECTION |  |

虽然官方文档定义了很多种shutdown的参数，但是实际上只有SHUTDOWN\_WAIT\_ALL\_BUFFERS可用

![](../.gitbook/assets/image%20%2812%29.png)

* 0x09 COM\_STATISTICS 获取服务器内部信息，同样移入了COM\_QUERY

![](../.gitbook/assets/image%20%2820%29.png)

* 0x0a COM\_PROCESS\_INFO获取连接信息，从5.7.11开始，移入了COM\_QUERY

![](../.gitbook/assets/image%20%2819%29.png)

* 0x0b COM\_CONNECT 服务器内部指令
* 0x0c COM\_PROCESS\_KILL 杀死某个连接 从5.7.11开始，移入了COM\_QUERY

![](../.gitbook/assets/image%20%2841%29.png)

* 0x0d COM\_DEBUG 要求服务器将调试信息保存下来，保存的信息多少依赖于编译选项设置（debug=no\|yes\|full），依赖代码实现
* 0x0e COM\_PING 检查与服务器之间的连通性，依赖代码实现
* 0x0f COM\_TIME 服务器内部指令
* 0x10 COM\_DELAYED\_INSERT 服务器内部指令
* 0x11 COM\_CHANGE\_USER 切换当前用户，依赖代码实现
* 0x12 COM\_BINLOG\_DUMP  向主机 请求给定位置的二进制日志网络流

![](../.gitbook/assets/image%20%2829%29.png)

执行 show binary logs即可

* 0x13 COM\_TABLE\_DUMP 丢弃一张表
* 0x14 COM\_CONNECT\_OUT 内部指令
* 0x15 COM\_REGISTER\_SLAV 注册一个从数据库
* 0x16 COM\_STMT\_PREPARE 根据传递的查询字符串创建准备好的语句
* 0x17 COM\_STMT\_EXECUTE  要求服务器执行由标识的准备好的语句
* 0x18 COM\_STMT\_SEND\_LONG\_DATA 发送列的数据
* 0x19 COM\_STMT\_CLOSE 取消分配准备好的语句
* 0x1a COM\_STMT\_RESET  重置通过COM\_STMT\_SEND\_LONG\_DATA命令累积的准备好的语句的数据
* 0x1b COM\_SET\_OPTION 启用和禁用当前连接的功能
* 0x1c COM\_STMT\_FETCH  在COM\_STMT\_EXECUTE之后从现有结果集中获取行
* 0x1d COM\_DAEMON 服务器内部指令
* 0x1e COM\_BINLOG\_DUMP\_GTID  根据GTID 请求Binlog网络流
* 0x1f COM\_RESET\_CONNECTION 重置会话状态



> 参考资料
>
> [https://dev.mysql.com/doc/dev/mysql-server/latest/page\_protocol\_com\_quit.html](https://dev.mysql.com/doc/dev/mysql-server/latest/page_protocol_com_quit.html)
>
> [https://scala.cool/2017/11/mysql-protocol/](https://scala.cool/2017/11/mysql-protocol/)
>
> [http://hutaow.com/blog/2013/11/06/mysql-protocol-analysis/\#42-](http://hutaow.com/blog/2013/11/06/mysql-protocol-analysis/#42-)
>
> [https://dev.mysql.com/doc/internals/en/com-delayed-insert.html](https://dev.mysql.com/doc/internals/en/com-delayed-insert.html)

