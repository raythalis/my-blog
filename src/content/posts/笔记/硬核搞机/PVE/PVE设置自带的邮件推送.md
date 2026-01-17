---
updated: 2026-01-17
title: PVE设置自带的邮件推送
published: 2026-01-17
description: ""
image: ""
tags:
  - PVE
category: 硬核搞机
draft: false
---
# PVE设置自带的邮件推送

既然测试邮件能收到但备份邮件不行，而你又提到了 Postfix，这通常是因为**备份任务发送邮件时的“发件人地址”不符合邮件服务器的校验规则**（许多运营商如 QQ/Gmail 要求发件人必须与登录账号一致）。

以下是在 PVE 中配置 Postfix 外部中继（SMTP Relay）的标准流程：

### 1. 安装必要组件

确保系统有 SASL 认证支持：

```shellscript
apt update && apt install libsasl2-modules -y

```

### 2. 配置 Postfix 账号密码

你需要创建一个保存邮箱账号和授权码（或密码）的文件。

* 创建/编辑密码文件：

```shellscript
nano /etc/postfix/sasl_passwd

```

* 写入以下内容（替换为你的信息）：

```
[smtp.example.com]:587  your-email@example.com:your-password-or-token

```

*(注：保留中括号，**`smtp.example.com`**&#x20;换成你的服务器，如&#x20;**`smtp.qq.com`**&#x20;或&#x20;**`smtp.gmail.com`**)*

* 设置权限并生成数据库：

```shellscript
chmod 600 /etc/postfix/sasl_passwd
postmap /etc/postfix/sasl_passwd

```

### 3. 修改 Postfix 主配置文件

编辑 `/etc/postfix/main.cf`，在文件末尾添加或修改以下配置：

```shellscript
# 转发服务器地址
relayhost = [smtp.example.com]:587

# 启用 SASL 认证
smtp_sasl_auth_enable = yes
smtp_sasl_password_maps = hash:/etc/postfix/sasl_passwd
smtp_sasl_security_options = noanonymous

# 启用 TLS 加密
smtp_use_tls = yes
smtp_tls_security_level = encrypt
smtp_tls_CAfile = /etc/ssl/certs/ca-certificates.crt

```

### 4. 解决“发件人拒绝”的关键：地址重写 (关键步骤)

备份任务发送时，PVE 默认可能使用 `root@your-hostname`。如果你的邮件商要求 `From` 必须是 `your-email@example.com`，备份邮件就会被拒绝。

* 编辑 `/etc/postfix/generic`：

```shellscript
nano /etc/postfix/generic

```

* 添加以下映射，将所有系统生成的邮件地址都重写为你的真实发件邮箱：

```
root@pam       your-email@example.com
root@localhost your-email@example.com
root@pve       your-email@example.com

```

* 生成数据库并重启服务：

```shellscript
postmap /etc/postfix/generic
# 在 main.cf 中启用此映射
echo "smtp_generic_maps = hash:/etc/postfix/generic" >> /etc/postfix/main.cf
systemctl restart postfix

```

***

### 5. 如何验证是否修复？

使用 `journalctl` 实时监控，然后去手动跑一个备份：

```shellscript
journalctl -u postfix -f

```

**重点观察日志：**

* 如果看到 `status=sent (250 OK)`：说明发送成功，去垃圾箱找找。
* 如果看到 `Sender address rejected: not owned by user`：说明你的 **Step 4 (地址重写)** 没生效或填错了。
* 如果看到 `Authentication failed`：请检查 **Step 2** 的账号和授权码。

***

[PVE安装](http://192.168.5.100:3010/workspace/e24da8a2-e2c4-4e85-9821-67300b920bf5/TcF6BTR1jr)

[PVE基础优化设置](http://192.168.5.100:3010/workspace/e24da8a2-e2c4-4e85-9821-67300b920bf5/D8GTfuFET1)
