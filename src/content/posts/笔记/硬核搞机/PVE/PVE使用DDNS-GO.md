---
title: PVE使用DDNS-GO
published: 2026-01-17
description: ""
image: ""
tags:
  - PVE
category: 硬核搞机
draft: false
updated: 2026-01-17
---
# PVE使用DDNS-GO

在 Proxmox VE (PVE) 上使用 **ddns-go** 来实现 DDNS 更新，其实就是在 PVE 或其宿主系统上运行一个客户端，让它定期更新你的动态公网 IP 到域名解析服务。下面给你整理一个完整步骤：

***

## 1️⃣ 准备工作

1. **PVE 系统**：基于 Debian/Ubuntu，所以大部分命令可以直接使用 `apt`。
2. **安装依赖**（可选，但建议）：

```shellscript
apt update
apt install wget curl unzip -y
```

1. **获取 ddns-go**：

&#x20;  \* 官方 GitHub 地址：https://github.com/jeessy2/ddns-go

&#x20;  \* 下载最新 release，例如：

```shellscript
wget https://github.com/jeessy2/ddns-go/releases/download/v1.9.2/ddns-go_linux_amd64.zip
unzip ddns-go_linux_amd64.zip
chmod +x ddns-go
```

***

## 2️⃣ 配置 ddns-go

### 方法一：命令行交互

```shellscript
./ddns-go
```

第一次运行会打开交互式界面或提供网页配置地址（默认 `http://127.0.0.1:8080`）。

在浏览器中访问这个地址，添加你的 DDNS 提供商账号和域名。

### 方法二：使用配置文件

可以直接创建 `config.json`（在同目录或 `/etc/ddns-go/`）：

```json
{
    "provider": "cloudflare",
    "domain": "example.com",
    "host": "home",
    "email": "你的CF邮箱",
    "apikey": "你的CF全局API密钥",
    "interval": 300
}
```

* `interval` 是更新间隔，单位秒。

***

## 3️⃣ 测试运行

```shellscript
./ddns-go -c config.json
```

* 如果成功，会显示类似：`update success: your current IP is x.x.x.x`

***

## 4️⃣ 设置开机自启

### 方法 A：systemd 服务（推荐）

创建服务文件 `/etc/systemd/system/ddns-go.service`：

```ini
[Unit]
Description=ddns-go service
After=network.target

[Service]
Type=simple
ExecStart=/root/ddns-go/ddns-go -c /root/ddns-go/config.json
Restart=always
User=root

[Install]
WantedBy=multi-user.target
```

启用并启动：

```shellscript
systemctl daemon-reload
systemctl enable ddns-go
systemctl start ddns-go
systemctl status ddns-go
```

### 方法 B：cron 定时（如果不想用 systemd）

```shellscript
crontab -e
```

添加：

```shellscript
*/5 * * * * /root/ddns-go/ddns-go -c /root/ddns-go/config.json
```

* 每 5 分钟更新一次 IP。

***

## 5️⃣ 常见问题

1. **DDNS 无法更新**：检查防火墙，确保 ddns-go 可以访问 DDNS 提供商 API。
2. **多个域名/记录**：ddns-go 支持多条配置，可以在 config.json 里配置数组或在 web UI 添加。
3. **PVE 防火墙**：如果启用了 PVE 内置防火墙，要允许 outgoing HTTPS。
