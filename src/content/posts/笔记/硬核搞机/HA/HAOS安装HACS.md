---
title: HAOS安装HACS
published: 2026-01-17
description: ""
image: ""
tags:
  - HomeAssistant
category: 硬核搞机
draft: false
updated: 2026-01-17
---
# HAOS安装HACS

1 打开高级设置模式

左下角，个人账户--打开高级模式

![](assets/40-TODiGeONJ763krLpkduZrJMAZjSrChDFatGNqt6Y=.jpeg)




2 打开配置——加载项

![](assets/oA4cGoFUBG36cwBTZmlBxynjQdJkjIa_jagtCqj0G3c=.jpeg)

进入右下角的加载项商店

![](assets/C8rxrpkXgJWGKF4QElwvg0w8h-I2HpiRlkGfBy-5bLk=.jpeg)

3 在商店里找到 Terminal & SSH，并安装

![](assets/mQfjfzpYp4pTEHFU6E9qd90jwnc8ssiKS6hM6TAufF0=.jpeg)

![](assets/xJbSa2dfrlWhqQFuJPPbNMt8xrZpcHUepokEm4IJeJY=.jpeg)

下图这样就是安装成功了

![](assets/DguHybYUvAr4ZIndh7NECZvFQjnDJJJXUlNUq7pdPho=.jpeg)

点击左边栏的terminal，在右边终端里粘贴或者输入以下代码回车：

wget -O -[https://get.hacs.xyz](https://link.zhihu.com/?target=https%3A//get.hacs.xyz/)| bash -

![](assets/hwgDfwgzxO7hlHl8Gmt4axMrtCkitMF5a3HRHsx7fWc=.jpeg)

如果出现Connection refused等无法连接的提示，可以多试几次，几次之后还是不行那么就需要解决了

（从上图可以得知是无法访问githubusercontent com造成的，可以通过全局加速、修改[hosts](https://zhida.zhihu.com/search?content_id=253742144\&content_type=Article\&match_order=1\&q=hosts\&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3Njc3MDc5MjUsInEiOiJob3N0cyIsInpoaWRhX3NvdXJjZSI6ImVudGl0eSIsImNvbnRlbnRfaWQiOjI1Mzc0MjE0NCwiY29udGVudF90eXBlIjoiQXJ0aWNsZSIsIm1hdGNoX29yZGVyIjoxLCJ6ZF90b2tlbiI6bnVsbH0.M9oMEL8_QODCMN8gUxq2_QuARCxAbSW9gwBJ_y-9tG4\&zhida_source=entity)等办法解决，这里讲一下修改hosts的方法。）

在home Assistant终端输入以下代码编辑hosts：

vi /etc/hosts

![](assets/ShA3QQbSyMHuJgpqgmc14zz-s8Wjev9e_1yIrbk2vdE=.jpeg)

进入到hosts编辑界面，先按一下i键进入编辑模式，浏览器再开个窗口，使用域名查询网站查到[http://raw.githubusercontent.com](https://link.zhihu.com/?target=http%3A//raw.githubusercontent.com)的ip地址后，复制并且插入到终端窗口，格式如图，是IP地址 空格 域名,把[http://github.com](https://link.zhihu.com/?target=http%3A//github.com)也插入进去，他们的IP随时在变，所以需要查询后插入。操作完成后按一下键盘esc键退出编辑模式，再输入:wq保存退出：

![](assets/A5S8JSJ21UBvlybo7tlPwBGhoo79_1wDHpuTeQypEHk=.jpeg)

之后再次输入之前的命令安装，安装终于可以进行了，等一会儿看到Installation complete字样表示安装成功。

![](assets/sB3R8UXrI4I7n5Uh0hXcIsKwfT4t5WVIkieDd256ftU=.jpeg)

4 安装完成后，在终端输入reboot命令重启HA服务器

刷新浏览器，点击左下角的配置、设备与服务




5 安装HACS商店

进入设置——设备与服务

（原来叫配置的，好像突然改成设置了）

![](assets/kAGBo_vs6JOt_ijsGAR1PmKQlmHGNGpgVZmfgMc6FOM=.jpeg)

添加集成——搜索HACS——安装

![](assets/6u7ilRKc14v15DV5v_uHGWswhnYADUy8zE3dhsSr2eY=.jpeg)

给所有选项打上勾，点击提交

![](assets/Yk6lyXW6OMCpAO6cw2O7OutGIGKzT1KpobvAOf95oYA=.jpeg)

6 GITHUB设置

点击图中的超链接，并且复制下方8位数的激活码等会儿要用

![](assets/tBmeZXsZVDV7hj_ysFUrmIZWKiBjr03PdGEi2uqb6YI=.jpeg)

点击链接后，注册一个GITHUB网站账号，注册过程不表

然后登录后出现这个页面，将上一步复制的激活码填写进来

![](assets/WcqpavjJ37AAJ5I5iApyA1dzC8l9IkcyYB_Z3sDUbbY=.jpeg)

![](assets/yKpjach3PBDcg7NpL3N7MnJsG_Wjz9Ydls7ZAo1a5DA=.jpeg)

回到Home Assistant页面，弹出提示成功！安装HACS完成。

然后重启一次HA服

务器

![](assets/bCsLEyuwZNGX_VH4czudB0M81GnnF_9dLQNIHeOkQos=.jpeg)

重启后，可以看到HACS图标正常显示了，插件也都能看到

![](assets/0cUZM3yx2wBm7CXjDtQLAMGANhoChqJ2a9nwGEHvkWU=.jpeg)

###

### Tips：在使用HACS关联米家，美的等设备前，先在openwrt中为它们设置静态ip！然后重启设备重新联网！
