# How to Use Macbook to capture 802.xx packet?

In daily work,  we often need to capture the wireless packets either from the air or a specific interface. Its an easy thing to capture packets from a specific interface both in Win and Mac, but it comes a little complicate when we wanna capture some packets from the air. We need to install some special drivers in Win, so I decide to use my MacBook to capture some 802.xx packets.

## 1.Configure Airpot 

Airport is a built-in tool for wireless config in Macbook, with it we can easily cature 802.xx packets.

First of all,establish a soft connection for airport\(so that we can call it anywhere in the terminal\)

```text
ln -s /System/Library/PrivateFrameworks/Apple80211.framework/Versions/Current/Resources/airport /usr/sbin/airport
```

 If we dont have enough permission here, follow steps below

* Reboot
* Hold Command+R during rebooting
* Open a terminal
* Key in "csrutil disable"\("csrutil enable" to open the protection, you can open it after cofigure\)
* Reboot again

Now , we can call "airport" anywhere in the terminal instead of call it by its obsolute path

## 2. Start capturing

Before we start capturing , please key in some commands below

```text
//disconnect all connection
airport -z 
//6 is the channel you wanna capture, of course you can change it on your need
airport -c6 
```

Now , you can use your wireshark or some other tools to capture the wireless packets

