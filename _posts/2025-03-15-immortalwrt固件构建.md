---
layout:     post
title:      "immortalwrt固件构建"
subtitle:   " \"快速构建属于自己的immortalwrt固件\""
date:       2025-03-15 12:00:00
author:     "OLDK"
header-img: "img/post-bg-20250315.jpg"
catalog: true
tags:
    - 网络技术
    - openwrt
    - immortalwrt
---



## 方法一

官方网站构建



地址链接

```
https://firmware-selector.immortalwrt.org/?version=24.10.0&target=x86%2F64&id=generic
```

预安装的软件包

```bash
autocore automount base-files block-mount ca-bundle default-settings-chn dnsmasq-full dropbear fdisk firewall4 fstools grub2-bios-setup i915-firmware-dmc kmod-8139cp kmod-8139too kmod-button-hotplug kmod-e1000e kmod-fs-f2fs kmod-i40e kmod-igb kmod-igbvf kmod-igc kmod-ixgbe kmod-ixgbevf kmod-nf-nathelper kmod-nf-nathelper-extra kmod-nft-offload kmod-pcnet32 kmod-r8101 kmod-r8125 kmod-r8126 kmod-r8168 kmod-tulip kmod-usb-hid kmod-usb-net kmod-usb-net-asix kmod-usb-net-asix-ax88179 kmod-usb-net-rtl8150 kmod-usb-net-rtl8152-vendor kmod-vmxnet3 libc libgcc libustream-openssl logd luci-app-package-manager luci-compat luci-lib-base luci-lib-ipkg luci-light mkf2fs mtd netifd nftables odhcp6c odhcpd-ipv6only opkg partx-utils ppp ppp-mod-pppoe procd-ujail uci uclient-fetch urandom-seed urngd kmod-amazon-ena kmod-amd-xgbe kmod-bnx2 kmod-e1000 kmod-dwmac-intel kmod-forcedeth kmod-fs-vfat kmod-tg3 kmod-drm-i915 luci-app-openclash luci-app-ttyd luci-app-xlnetacc luci-app-zerotier luci-app-wol luci-app-upnp luci-app-unblockneteasemusic luci-app-udpxy luci-app-ddns-go luci-i18n-base-zh-cn luci-i18n-firewall-zh-cn luci-i18n-opkg-zh-cn luci-app-argon-config luci-i18n-argon-config-zh-cn luci-i18n-filebrowser-zh-cn luci-i18n-ttyd-zh-cn luci-i18n-passwall-zh-cn luci-i18n-homeproxy-zh-cn openssh-sftp-server luci-i18n-xlnetacc-zh-cn luci-i18n-upnp-zh-cn luci-i18n-udpxy-zh-cn luci-i18n-ddns-go-zh-cn luci-i18n-wol-zh-cn luci-i18n-zerotier-zh-cn luci-app-nlbwmon luci-i18n-nlbwmon-zh-cn luci-app-vlmcsd luci-i18n-vlmcsd-zh-cn luci-app-ramfree luci-i18n-ramfree-zh-cn luci-app-appfilter luci-i18n-appfilter-zh-cn
```

首次启动时运行的脚本（uci-defaults）

```bash
# Beware! This script will be in /rom/etc/uci-defaults/ as part of the image.

# Uncomment lines to apply:

#

# wlan_name="ImmortalWrt"

# wlan_password="12345678"

#
root_password="woaizekai123!"
lan_ip_address="192.168.10.1"
#
pppoe_username=""
pppoe_password=""

# log potential errors

exec >/tmp/setup.log 2>&1

if [ -n "$root_password" ]; then
  (echo "$root_password"; sleep 1; echo "$root_password") | passwd > /dev/null
fi

# Configure LAN

# More options: https://openwrt.org/docs/guide-user/base-system/basic-networking

if [ -n "$lan_ip_address" ]; then
  uci set network.lan.ipaddr="$lan_ip_address"
  uci commit network
fi

# Configure WLAN

# More options: https://openwrt.org/docs/guide-user/network/wifi/basic#wi-fi_interfaces

if [ -n "$wlan_name" -a -n "$wlan_password" -a ${#wlan_password} -ge 8 ]; then
  uci set wireless.@wifi-device[0].disabled='0'
  uci set wireless.@wifi-iface[0].disabled='0'
  uci set wireless.@wifi-iface[0].encryption='psk2'
  uci set wireless.@wifi-iface[0].ssid="$wlan_name"
  uci set wireless.@wifi-iface[0].key="$wlan_password"
  uci commit wireless
fi

# Configure PPPoE

# More options: https://openwrt.org/docs/guide-user/network/wan/wan_interface_protocols#protocol_pppoe_ppp_over_ethernet

if [ -n "$pppoe_username" -a "$pppoe_password" ]; then
  uci set network.wan.proto=pppoe
  uci set network.wan.username="$pppoe_username"
  uci set network.wan.password="$pppoe_password"
  uci commit network
fi

echo "All done!"
```

构建完成后，即可下载固件。



## 方法二

GitHub Action构建



1 、fork[我的项目](https://github.com/KEVINJ1E/AutoBuildImmortalWrt)

2、点击 Action后，选择相应的平台，输入 自定义固件大小 和 路由器型号

3、点击Run Workflow

4、等待编译结束 在Release中下载即可

视频教程

<iframe src="//player.bilibili.com/player.html?isOutside=true&aid=113741840515614&bvid=BV1EG6VYCER3&cid=27613203939&page=2&high_quality=1&danmaku=0&autoplay=0" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true" sandbox="allow-top-navigation allow-same-origin allow-forms allow-scripts" width="100%" height="350"></iframe>

## 关于首次设置和备份


**首次设置需要配置好网络** 

1.LAN配置eth0 eth1 eth2

2.LAN关闭IPv6功能

3.WAN配置eth3

4.WAN关闭IPv6功能



**一键安装 分区扩容 app**

```bash
# 一键安装 sirpdboy分区扩容 app
opkg update
wget -O install.sh https://cafe.cpolar.top/wkdaily/OneKeyExpand/raw/branch/main/install.sh && chmod +x install.sh && ./install.sh

# 如果上述代码访问不到。可直接复制下面的内容。 
opkg update
wget https://cafe.cpolar.top/wkdaily/OneKeyExpand/raw/branch/main/luci-app-partexp_all.ipk
opkg install luci-app-partexp_all.ipk **2**>/dev/null
echo "安装成功"
```



⚠️注意⚠️ 每次升级前务必要导出备份。