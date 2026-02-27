---
title: 使用 Ventoy 实现多 ISO 启动盘 + WePE 安装 Windows 11 并激活
date: 2026-02-27
tags:
 - ventoy
 - wepe
 - windows
 - 装机
categories:
 - 实习
---

这篇笔记记录如何使用 Ventoy 制作多 ISO 合一的 USB 启动盘，配合 WePE 完成 Windows 11 的安装，并使用 HEU_KMS_Activator 完成激活。

## ✅ 1. Ventoy 简介与优势

Ventoy 是一款开源的 USB 启动盘制作工具，最大的特点是：**只需将 ISO 文件复制到 U 盘中即可直接启动，无需反复格式化**。

与传统的 Rufus、UltraISO 等工具不同，Ventoy 支持在同一个 U 盘中存放多个 ISO 镜像，开机时通过菜单选择要启动的镜像，非常适合需要维护多个操作系统的场景。

---

## ✅ 2. 制作 Ventoy 启动盘

### ① 下载 Ventoy

前往 [Ventoy 官网](https://www.ventoy.net) 下载最新版本的 Ventoy，解压后运行 `Ventoy2Disk.exe`。

### ② 安装 Ventoy 到 U 盘

1. 插入 U 盘（建议 16GB 以上）
2. 打开 `Ventoy2Disk.exe`，在设备列表中选择目标 U 盘
3. 分区类型建议选择 **GPT**（适配 UEFI 启动）
4. 点击 **安装**，等待完成（⚠️ 会格式化 U 盘，注意备份数据）

安装完成后，U 盘会被分为两个分区：
- **exFAT 数据分区**：用于存放 ISO 文件
- **Ventoy 引导分区**：隐藏分区，用于引导启动

### ③ 复制 ISO 文件到 U 盘

将以下 5 个 ISO 镜像文件直接复制到 U 盘的数据分区根目录：

| 文件名 | 用途 |
|---|---|
| `cn_windows_7_professional_with_sp1_x64_dvd_u_677031.iso` | Windows 7 专业版 |
| `cn_windows_10_multiple_editions_x64_dvd_6848463.iso` | Windows 10 多版本 |
| `ubuntu-24.04.4-desktop-amd64.iso` | Ubuntu 24.04 桌面版 |
| `WePE_64_V2.3.iso` | 微PE工具箱（PE环境） |
| `Win11_25H2_Pro_Chinese_Simplified_x64.iso` | Windows 11 25H2 专业版 |

无需解压，直接放入即可。Ventoy 会自动识别这些 ISO 文件。

---

## ✅ 3. 从 Ventoy 启动

### ① 进入 BIOS 设置 U 盘启动

1. 重启目标电脑，按对应快捷键进入 BIOS/Boot Menu（常见：`F2`、`F12`、`Del`、`Esc`）
2. 将启动模式设置为 **UEFI**（推荐）
3. 将 U 盘设为第一启动项，保存并重启

### ② Ventoy 启动菜单

启动后会进入 Ventoy 的引导菜单，列出 U 盘中所有的 ISO 文件：

```
cn_windows_7_professional_with_sp1_x64_dvd_u_677031.iso
cn_windows_10_multiple_editions_x64_dvd_6848463.iso
ubuntu-24.04.4-desktop-amd64.iso
WePE_64_V2.3.iso
Win11_25H2_Pro_Chinese_Simplified_x64.iso
```

用方向键选择要启动的镜像，回车即可进入。

---

## ✅ 4. 使用 WePE 安装 Windows 11

直接从 Ventoy 菜单选择 Windows 11 ISO 可以进行安装，但在某些情况下（如需要分区、绕过 TPM 检测等），通过 **WePE** 来安装会更灵活。

### ① 启动 WePE

在 Ventoy 菜单中选择 `WePE_64_V2.3.iso`，回车进入微PE工具箱。

进入 PE 桌面后，你将获得一个完整的 Windows PE 环境，包含常用的装机工具。

### ② 磁盘分区（如需要）

如果是全新硬盘或需要重新分区：

1. 打开 PE 桌面上的 **DiskGenius** 或 **分区助手**
2. 选择目标硬盘，删除旧分区（⚠️ 注意数据备份）
3. 将磁盘转换为 **GPT** 格式（适配 UEFI + Windows 11）
4. 创建 **ESP 分区**（300MB）和 **MSR 分区**（128MB）
5. 剩余空间创建 **NTFS 主分区**，用于安装系统

### ③ 安装 Windows 11

1. 打开 PE 桌面上的 **Windows 安装器**（WinNTSetup）
2. 在安装器中进行如下配置：
   - **安装源**：浏览到 U 盘中的 `Win11_25H2_Pro_Chinese_Simplified_x64.iso`，选择其中的 `sources/install.wim`（或 `install.esd`）
   - **引导驱动器**：选择 ESP 分区（通常为 Z: 或系统自动识别的 EFI 分区）
   - **安装驱动器**：选择刚才创建的 NTFS 主分区（如 C:）
3. 引导方式选择 **UEFI**
4. 点击 **开始安装**，等待映像释放完成
5. 释放完成后，拔掉 U 盘，重启电脑

### ④ 完成 Windows 11 初始化设置

重启后电脑会自动从硬盘引导，进入 Windows 11 的 OOBE（开箱体验）：

1. 选择地区和键盘布局
2. 连接网络（如需跳过可按 `Shift + F10` 打开命令行输入 `oobe\bypassnro` 回车重启）
3. 设置用户名和密码
4. 完成后进入 Windows 11 桌面

---

## ✅ 5. 使用 HEU_KMS_Activator 激活 Windows 11

### ① 准备激活工具

将 `HEU_KMS_Activator_v42.3.0` 复制到已安装好的 Windows 11 系统中。

> ⚠️ 运行前建议先**关闭 Windows Defender** 或将该工具**添加到排除项**中，否则可能被误报拦截。

### ② 执行激活

1. **右键以管理员身份运行** `HEU_KMS_Activator_v42.3.0.exe`
2. 在主界面点击 **开始** 按钮（默认会自动识别当前系统版本）
3. 工具会自动完成激活，提示"激活成功"

### ③ 验证激活状态

激活完成后，验证是否成功：

1. 打开 **设置 → 系统 → 激活**
2. 确认显示"Windows 已激活"
3. 或按 `Win + R`，输入 `slmgr.vbs -xpr`，弹窗显示激活状态

---

## ✅ 6. 完整操作流程总结

```
制作启动盘                    安装系统                         激活系统
    │                           │                               │
    ▼                           ▼                               ▼
安装 Ventoy 到 U 盘     Ventoy 菜单选择 WePE 启动      复制 HEU_KMS_Activator
    │                           │                               │
    ▼                           ▼                               ▼
复制 5 个 ISO 到 U 盘    PE 中用 DiskGenius 分区         以管理员身份运行
    │                           │                               │
    ▼                           ▼                               ▼
设置 BIOS U 盘启动       WinNTSetup 释放 Win11 镜像      点击"开始"激活
                                │                               │
                                ▼                               ▼
                         重启完成 OOBE 设置              验证激活状态
```

---

## ✅ 7. 常见问题

- **Ventoy 菜单没有显示 ISO 文件**：确认 ISO 文件放在数据分区根目录，文件名不含特殊字符
- **WePE 无法识别 Win11 ISO**：检查 ISO 文件是否完整，或尝试挂载后手动定位 `install.wim`
- **Windows 11 提示不满足硬件要求**：通过 PE 中的 WinNTSetup 安装可以绕过 TPM 和安全启动检测
- **HEU_KMS 被杀毒软件拦截**：运行前关闭实时防护，或将工具目录添加到排除列表
- **激活后显示"通知模式"**：重新运行一次 HEU_KMS 或尝试切换激活模式（数字许可证 / KMS）

---
