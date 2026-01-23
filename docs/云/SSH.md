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

### 安装

Windows 10及以上版本可以通过“可选功能”安装“OpenSSH 服务器”。也可以使用Scoop等包管理工具安装。

```pwsh
scoop install openssh
```

安装完成后，以管理员身份运行以下脚本安装SSH Server服务：

```pwsh
sudo $dir\install-sshd.ps1
```

### 启动服务

OpenSSH 服务启动时需要密钥文件。如果这些文件不存在，服务会直接崩溃。进入OpenSSH安装目录，在管理员权限的 PowerShell 中运行以下命令：

```pwsh
.\ssh-keygen.exe -A
```

