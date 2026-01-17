---
title: PVE 2FA
published: 2026-01-17
description: ""
image: ""
tags:
  - PVE
category: 硬核搞机
draft: false
updated: 2026-01-17
---
# PVE 2FA

简单来说，**PVE (Proxmox Virtual Environment) 是支持双重认证 (2FA) 的**。

作为一个企业级的虚拟化平台，PVE 提供了非常完善的安全机制。既然你已经通过 Lucky 实现了公网暴露，为 PVE 开启 2FA 是极其重要的安全防护手段，可以防止别人通过暴力破解进到你的底层系统。

***

### PVE 支持的 2FA 类型

PVE 目前支持多种主流的二次验证方式：

* **TOTP (最推荐)**：使用手机上的 **Google Authenticator**、**Microsoft Authenticator** 或 **Authy**。每次登录时输入 6 位动态验证码。
* **WebAuthn / FIDO2**：支持 **Yubikey** 等硬件安全密钥，或者某些电脑自带的指纹识别（TouchID/Windows Hello）。
* **Recovery Keys (恢复码)**：在你开启 2FA 时，PVE 会生成一组一次性代码。如果手机丢了，可以用这些代码登录。
* **Yubico OTP**：专门针对 Yubikey 提供的动态码验证。

***

### 如何开启 PVE 的 2FA（以 TOTP 为例）

1. **登录 PVE 后台**。
2. 点击右上角的用户名（例如 `root@pam`），选择 **“权限 (Permissions)”** -> **“双重身份验证 (Two-Factor Authentication)”**。
3. 点击 **“添加 (Add)”**，选择 **“TOTP”**。
4. **扫码**：用你手机上的身份验证器 APP 扫描屏幕上的二维码。
5. **验证**：输入 APP 上显示的 6 位验证码，并输入你当前的 PVE 登录密码。
6. **保存恢复码**：系统会给出一组恢复码，请务必记在纸上或存入密码管理器，**防止手机丢失导致进不去系统**。

***

### ⚠️ 特别提醒：关于“多重保险”

既然你正在使用 **Lucky** 且开启了 **CorazaWAF**，针对 PVE 的访问，建议做以下三重加固：

1. **PVE 本身开启 2FA**：这是最后的防线。
2. **Lucky 访问控制**：在 Lucky 的反向代理规则里，可以针对 PVE 的子域名设置 IP 白名单，或者开启 Lucky 自带的 **“第三方认证”**。
3. **内网隔离**：如果条件允许，尽量不要直接在公网暴露 PVE 管理页面，而是通过 **Tailscale/ZeroTier** 组网，或者通过 Lucky 的安全隧道进入。

**你现在是已经能通过&#x20;****`域名:端口`****&#x20;访问 PVE 管理界面了吗？建议先开启 TOTP，这是性价比最高的防御方式。**
