# Azure

## 云虚拟机

### 创建云虚拟机

在 Azure 上点击虚拟机，选择需要的映像。可免费使用的 VM 包括 B1s、B2pts v2（ARM）、B2ats v2（AMD），每月最多 750 小时，持续 12 个月。

![创建云虚拟机](https://cdn.jsdelivr.net/gh/cyhkbl/picture_bed@main/vm.png)

身份验证更推荐使用 SSH 密钥（只要你相信密码学）。当然我选择了密码（笑）。

### 远程桌面

虽然命令行也能用，但 GUI 更直观。下面介绍使用 xRDP + XFCE 桌面环境的方式。

#### 安装

```bash
sudo apt update
sudo apt install -y xfce4 xfce4-goodies xrdp dbus-x11
```

2) 让 xRDP 使用 XFCE 会话
echo "startxfce4" | sudo tee /etc/skel/.xsession
echo "startxfce4" > ~/.xsession

3) 启用并重启服务
sudo systemctl enable xrdp
sudo systemctl restart xrdp

4) 放通 3389（RDP）端口（Azure 侧最关键）

在 Azure Portal：

进入 虚拟机 → Networking（网络）

给 NSG 入站规则加一条：TCP 3389 允许

强烈建议：Source 不要写 Any，改成你的公网 IP（或使用 VPN / Bastion）

也可以在 VM 上确认防火墙（如果启用了 UFW）：

sudo ufw allow 3389/tcp
sudo ufw status

5) 用客户端连接

Windows：mstsc 远程桌面

macOS：Microsoft Remote Desktop

Linux：Remmina

连接地址：

公网 IP（或通过 VPN/Bastion 的跳板地址）

用户名/密码：你的 Ubuntu 用户（建议不要用 root）

常见坑：首次连接出现黑屏/卡住，多半是会话配置或权限问题。通常装了 dbus-x11 + 设置 .xsession 就能解决；不行再试试 sudo adduser xrdp ssl-cert 后重启 xrdp。

## 安全：最少暴露面 + 更新 + 认证

禁用密码登录、改用 SSH Key（强烈推荐）

定期更新：

sudo apt update && sudo apt upgrade -y


开启防火墙（UFW）只放行必要端口：

sudo ufw allow OpenSSH
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw enable
sudo ufw status

稳定运行：用 systemd / Docker 让服务“常驻”

systemd 管理：开机自启、崩溃重启、日志可查

或者 Docker Compose：部署更方便、环境一致