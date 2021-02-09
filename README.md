# unlocking router regions on netgear r7000 with stock firmware
Unlocking your router region could allow you to broadcast wifi on additional channels. If you live in an apartment building with many wifi networks, this could reduce your wifi interference and give you better wifi performance.

See: https://motorolacable.com/whitepapers/wifi-dfs

Warning: don't broadcast on channels for which you are not authorized! DFS channels are also used by radar systems at airports, weather stations, and other government radar installations. You may cause interference if your router is located close to one of these installations.

## Downgrade firmware
Netgear locked down the router's telnet server in later versions. Downgrade the router's firmware to V1.0.9.42_10.2.44. On this version, the telnet server was still accessible. Source (from the author of `nte`: https://community.netgear.com/t5/Nighthawk-WiFi-Routers/R7000-telnet-enable/m-p/1786880/highlight/true#M136303 )

Firmware downloads: https://www.netgear.com/support/product/r7000.aspx#download

## Enable the telnet server
Use `nte` http://www.antinode.info/nte/index.html
```
./nte m=<mac address> n=192.168.1.1 u=admin p=<password>
nte: Received ACK message.  Telnet access should be enabled.
```

On an incompatible firmware, we'd have gotten a message like: `nte: FAIL.  No ACK message received.`

## Telnet to router and switch region
via: https://koolshare.cn/archiver/tid-172604.html?page=5
```
telnet 192.168.1.1

# cd bin

# routerinfo
Release version : 802.11ac Dual Band Gigabit Wireless Router R7000
                  U12H270T00/V1.0.9.42/10.2.44
           Time : Sep  6 2018 15:13:53
                  U12H270T00/V1.0.9.42
...
region_num - 0x0001
sku_name - NA
language - English
wl_region - 11
timezone - -8

# burnsku 0x0002
burnsku OK

# routerinfo
Release version : 802.11ac Dual Band Gigabit Wireless Router R7000
                  U12H270T00/V1.0.9.42/10.2.44
           Time : Sep  6 2018 15:13:53
                  U12H270T00/V1.0.9.42
...
region_num - 0x0002
sku_name - WW
language - English
wl_region - 5
timezone - 0

# reboot
```

## Login to router and change region + channel
1. login to router
2. Wireless > Region Selection
3. `Europe` seems to be a good choice? https://en.wikipedia.org/wiki/List_of_WLAN_channels#5_GHz_or_5.9_GHz_(802.11a/h/j/n/ac/ax)
4. Use 5ghz channel 100

n.b.: channel 52 didn't seem to work when I tried that at first. The router UI said I was on channel 52, but wifi scanning tools said my wifi was still on channel 36 actually. Very strange.

Wifi networks can be scanned on mac OS X via the Wireless Diagnostics app. `Window > Scan`

Also check what channel is being used on linux clients via:
```
$ sudo iw wlan0 info
Interface wlan0
    ifindex 3
    wdev 0x1
    addr <redacted>
    ssid <redacted>
    type managed
    wiphy 0
    channel 100 (5500 MHz), width: 80 MHz, center1: 5530 MHz
    txpower 31.00 dBm
```

## Upgrade to latest firmware
Older firmware versions may have [security vulnerabilities](https://threatpost.com/netgear-zero-day-takeover-routers/156744/)
