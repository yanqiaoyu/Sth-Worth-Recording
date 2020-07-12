# 一些工具的原理

## 1. Fiddler原理

Fiddler的工作原理与中间人攻击非常类似，Fiddler是客户端与服务端中间的一层代理

客户端发送给服务端的HTTP或者HTTPS请求，其实是发给了Fiddler，而Fiddler，接受了这个请求并伪装成了客户端的请求发送给了服务器。

## 2.说说Selenium的原理

首先，我认为selenium在进行自动化测试时，分为3个部分

脚本，webdriver，浏览器

我们在脚本里实例化了WebDriver之后，WebDriver会在目标浏览器内启动一套WebService，用以对浏览器进行我们想要的各种操作

当我们打开浏览器之后，会返回一个sessionID，这个sessionID是每次打开浏览器都不同的，主要目的是保证多线程并行执行时不会互相干扰。

最后我们通过JSON Wire Protocol进行指令的发送，以及获取命令执行的结果，保证了对于不同的语言的兼容性

## 3.说说UIAutomator的原理？



