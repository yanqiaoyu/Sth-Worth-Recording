# Directly control my XiaoMi Adapter\(Wi-Fi Edition\) without Mi Home App

Lately  I bought a Smart Adapter from XiaoMi, what depressed me is that I can only control my Mi-Plug through Mi Home App. I just want to control that adapter directly through some scripts or whaterver else , so I search the Internet and finally , after SO MANY disappointments , I got the token and it's time to record it !

### 1.Install a specific version of the Mi Home App \(5.4.63\)

Maybe some other versions can also get toekn through APP directly, try it as your wish

![](.gitbook/assets/ping-mu-kuai-zhao-20191013-22.47.42.png)

### 2.Find your token in your App "Network Information"

![](.gitbook/assets/ping-mu-kuai-zhao-20191013-22.42.02.png)

This would change if you change the router your adapter connects

### 3.Install "miio" in your Linux \(maybe ubuntu or else, I install it in my raspberry pi \)

```bash
git clone https://github.com/rytilahti/python-miio

cd python-miio/

//install python3.5 first
python3.5 setup.py install
```

Check if everthing is installed

![](.gitbook/assets/ping-mu-kuai-zhao-20191013-23.04.20.png)

what we need is the "**miplug**"

### 4.Use ''miplug" to control your MI-Plug

Run the command below

```bash
miplus --help
```

![](.gitbook/assets/jie-ping-2019101617.47.20.png)

From the Usage we can know that , we need "IP" and "token" to control the plug, all the two arguement can check in the App. Now , use the command to turn on&off the adapter

![](.gitbook/assets/ping-mu-kuai-zhao-20191013-23.10.33.png)

Once return "OK", which means you have successfully dominated this adapter ! Without the Mi Home App , link it to your Home Assist or whatever else ~

> [https://paper.seebug.org/616/](https://paper.seebug.org/616/)
>
> [https://bbs.hassbian.com/archiver/?tid-6717.html&page=1](https://bbs.hassbian.com/archiver/?tid-6717.html&page=1)
>
> [https://www.jianshu.com/p/da4bf815fcdb](https://www.jianshu.com/p/da4bf815fcdb)

