# Docker

Docker是容器运行时与管理工具的核心（后台的 Docker Engine 守护进程 + 命令行 `docker`）。
**做什么**：构建镜像、运行/停止容器、管理网络/卷等。
**典型命令**：`docker build`、`docker run`、`docker ps`、`docker logs`。
**适合谁**：需要直接操控单个或少量容器、做镜像构建与发布、在 Linux 服务器上部署的人。

在 Linux 上通常直接安装 Docker Engine 就能用；在 macOS/Windows 上“原生”没有 Linux 容器能力，需要借助 Docker Desktop。

## Docker Compose

一个“多容器编排”工具（现在多为 `docker compose` 子命令，原来有独立的 `docker-compose`）。

用一个 `compose.yaml/docker-compose.yml` 描述一组服务（例如 web + db + redis），一条命令一起启动/停止、统一网络、依赖关系、环境变量、卷挂载等。

**典型命令**：`docker compose up -d`、`docker compose down`、`docker compose logs`。
**适合谁**：本地开发环境、多服务项目联调、简单部署（不需要 Kubernetes 那么重）。

> Compose **不是**容器运行时，它依赖 Docker Engine 来真正创建/运行容器。

## Docker Desktop

面向 macOS/Windows（也有 Linux 版，但主要价值在前两者）的“一体化桌面应用”。

在 macOS/Windows 上提供一个 Linux 虚拟化环境（VM/WSL2）来运行 Docker Engine

集成 Docker CLI、Compose、镜像管理、GUI、自动更新、Kubernetes（可选）、扩展插件等
**适合谁**：在 macOS/Windows 上做容器开发、希望开箱即用+图形界面管理的人。

> Desktop = “Docker Engine（跑在 VM/WSL2 里）+ CLI/Compose + GUI/周边功能”。
