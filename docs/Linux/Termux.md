# Termux

Termux 是一个 Android 平台上的终端模拟器和 Linux 环境，允许用户在 Android 设备上运行 Linux 命令行工具和应用程序。它提供了一个轻量级的 Linux 环境，适合开发者、系统管理员和 Linux 爱好者在移动设备上进行编程、系统管理和学习。

## SSH

1. **安装 OpenSSH：**

```bash
pkg update && pkg install openssh -y
```

2. **设置密码：**

Termux 的 SSH 默认不带密码，必须手动设置：
```bash
passwd
```


3. **启动 SSH 服务：**

```bash
sshd
```

*(Termux 的 SSH 默认端口是 **8022**)*

从电脑端连接：

1. **获取 Termux 用户名：**
在 Termux 输入 `whoami`（通常会返回类似 `u0_a123` 的字符串）。
2. **获取 IP 地址：**
输入 `ifconfig` 查看 `wlan0` 的 IP。
3. **电脑端连接：**
```bash
ssh user@<IP> -p 8022
```

## Proot

Proot 是 Termux 中的一个工具，允许用户在 Android 上创建一个隔离的 Linux 环境，类似于容器或虚拟机。它使用用户空间的技术来模拟 Linux 文件系统和环境，使得用户可以在 Termux 中运行不同版本的 Linux 发行版，如 Ubuntu、Debian 等，而无需 root 权限。