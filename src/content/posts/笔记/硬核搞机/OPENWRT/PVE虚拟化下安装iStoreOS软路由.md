---
title: PVE虚拟化下安装iStoreOS软路由
published: 2026-01-17
description: ""
image: ""
tags:
  - OpenWrt
category: 硬核搞机
draft: false
updated: 2026-01-17
---
# PVE虚拟化下安装iStoreOS软路由

## **1、iStoreOS软路由介绍**

iStoreOS (opens new window)是一个开源免费的路由兼存储系统，轻松管理网络与存储，享受一致的操作体验，代码完全开源 

![](assets/H1MGeXXrqJwOuzRLiTjX7SMnvCucO9M-C5hr7Dyek-k=.jpeg)





```
https://doc.istoreos.com/zh/guide/istoreos/install_pve.html

```

## **2、下载X86\_64最新固件**



```
https://fw.koolcenter.com/iStoreOS/x86_64/

```

![](assets/5m51PeWT9OXgHCGq1nEr3IlCoFg8I6dAtGRErHP7gVg=.jpeg)

## **3、创建iStoreOS虚拟机**

### **1)名称就叫iStoreOS**

![](assets/avLUYhAsS38JT16ISJzf6NvnNc8rTQPbCiwdT3qGmAY=.jpeg)

操作系统不使用任何介质 

![](assets/OMVg2Bf7qh94A6xwL8Rm-uNicD1k-I4CjaadRbz-1MU=.jpeg)

### **2)这里下载的是非EFI固件，此页面不用管，直接下一步；**

![](assets/QhQ7Wh7seRFnAzknXzvJGvfYzfG9DEiaKXIzzt6-HPU=.jpeg)

### **3)磁盘设置**

不需要创建，默认的直接删掉，下一步； 

![](assets/kht6Y2thdaXHwstVssHVL1KaX_2zB6sSBWcp9wCqk-o=.jpeg)

### **4）CPU、内存根据自身资源的大小自行配置**

![](assets/tQ966HyGhdP8PYa9lGKALI5tZrLDdK4CkSW8Rf8PRJk=.jpeg)

![](assets/OZnnWh_j5H71cteDwNiGu-7xdq7K2IMTlB0UuW-ThGc=.jpeg)

### **5）网络**

![](assets/XqhHIy6mGj8aq9t9DfERQ6HmssFzvDFFwspdNxf7WO4=.jpeg)

![](assets/kmEKoWKMKZRN_hSXI5WA6BHwz_lVWdHzMXoAteUDsgU=.jpeg)

## **4、刚创建的虚拟机里写入固件**

![](assets/3LqFaFk0JoRhoICcEEezbhuA5GfsXNu_FzWEszCH5Sw=.jpeg)

这里需要img2kvm 这个工具

```
wget https://fw0.koolcenter.com/binary/other-tools/img2kvm
chmod 777 img2kvm 

./img2kvm istoreos-24.10.3-2025101711-x86-64-squashfs-combined.img 104 local

```

![](assets/aepMYkKgalqP8Ao8zDfX3eVXQy0rxXU0-rpRQJztl2A=.jpeg)

![](assets/G7W9_imttK5BGjyyCK7Wx-GXyUGNo__DF7bQndu9Ex0=.jpeg)

![](assets/qCvaahc6IZDtCc1DjJ0pxoihjn8I2VbUqMb8rUdlyyE=.jpeg)

因为我这里的存储池的名字叫local 需要手动指定一下



不用img2kvm：

```
qm importdisk 100 istoreos-24.10.4-2025122612-x86-64-squashfs-combined.img local-lvm

```

## **5、添加磁盘**

PVE虚拟机104的硬件界面，会出现一个未添加的硬盘，双击添加：

![](assets/ERIYmnhv7u5AV5he0BhwMC17BUsT6NBi6ZFQqSiwdUY=.jpeg)

![](assets/yWvehUVWe_aF6I7fO3dPUoqwl_VSqbzBUTncs0qU-aM=.jpeg)

## **6、把刚添加的硬盘作为第一启动**

![](assets/SvUi38shlBeoVK6ABY6tQ9JAgQgvac-0qJMLANFlLkk=.jpeg)

## **7、启动虚拟机**

![](assets/OAO99wn_AvyaZQueqeo4dvdFXlU_H1SwyA5QE1c3_Ic=.jpeg)

![](assets/VSBsmPEUdmlZEbStV3PxugiUKum55EcaV9LXUWds23E=.jpeg)

## **8、登陆配置iStoreOS软路由**

![](assets/b5Xdb6j3ewdTj2QYynMAFIqtaCS8lSGrMXGDohvyLh4=.jpeg)

## **9、quickstart命令进行网络配置**

![](assets/442X5hwdrhitGaE93PvLSvb1HFu-89EJPLs8LG95XlM=.jpeg)

## **10、iStoreOS界面截图**

![](assets/jL7RmqKa30JlB2XS5fgjpkUI6-lj1mZ3DUrt3vxo6gI=.jpeg)

你懂得的工具截图

![](assets/9ugtDjIbSQJwvddLU4DBjctxKHok-7BezkuX48Chjxw=.jpeg)
