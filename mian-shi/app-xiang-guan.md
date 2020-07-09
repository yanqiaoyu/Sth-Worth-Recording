# App

## 1.如何定位手机中的H5页面的元素?

如果是我自己，我会去用Airtest里的图像识别来定位元素 

但是其实还有另外一种方法，现在记录下来

1. 确保电脑能翻墙
2. 打开谷歌浏览器
3. 进入[chrome://inspect/\#devices](chrome://inspect/#devices)
4. 通过USB连接手机，就能定位H5中的元素了
5. 当然，使用Appium才能这样做，并且还需要在Appium中通过switch\_to\_context\("需要进入的WebView"\)才能进入H5页面并定位



