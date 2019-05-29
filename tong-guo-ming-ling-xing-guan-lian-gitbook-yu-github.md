下载了一个GitBook的桌面版，GitBook Editor，但是发现想要通过HTTPS关联Github的仓库时，一直无法关联。在尝试各种方法无果后，终于决定通过命令行进行手动关联，现在记录一下具体的方法

### 1.通过Gitbook Editor新建一个工程

无需多言

### 2.进入到该目录下并且手动配置Git的相关信息

![image](https://github.com/yanqiaoyu/Sth-Worth-Recording/blob/master/assets/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-05-29%2013.12.36.png)

![](/assets/屏幕快照 2019-05-29 13.20.35.png)

可以看到，在没有进行第一次保存之前，git的config文件是没有配置的。接下来就手动配置一下

手动执行如下命令

```
#当然，在使用如下命令前，还要对git做一些初始配置，不过没必要在这里讲，因此略过
#通过HTTPS的方式添加远程库，当然地址要是视自己的情况而定
git remote add origin https://github.com/yanqiaoyu/Sth-Worth-Recording.git
#也可以试一试git push -u origin master
git push -f origin master
```

此时，就完成了与远程库的关联，我们在GitBook Editor里面点击一下保存就会发现，可以同步了

![](/assets/屏幕快照 2019-05-29 19.42.47.png)

之后填写一下GitHub的登陆信息，就大功告成了

