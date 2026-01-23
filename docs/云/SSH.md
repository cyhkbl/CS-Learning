# SSH

> SSH key（SSH 密钥）是一套非对称加密的“钥匙对”。

在现实使用中，SSH key 通常比强密码更安全，但前提是把私钥保护好。

## 查看密钥文件

ssh-key路径：

```
C:\Users\用户名\.ssh\
```

进入 `.ssh` 目录后，可以查看是否存在以下文件：
   
- **公钥**（用于配置到远程平台）  

```
id_rsa.pub
```

- **私钥**（需保密，切勿泄露）

```
id_rsa
```

- 如果使用其他算法（如 Ed25519），文件名可能是 `id_ed25519` 和 `id_ed25519.pub`。

## 生成 SSH 密钥

如果 `~/.ssh/` 目录中没有密钥文件，可以通过以下命令生成：

```bash
ssh-keygen -t ed25519 # 推荐使用 Ed25519 算法
```

或传统 RSA 算法：
```bash
ssh-keygen -t rsa -b 4096
```

生成过程中会提示选择保存路径，默认直接按回车即可（默认保存到 `~/.ssh/`）。

## 给私钥加passphrase提高安全性

在 PowerShell（或 Git Bash）执行：

```bash
ssh-keygen -p -f $env:USERPROFILE\.ssh\id_ed25519
```

输入时，为了保证安全，一般不会在窗口中显示输入的内容。

### 让系统记住 passphrase

可以使用 Windows 自带 ssh-agent。在 PowerShell（管理员） 中：

```pwsh
Get-Service ssh-agent | Set-Service -StartupType Automatic
Start-Service ssh-agent
ssh-add $env:USERPROFILE\.ssh\id_ed25519
```

输入一次 passphrase 后，会缓存一段时间（具体看系统/会话）。

查看已加载 key：

```pwsh
ssh-add -l
```

## 连接

### 显式指定私钥

```pwsh
ssh -i ~/.ssh/your-key.pem username@<虚拟机公网IP>
```

### 密码登录

```pwsh
ssh username@<虚拟机公网IP>
```

### 配置 SSH 配置文件简化连接

编辑 .ssh/config 文件（没有则创建），添加以下内容：

```plaintext
Host azure-vm
    HostName <虚拟机公网IP>
    User <用户名>
    IdentityFile .ssh/your-key.pem
```

保存后，可以通过以下命令连接：

```bash
ssh azure-vm
```

## 主机

可以将自己的电脑设置为ssh主机，允许其他设备通过ssh连接到自己的电脑。

### 安装与配置

#### 安装

Windows 10及以上版本可以通过“可选功能”安装“OpenSSH 服务器”。

#### 服务

打开 PowerShell（管理员身份），启动SSH Server服务并设置自启。

```pwsh
Start-Service sshd
Set-Service -Name sshd -StartupType 'Automatic'
```

配置防火墙规则：

```pwsh
# 允许 22 端口通过防火墙
if (!(Get-NetFirewallRule -Name "OpenSSH-Server-In-TCP" -ErrorAction SilentlyContinue)) {
    New-NetFirewallRule -Name "OpenSSH-Server-In-TCP" -DisplayName "OpenSSH Server (sshd)" -Enabled True -Direction Inbound -Protocol TCP -Action Allow -LocalPort 22
}
```

#### 配置 DDNS

家用公网 IP 通常会定期变动。需要一个域名实时指向家里当前的 IP。查看路由器后台（“高级设置” -> “动态 DNS”或 “DDNS”）。

我用的是公云：https://www.pubyun.com/，点击“申请动态域名”。然后在路由器界面配置pub云上创建的用户名、密码、动态域名即可。状态检查可以填写每 5 分钟检查一次 IP 是否变动，强制更新可以填写每 24 小时无论 IP 是否变动都强制刷新一次（防止账号因长时间不活跃被回收）。这样，只需要连接域名即可（例如我的域名`blank.x3322.net`对应IP）。

#### 配置静态 IP

在DHCP静态IP分配中为电脑配置静态内网IP。

#### 配置端口转发

* **外部端口**：建议使用非标准端口（如 `8822`），以规避针对 `22` 端口的自动化脚本攻击。
* **内部端口**：`22`（Windows SSH 的默认端口）。
* **内部 IP**：电脑的固定 IP（`192.168.1.100`）。
* **协议**：TCP。

这样，在公网连接`blank.x3322.net:8822`即可通过路由器转发到22端口访问家里的电脑。

#### SSH 密钥登录

长期将 SSH 端口暴露在公网，仅靠密码是非常危险的。**强烈建议禁用密码登录，改用密钥。**

1. 在手机 SSH 客户端（如 Termius）中生成一对密钥（RSA 或 Ed25519），并复制其**公钥 (Public Key)**。
2. 在 Windows 的 `C:\Users\你的用户名\.ssh\` 文件夹下，新建一个名为 `authorized_keys` 的文件（无后缀名）。
3. 将手机生成的公钥粘贴进去。
4. **修改权限**：这一步非常关键，右键该文件 -> 属性 -> 安全 -> 高级，**禁用继承**，并确保只有你自己的账号和 SYSTEM 拥有读取权限，否则 SSH 服务会出于安全考虑拒绝该密钥。
5. 修改`sshd_config`：以管理员身份运行记事本，打开：C:\ProgramData\ssh\sshd_config，滚动到文件最底部。找到这两行，并在每一行开头添加 # 号将其注释掉：
```Plaintext
# Match Group administrators
#       AuthorizedKeysFile __PROGRAMDATA__/ssh/administrators_authorized_keys
```

保存文件，在 PowerShell 中重启服务使其生效：

```PowerShell
Restart-Service sshd
```

### 禁用密码登录 (慎重，确保密钥能通后再操作)

编辑 `C:\ProgramData\ssh\sshd_config`：

```text
PubkeyAuthentication yes
PasswordAuthentication no  # 关键：验证成功后改为此项

```

然后重启 SSH 服务：`Restart-Service sshd`。

### 连接

在手机上**关闭代理**（或者进行合理的代理配置），使用 Termius 等 SSH 客户端连接。