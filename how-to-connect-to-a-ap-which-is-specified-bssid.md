# How to connect to a AP which is specified BSSID?

In my daily work, I often need to connect to a AP whose 2.4G & 5G band shares the same SSID. This leads to a question : how can I link my Network Card to a specified BSSID?

## 1.Disable & Enable your Network Card 2.4G & 5G band \(Win\)

This is the easiest way to link your Network Card to a specified band in WIN. No need to talk much



## 2.A Helpful Tool : WirelessMon \(Win\)

This is a very common-used tool in my daily work. With its GUI , you can also link your NC to a secified BSSID in WIN. If your Network Card doesn't support disable 2.4G&5G, its  a great alternative plan. It's worth noticing that this softvware would periodically scan AP aroud, so it may affect your throughput result  \(REMEMBER to close it during throughput test!\)

## 3. wpa\_supplicant & wpa\_cli \(Linux\)

wpa\_supplicant is a cross-platform supplicant with support for WEP, WPA and WPA2 \([IEEE 802.11i](https://en.wikipedia.org/wiki/IEEE_802.11i-2004)\). Your call it in your terminal any time

Usually , we use wpa\_supplicant & wpa\_cli at the same time to ensure our NC can link to a specified BSSID we want.

### 3.1 How to use it ?

First of all , we need to let the  wpa\_supplicant run normally.\(We can take it as a agent who apply for authentication for the AP\)

```text
wpa_supplicant -B -c/etc/wpa_supplicant/wpa_supplicant.conf -iwlan0 -Dnl80211,wext
```

* -B: run in the backgroud
* -c: Path to configuration file
* -i: interface to listen on
* -D: Driver to use \(you can specify multiple drivers at the time\)

After running it, use the wpa\_cli to continue

* Scan AP around your Device

```text
wpa_cli -i wlan0 scan  //wlan0 is the name of your Network card,change it if needed
```

* Get the scan result

```text
wpa_cli -i wlan0 scan_result  
```

After the two steps , AP's information may dispaly in your terminal , choose the one you want to link ,  mark it down and continue

* Add the information to the configuration file

```bash
wpa_cli -i wlan0 add_network  //it would return a number which would be used below
//let's suppose it would return 2 as a handle
```

```bash
wpa_cli -i wlan0 set_network 2 ssid '"SSID"' //replace "SSID" with what u need
wpa_cli -i wlan0 set_network 2 key_mgmt WPA-PSK //
wpa_cli -i wlan0 set_network 2 psk '"KEY"' //replace "KEY" with what u need
wpa_cli -i wlan0 set_network 2 bssid XX:XX:XX:XX:XX:XX //specified the bssid u want
wpa_cli -i wlan0 enable_network 2 //
wpa_cli -i wlan0 save_config      //
```

Now , check your network status to ensure you have successfully connected to the AP you want !

### 3.2 How to change my configure ?

Well , what if I have a dual frequency router and I want to roam to another frequency?

* First and most importantly , disconnect from your current AP

```bash
wpa_cli -i wlan_0 disconect //
```

* Now , configure the wpa\_cli like above

```bash
wpa_cli -i wlan0 set_network 2 bssid XX:XX:XX:XX:XX:XX //
wpa_cli -i wlan0 save_config //
wpa_cli -i wlan0 reconnect //ensure there is no other effective AP in your list_network
```

> Reference: 
>
> [https:// https://segmentfault.com/a/1190000011579147](https://segmentfault.com/a/1190000011579147)
>
> [https://www.raspberrypi.org/documentation/configuration/wireless/wireless-cli.md](https://www.raspberrypi.org/documentation/configuration/wireless/wireless-cli.md)



