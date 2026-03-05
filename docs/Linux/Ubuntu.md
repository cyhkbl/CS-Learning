# Ubuntu

## 用户

创建用户:

```bash
# 创建用户
# -m 自动创建家目录 (/home/username)
# -s 指定默认 Shell 为 bash，这样才有高亮和补全
adduser admin
```

赋予 `sudo` 权限：

```bash
# 将用户加入 sudo 组
usermod -aG sudo admin
```

执行以下命令切换到新用户，并检查权限是否生效：

(安装sudo：`apt update && apt install sudo`)

```bash
su - admin

# 验证是否有 sudo 权限
sudo whoami
# 如果输出是 "root"，说明已经是一个合格的“正常用户”了
```