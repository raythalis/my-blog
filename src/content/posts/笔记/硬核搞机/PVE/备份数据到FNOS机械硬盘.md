---
title: 备份数据到FNOS机械硬盘
published: 2026-01-17
description: ""
image: ""
tags:
  - PVE
category: 硬核搞机
draft: false
updated: 2026-01-17
---
# 备份数据到FNOS机械硬盘

在 PVE（Proxmox VE）上把虚拟机或容器备份到飞牛 NAS，你可以按照以下步骤操作。我假设你的飞牛 NAS可以通过 SMB/CIFS（Windows 文件共享）或者 NFS 访问：

***

### **1. 在飞牛 NAS上创建共享目录**

1. 登录 NAS 管理界面。
2. 创建一个共享文件夹，例如 `pve_backup`。
3. 配置权限：

&#x20;  \* 给 PVE 服务器所在的用户访问权限。

&#x20;  \* 建议启用 SMB/CIFS 或 NFS。

***

### **2. 在 PVE 上挂载 NAS**

你可以选择 **NFS** 或 **CIFS/SMB**，以便 PVE 可以直接访问 NAS。

#### **方式一：NFS**

1. 安装 NFS 客户端：

```shellscript
apt update
apt install nfs-common
```

1. 挂载 NAS：

```shellscript
mkdir -p /mnt/nas_backup
mount -t nfs <NAS_IP>:/共享目录 /mnt/nas_backup
```

例：

```shellscript
mount -t nfs 192.168.5.100:/pve_backup /mnt/nas_backup
```

1. 验证挂载：

```shellscript
df -h
ls /mnt/nas_backup
```

#### **方式二：CIFS/SMB**

1. 安装 SMB 客户端：

```shellscript
apt update
apt install cifs-utils
```

1. 挂载 NAS：

```shellscript
mkdir -p /mnt/nas_backup
mount -t cifs //192.168.5.100/pve_backup /mnt/nas_backup -o username=<NAS用户名>,password=<NAS密码>,vers=3.0
```

1. 验证挂载：

```shellscript
ls /mnt/nas_backup
```

> 如果想开机自动挂载，可以把挂载信息写入 `/etc/fstab`。

***

### **3. 在 PVE Web 界面配置备份存储**

1. 登录 PVE Web 界面。
2. 点击左侧 **数据中心 → 存储**。
3. 点击右上角 **添加 → 目录**（如果是本地挂载的目录）或 **添加 → CIFS/NFS**。
4. 填写：

&#x20;  \* **ID**：随便起一个名字，例如 `nas_backup`

&#x20;  \* **目录/服务器**：

&#x20;    \* 如果是 NFS：填写 NAS IP + 导出目录

&#x20;    \* 如果是 CIFS：填写 `//NAS_IP/共享目录`

&#x20;  \* **内容类型**：选择 `VZDump backup file`

&#x20;  \* **启用**：打勾

5\. 保存。

***

### **4. 创建备份任务**

1. 在 PVE Web 界面，点击 **数据中心 → 备份 → 添加**。
2. 配置：

&#x20;  \* **节点**：选择你要备份的 PVE 节点

&#x20;  \* **存储**：选择你刚添加的 NAS 存储

&#x20;  \* **选择要备份的虚拟机/容器**

&#x20;  \* **备份模式**：快照或停机

&#x20;  \* **调度**：设置自动备份时间

3\. 保存，PVE 会按计划把备份存到 NAS 上。

***
