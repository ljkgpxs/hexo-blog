---
title: 树莓派3做路由并负载SSR
url: 389.html
id: 389
categories:
  - Linux
date: 2018-01-24 17:24:57
tags:
---

斐讯路由器做活动的时候, 领了一个K2, 然而这路由器性能很差, 跑shadowsocks顶多2MB/s, 后来shadowsocks容易被查水表, 换了shadowsocksR, 速度更低了, 性能实在捉急. 手头正好有一块树莓派3B, 平时就用来做小型的NAS, 主要用来电脑和手机互相传文件方便, 性能上有点浪费, 就拿来做路由吧..

  

博主选择LEDE做为路由固件, 另外需要一个USB网卡, 首先去LEDE官网下载树莓派3的固件和SDK(后续编译ssr), 这里推荐用最新稳定版的, 我试过开发版, 在把opkg源换成国内的时候有点问题, 固件和SDK下载地址:[https://downloads.lede-project.org/releases/17.01.4/targets/brcm2708/bcm2710/](https://downloads.lede-project.org/releases/17.01.4/targets/brcm2708/bcm2710/)

  

我用了Etcher烧写到了TF卡上, 由于系统分区没有把TF卡空间完全利用, 可以用Gparted, 把TF卡的系统分区拓展. 插到树莓派上面启动, 这时先不要插USB网卡

启动之后, 树莓派自带的网口会被分配成LAN, 拿一根线练到电脑上, 后可以直接浏览器访问192.168.1.1, 或者ssh, 用户名root, 没有密码, 连上之后修改自带网口为LAN, 自动获取IP, 并开启WIFI功能 (当然, 你也可以在Linux系统上挂在TF卡的分区, 直接修改这些文件)

  

修改/etc/config/network , 把自带网口分配给WAN :

  
```
config interface 'loopback'
        option ifname 'lo'
        option proto 'static'
        option ipaddr '127.0.0.1'
        option netmask '255.0.0.0'

config globals 'globals'
        option ula_prefix 'fd11:8629:b448::/48'

config interface 'lan'
         option type 'bridge'
#       option ifname 'eth0'
        option proto 'static'
        option ipaddr '192.168.253.1'
        option netmask '255.255.255.0'
        option ip6assign '60'

config interface wan
        option proto dhcp
        option ifname eth0
```
  

  

修改/etc/config/wireless 把LAN 挂在wlan0设备上 :

  
```
config wifi-device radio0
        option type     mac80211
        option channel  11
        option hwmode   11g
        option path     'platform/soc/3f300000.mmc/mmc_host/mmc1/mmc1:0001/mmc1:0001:1'
        option htmode   HT20
        option disabled 0

config wifi-iface
        option device   radio0
        option network  lan
        option mode     ap
        option ssid     LEDE
        option encryption psk2
        option key      wifi_password
```
  

  

修改后, 用网线把树莓派连到家里路由器上的lan口, 重启树莓派, 通过WIFI连接树莓派, 这个时候切记不可关闭WIFI, 因为LAN是挂在wlan0上面的, 关闭WIFI后wlan0也会被ifconfig down, 你没法连上树莓派, 只能通过电脑修改配置文件重新开启WIFI功能

重启后树莓派就连上网了, 其实这个时候不用USB网卡也行, 树莓派只做无线路由, 但是树莓派的无线模块不是很好, 延迟有时候会乱跳, 所以我选择用K2做无线, 树莓派只做普通路由

接下来安装USB网卡驱动, 请根据你的网卡型号安装, 我的网卡型号是ax88179. 先修改opkg的源, 访问速度会快很多, 修改文件/etc/opkg/distfeeds.conf, 这里用的是清华大学的源 :

  
```
src/gz reboot_core http://mirrors.tuna.tsinghua.edu.cn/lede/releases/17.01.4/targets/brcm2708/bcm2710/packages
src/gz reboot\_base http://mirrors.tuna.tsinghua.edu.cn/lede/releases/17.01.4/packages/arm\_cortex-a53_neon-vfpv4/base
src/gz reboot\_luci http://mirrors.tuna.tsinghua.edu.cn/lede/releases/17.01.4/packages/arm\_cortex-a53_neon-vfpv4/luci
src/gz reboot\_packages http://mirrors.tuna.tsinghua.edu.cn/lede/releases/17.01.4/packages/arm\_cortex-a53_neon-vfpv4/packages
src/gz reboot\_routing http://mirrors.tuna.tsinghua.edu.cn/lede/releases/17.01.4/packages/arm\_cortex-a53_neon-vfpv4/routing
src/gz reboot\_telephony http://mirrors.tuna.tsinghua.edu.cn/lede/releases/17.01.4/packages/arm\_cortex-a53_neon-vfpv4/telephony
```
  

然后运行命令安装驱动:
```
opkg update
opkg install kmod-usb-net kmod-usb-net-asix-ax88179
```
  

安装可能会出错, 无视就行, 然后插上USB网卡, 修改/etc/config/network:
```
config interface 'loopback'
	option ifname 'lo'
	option proto 'static'
	option ipaddr '127.0.0.1'
	option netmask '255.0.0.0'

config globals 'globals'
	option ula_prefix 'fd2b:964c:4108::/48'

config interface 'lan'
	option type 'bridge'
	option _orig_ifname 'radio0.network1'
	option _orig_bridge 'true'
	option proto 'static'
	option ipaddr '192.168.253.1'
	option netmask '255.255.255.0'
	option ifname 'eth1'

config interface 'wan'
	option ifname 'eth0'
	option _orig_ifname 'eth0'
	option _orig_bridge 'true'
	option proto 'pppoe'
	option username '宽带账号'
	option ipv6 'auto'
	option password '宽带密码'
```
修改后不要着急重启, 进老路由器的管理页面, 关闭DHCP服务, 把路由器当做交换机使用, 并把老路由器的LAN口IP设置为树莓派同一网络, 例如我设置的是192.168.253.2, 方便以后管理  

  

之后树莓派断电, 树莓派自带网口接外网, USB网卡接路由器的LAN口, 路由器的WAN口不要接网线, 全部接好后插电启动就OK了, 图示如下:

![](/images/raspberry3_router.png)

  

  

ssr编译比较简单, 项目地址在这里: [https://github.com/ywb94/openwrt-ssr](https://github.com/ywb94/openwrt-ssr)

只是注意, 运行 ./scripts/feed update的时候加上-f参数, 强制更新所有数据, 不然后面会提示找不到依赖包:
```
./scripts/feeds update -f
```
我这里有个问题就是, GFW模式不正常, IP路由模式可以用, 但是似乎这个项目没有再更新了, 先用着吧, 等找到更好用的再说  

  

经过测试, 树莓派的性能足够把百兆宽带发挥出来, ssr也能跑满速
