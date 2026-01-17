# Cloudflare

## WARP

WARP 是一款基于 WireGuard 协议的免费系统级 VPN。在校园网环境下，它的核心价值在于提供 **IPv6 入口**。（没错说的就是你浙大）

开启 WARP 后，它会在本地建立虚拟网卡，将 IPv4 下载请求封装在 IPv6 数据包中，通过的 IPv6 链路传输至 Cloudflare 边缘节点进行流量中转。当先开 WARP 再传输流量时，流量都会去Cloudflare节点洗成IPv6。