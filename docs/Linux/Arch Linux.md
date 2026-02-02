# Arch Linux

## WSL2

### 安装

```pwsh
wsl --install -d archlinux
```

### 配置

#### 全量更新系统

Arch 是滚动发行：

```bash
pacman -Syu
```

如果提示 keyring 或签名问题，可以先：

```bash
pacman -Sy archlinux-keyring
pacman -Syu
```

#### 安装 sudo

```bash
pacman -S sudo
```

#### 创建用户和密码

创建用户（含 home 目录、默认 shell、加入常用组）:

```bash
useradd -m -G wheel -s /bin/bash 用户名
```

- `-m`：创建 `/home/用户名`
- `-G wheel`：加入 `wheel` 组（后面用来 sudo）
- `-s`：指定 shell（你也可以用 `/bin/zsh` 等）

设置用户密码:

```bash
passwd 用户名
```

允许 wheel 组使用 sudo（编辑 sudoers）:

```bash
EDITOR=nano visudo
```

找到并取消注释这一行（去掉前面的 `#`）：

```text
%wheel ALL=(ALL:ALL) ALL
```

保存退出。

切换到新用户测试：

```bash
su - 用户名
```

测试 sudo：

```bash
sudo -v
```

设置默认登录用户，写入 `/etc/wsl.conf`：

```bash
sudo tee /etc/wsl.conf <<'EOF'
[user]
default=Henry
[interop]
appendWindowsPath=false
EOF
```

- `default=yourname`：以后进来默认就是普通用户。
- `appendWindowsPath=false`：可选。关掉自动把 Windows PATH 混进来，避免命令冲突。

然后在 Windows 里重启该发行版：
```powershell
wsl --terminate archlinux
wsl -d archlinux
```

#### 设置镜像源

编辑 `/etc/pacman.d/mirrorlist`，把离你近/速度快的源放前面。

例如用 `reflector` 自动选源（先装再生成）：
```bash
sudo pacman -S reflector
sudo reflector --country China --latest 10 --sort rate --save /etc/pacman.d/mirrorlist
sudo pacman -Syyu
```

#### 安装包管理器

> AUR（Arch User Repository）+ AUR 助手（yay / paru）。

先装构建工具：

```bash
sudo pacman -Syu --needed base-devel git
```

再装 AUR 助手（这里用 yay）：

```bash
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si
```

#### 代理

> 检测到 localhost 代理配置，但未镜像到 WSL。NAT 模式下的 WSL 不支持 localhost 代理。

Windows 里如果设置了 `http://127.0.0.1:xxxx` 这类代理，WSL2（NAT）里无法直接用 “localhost” 指向宿主机的代理端口。因为127.0.0.1被认为是linux的localhost。

我这里使用代理软件FLClash。搜索并安装 flclash（看 AUR 是否存在对应包名）：

```bash
yay -Ss flclash
```

安装：

```bash
# 安装原版二进制
yay -S flclash-bin
```

#### 初始化基础工具

装常用工具：

```bash
pacman -S --needed base base-devel sudo git vim nano curl wget openssh ca-certificates
```

#### Locale（语言）

编辑 `/etc/locale.gen`，取消注释你需要的，例如：
- `en_US.UTF-8 UTF-8`
- `zh_CN.UTF-8 UTF-8`

然后生成：
```bash
sudo locale-gen
```

设置默认语言（示例用英文界面更通用）：
```bash
echo 'LANG=en_US.UTF-8' > /etc/locale.conf
```

#### SSH

```bash
pacman -S openssh
```

生成密钥：

```bash
ssh-keygen -t ed25519
```

WSL 里跑 ssh server 一般不如直接用 Windows 的 OpenSSH Server。

#### systemd

开启systemd。在 `/etc/wsl.conf` 加：

```ini
[boot]
systemd=true
```

### GUI

#### 安装桌面环境与 XRDP

```bash
# 安装 Gnome
sudo pacman -S gnome gnome-extra

# 安装 XRDP
yay -S xrdp

# 配置 XRDP 使用 GNOME
echo 'export XDG_SESSION_TYPE=x11
export GDK_BACKEND=x11
exec gnome-session' > ~/.xsession
```

#### 配置并启动服务

```bash
# 修改 XRDP 端口（避免与 Windows 远程桌面冲突）
sudo sed -i 's/port=3389/port=3390/g' /etc/xrdp/xrdp.ini

# 启动服务
sudo systemctl enable xrdp
sudo systemctl start xrdp

# 检查状态
sudo systemctl status xrdp
```

### 3. Windows 连接
1. 按 `Win + R`，输入 `mstsc` 打开远程桌面
2. 连接地址输入：`localhost:3390`
3. 用户名密码填写 Arch Linux 的账户

**防火墙设置**（如果连接失败）：
```bash
# 在 PowerShell（管理员）中执行
New-NetFirewallRule -DisplayName "WSL2 XRDP" -Direction Inbound -LocalPort 3390 -Protocol TCP -Action Allow
```