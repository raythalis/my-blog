---
title: Lucky反代HA报400
published: 2026-01-17
description: ""
image: ""
tags:
  - Lucky
category: 硬核搞机
draft: true
updated: 2026-01-17
---

# Lucky反代HA报400

这是因为 **Home Assistant (HA)** 出于安全考虑，默认会拒绝任何来自“代理服务器”（也就是你的 Lucky）的请求。

在 HA 看来，Lucky 是一个中转站，它担心有人通过伪造中转地址来攻击系统。要解决这个问题，你需要告诉 HA：“**Lucky 是我的人，请信任它。**”

***

### 解决方法：修改 HA 的配置文件

你需要编辑 Home Assistant 的 `configuration.yaml` 文件，手动添加“受信代理”设置。

#### 1. 确认 Lucky 的内网 IP

首先，你要知道 Lucky 所在的设备（也就是你的 OpenWrt 路由器）的 **内网 IPv4 地址**。通常是 `192.168.1.1` 或 `192.168.x.1`。

#### 2. 修改 `configuration.yaml`

在文件中找到或添加 `http:` 部分，填入以下内容：

```yaml
http:
  use_x_forwarded_for: true
  trusted_proxies:
    - 192.168.1.1  # 这里替换成你 OpenWrt/Lucky 的内网 IP
    - 127.0.0.1    # 建议保留
    - ::1          # 建议保留

```

#### 3. 重启 Home Assistant

保存文件后，在 HA 后台点击 **配置 -> 系统 -> 右上角重启按钮**。重启后，400 错误就会消失，你可以正常通过域名访问了。

***

### 为什么会出现这个错误？（原理科普）

1. **身份伪装**：当你通过 `ha.raythalis.cn:16666` 访问时，Lucky 会把请求转交给 HA。
2. **IP 变化**：对于 HA 来说，它看到的访问者 IP 不是你手机的公网 IP，而是 **Lucky 的内网 IP**（如 192.168.1.1）。
3. **安全拦截**：由于请求头里包含了 `X-Forwarded-For` 信息（代表这是个代理请求），HA 会检查这个代理 IP 是否在白名单里。如果没有，它就会报错 `400: Bad Request`。

***

### 进阶提示：Lucky 端的设置

在 Lucky 的反向代理规则里，请确保以下两个开关是开启的（通常默认开启）：

* **透传 Host 头部 (Pass Host Header)**：必须开启，否则 HA 不知道你访问的是哪个域名。
* **透传真实 IP (X-Forwarded-For)**：必须开启。

### 如果还是 400 怎么办？

如果在添加了 `192.168.1.1` 后依然报错，请查看 HA 的 **系统日志**（配置 -> 系统 -> 日志）。

你会看到一条类似这样的报错：

> `A request from a reverse proxy was received from 192.168.x.x, but your HTTP integration is not set up for reverse proxies.`

**日志里显示的那个 IP 地址就是你必须填入&#x20;****`trusted_proxies`****&#x20;列表的地址。**

**你的&#x20;****`configuration.yaml`****&#x20;文件现在能修改吗？如果是在飞牛虚拟机里，你可以通过 HA 的&#x20;****`File Editor`****&#x20;加载项或者&#x20;****`Samba`****&#x20;分享来编辑它。**



**&#xA;&#xA;**&#x5728; Home Assistant OS (HAOS) 中，`configuration.yaml` 是核心配置文件。根据你目前的安装方式，通常有以下 **3 种最简单的方法** 找到并修改它：

***

### 方法一：安装 File Editor 加载项（最推荐，最简单）

这是专为新手准备的网页端编辑器，直接在 HA 界面操作：

1. 进入 HA 界面，点击 **配置** -> **加载项** (Add-ons)。
2. 点击右下角 **加载项商店**。
3. 搜索并安装 **File Editor**。
4. 安装后，开启“在侧边栏显示”，然后点击 **启动**。
5. 点击侧边栏的 **File Editor**，点击左上角的文件夹图标。
6. 在列表里你就能看到 `configuration.yaml`，点击它即可编辑。

***

### 方法二：安装 Studio Code Server（功能最强）

如果你习惯用类似 VS Code 的编辑器，这个是神具：

1. 同样在 **加载项商店** 搜索 **Studio Code Server**。
2. 安装并启动后，在侧边栏打开。
3. 它会自动打开 HA 的配置目录，`configuration.yaml` 就在左侧文件树里。

***

### 方法三：通过 Samba 分享（像访问 U 盘一样修改）

如果你想在 Windows 电脑上直接修改：

1. 在 **加载项商店** 搜索并安装 **Samba share**。
2. 在配置页设置一个用户名和密码。
3. 启动后，在 Windows 的文件资源管理器地址栏输入 `\\你的HA内网IP`。
4. 进入 `config` 文件夹，就能看到那个文件，用记事本或 Notepad++ 就能改。

***

### ⚠️ 修改时的关键注意事项

1. **缩进必须准确**：YAML 文件对缩进极度敏感。`trusted_proxies:` 下方的横杠 `-` 前面必须有 **2 个空格**。
   ```
   http:
     use_x_forwarded_for: true
     trusted_proxies:
       - 192.168.1.1  # 这里的空格不能少
   
   ```
2. 修改后检查：改完文件后，不要直接重启！
   * 进入 **配置** -> **系统** -> **右上角“开发者工具”**。
   * 点击 **检查配置 (Check Configuration)**。
   * 只有显示“配置有效”时，才点击下方的 **重启**。如果报错了，说明你空格没对齐。

***

### 最后确认：Lucky 的 IP 到底是多少？

由于你的 Lucky 装在 OpenWrt 上，那么 `trusted_proxies` 后面填的就是你的 **路由器内网 IP**（通常是 `192.168.1.1`）。

**如果你在修改时遇到“配置无效”的红字报错，把你的代码发给我，我帮你检查缩进。**
