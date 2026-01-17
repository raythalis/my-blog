---
title: openwrt作为主路由
published: 2026-01-17
description: ""
image: ""
tags:
  - OpenWrt
category: 硬核搞机
draft: false
updated: 2026-01-17
---
# openwrt作为主路由

首先pve一网口A作为wan，两个网口BC作为lan

A网口可以直通给openwrt虚拟机，BC桥接成vmbr0给虚拟机



首先，网络全是乱的情况下，电脑网线插入B口或C口

pve设置网关和ip

```shellscript
nano /etc/network/interfaces
```

```shellscript
# 本地回环
auto lo
iface lo inet loopback

# 物理网口都设 manual
iface eno1 inet manual
iface enp4s0 inet manual
iface enp5s0 inet manual

# 内网桥（LAN）给 OpenWrt 和其他 VM
auto vmbr0
iface vmbr0 inet static
    address 192.168.5.9
    netmask 255.255.255.0
    gateway 192.168.5.10
    bridge-ports enp4s0 enp5s0
    bridge-stp off
    bridge-fd 0
```

电脑设置ip为同子网内ip，如192.168.5.120，此时可以通过ip访问pve页面

安装istoreOS：



[PVE虚拟化下安装iStoreOS软路由](http://192.168.5.100:3010/workspace/e24da8a2-e2c4-4e85-9821-67300b920bf5/gUSoYjGx_I)

安装后使用quickstart命令，设置lan ip为同子网下ip，如192.168.5.10，访问网页进行后续配置。



配置完成后将电脑和pve网关，dns指向openwrt，并设置静态ipv4

也可设置为自动，由openwrt里设置静态dhcp-v4



ipv4地址分配区间可以在这里设置：

![](assets/ftXoulTEa2h66AeVMPa-yQ-3ON9yuublJ6pQVD72OaY=.png)
