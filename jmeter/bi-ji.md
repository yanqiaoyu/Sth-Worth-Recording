# 笔记

主要构成：

资源生成器：生成测试过程中服务器的资源代码

用户运行器：根据脚本要求模拟用户指定行为

报表生成器：生成可视化的报表

负载发生器：用以产生负载，通常以多进程或者多线程的方式模拟用户行为



Jmeter的脚本录制

这个功能很有意思，开启了脚本录制之后，我们可以监控当前浏览器的行为，然后自动录制成脚本，

但是我们一般不在jmeter里面录制脚本，而是结合badboy使用。通过badboy进行脚本录制，然后导出为jmeter可执行的脚本进行测试



性能测试实战需求：

测试20个用户访问XXXX网站，在负载达到30QPS时的平均响应

QPS （Query Per Second）

Jmeter设置断言

针对web界面一些指定需要出现的字符串，可以利用断言完成

Jmeter有两种导入参数的办法，一种Jmeter自带的参数设置，另一种可以导入CSV格式的数据，比方说一些带有CSV数据格式的txt文件，还可以从通过随机函数生成

Jmeter还可以拿来测试FTP服务器的性能，因此可以拿来测转发性能



