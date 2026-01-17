---
title: 关于SLAAC+EUI-64
published: 2026-01-17
description: ""
image: ""
tags:
  - OpenWrt
category: 硬核搞机
draft: false
updated: 2026-01-17
---
# 关于SLAAC+EUI-64

SLAAC模式是由客户端去请求ipv6前缀，再有自己的mac地址通过EUI-64计算出后缀拼接成ipv6

这种方法的好处是可以通过EUI-64便于防火墙管理



但是现在大多数系统都不会自动生效EUI-64，如HA，PVE，飞牛等，所以即使设置了EUI-64还要手动输入命令行来改一些配置，不推荐。

不如用反代+DDNS，便于防火墙管理和端口管理。
