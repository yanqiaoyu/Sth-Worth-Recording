# MySQL协议分析

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

请注意，无论是哪种数据类型，**最低有效字节都存储在数值的前面**，也就是我们熟知的小端存储，举一个很容易理解的例子：如果要用不定长的数据类型来表示512，应该怎么做？

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

### 2. MySQL数据包

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

### **3.通用响应报文**

从客户顿发往服务端的消息，服务端大多会回复3种类型的报文

* OK包
* Error包
* EOF包（从MySQL 5.7.5开始启用，用OK包代替EOF包）

> 参考资料
>
> [https://dev.mysql.com/doc/dev/mysql-server/latest/page\_protocol\_com\_quit.html](https://dev.mysql.com/doc/dev/mysql-server/latest/page_protocol_com_quit.html)
>
> [https://scala.cool/2017/11/mysql-protocol/](https://scala.cool/2017/11/mysql-protocol/)
>
> [http://hutaow.com/blog/2013/11/06/mysql-protocol-analysis/\#42-](http://hutaow.com/blog/2013/11/06/mysql-protocol-analysis/#42-)
>
> [https://dev.mysql.com/doc/internals/en/com-delayed-insert.html](https://dev.mysql.com/doc/internals/en/com-delayed-insert.html)

