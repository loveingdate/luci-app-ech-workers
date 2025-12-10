# LuCI App for Tuple ECH Worker

[![GitHub](https://img.shields.io/badge/GitHub-ECH--Workers-blue?logo=github)](https://github.com/byJoey/ech-wk)
[![License](https://img.shields.io/badge/License-GPL--3.0-green.svg)](LICENSE)
[![OpenWrt](https://img.shields.io/badge/OpenWrt-LuCI-blue.svg)](https://openwrt.org/)

OpenWrt LuCI 图形界面配置应用，用于管理 [ECH Workers](https://github.com/byJoey/ech-wk) 代理服务。

> 🙏 **致谢**: 本项目基于 [byJoey/ech-wk](https://github.com/byJoey/ech-wk) 开发，感谢原作者的出色工作！

---

## ✨ 功能特性

- 🔒 **ECH 加密**: 支持 Encrypted Client Hello (TLS 1.3)，隐藏真实 SNI
- 🌐 **多协议代理**: 同时支持 SOCKS5 和 HTTP/HTTPS 代理协议
- 🇨🇳 **智能分流**: 全局代理 / 跳过中国大陆 / 直连三种模式
- 📊 **Web 管理**: LuCI 图形界面，配置简单直观
- 🔄 **服务管理**: 支持启动/停止/重启，实时查看运行状态和日志
- 🚀 **自动重启**: 基于 procd 的进程管理，服务崩溃自动恢复

---

## 📦 安装方法

### 下载预编译包

从 [Releases](../../releases) 页面下载最新版本：

| 文件 | 说明 |
|------|------|
| `luci-app-ech-workers_x.x.x_all.ipk` | LuCI 界面包 |
| `ech-workers-linux-arm64` | ARM64 架构二进制 |
| `ech-workers-linux-armv7` | ARMv7 架构二进制 |
| `ech-workers-linux-amd64` | x86_64 架构二进制 |

### 安装步骤

1. **上传到路由器**

   ```bash
   # 替换 <arch> 为你的路由器架构 (arm64/armv7/amd64)
   scp luci-app-ech-workers_*.ipk root@192.168.1.1:/tmp/
   scp ech-workers-linux-<arch> root@192.168.1.1:/tmp/
   ```

2. **SSH 登录安装**

   ```bash
   ssh root@192.168.1.1
   
   # 安装二进制文件
   mv /tmp/ech-workers-linux-* /usr/bin/ech-workers
   chmod +x /usr/bin/ech-workers
   
   # 安装 LuCI 应用
   opkg install /tmp/luci-app-ech-workers_*.ipk
   ```

3. **访问界面**

   打开浏览器访问路由器管理页面，导航到 **服务 → Tuple ECH Worker**

---

## ⚙️ 配置说明

### 基本设置

| 配置项 | 说明 | 示例值 |
|--------|------|--------|
| **启用** | 开启/关闭服务 | ✓ |
| **服务器地址** | Workers 服务端地址 | `your-worker.workers.dev:443` |
| **监听地址** | 本地代理监听端口 | `0.0.0.0:30001` |
| **身份令牌** | 服务端验证密钥 | 可选 |

### 高级设置

| 配置项 | 说明 | 默认值 |
|--------|------|--------|
| **优选 IP/域名** | Cloudflare CDN 优选地址 | `joeyblog.net` |
| **DoH 服务器** | DNS over HTTPS 服务器 | `dns.alidns.com/dns-query` |
| **ECH 域名** | 用于获取 ECH 配置 | `cloudflare-ech.com` |

### 分流模式

| 模式 | 说明 |
|------|------|
| **全局代理** | 所有流量通过代理 |
| **跳过中国大陆** | 国内 IP 直连，其他走代理（推荐） |
| **直连模式** | 所有流量直连，不使用代理 |

---

## 🔧 客户端配置

服务启动后，在需要代理的设备上配置：

| 协议 | 地址 | 端口 |
|------|------|------|
| SOCKS5 | 路由器 IP | 30001（默认） |
| HTTP | 路由器 IP | 30001（默认） |

### 示例

- **Windows**: 系统设置 → 网络和 Internet → 代理 → 手动设置代理
- **macOS**: 系统偏好设置 → 网络 → 高级 → 代理
- **iOS/Android**: WiFi 设置 → 配置代理 → 手动
- **浏览器插件**: SwitchyOmega、FoxyProxy 等

---

## 🐛 故障排除

### 查看服务状态

```bash
/etc/init.d/ech-workers status
```

### 查看运行日志

```bash
logread -e ech-workers | tail -n 50
```

### 手动测试运行

```bash
/usr/bin/ech-workers -f your-worker.workers.dev:443 -l 0.0.0.0:30001
```

### 常见问题

| 问题 | 解决方案 |
|------|----------|
| 服务无法启动 | 检查服务器地址是否正确，确保二进制文件有执行权限 |
| 无法连接代理 | 检查防火墙设置，确保监听端口未被占用 |
| 速度慢 | 尝试更换优选 IP 或 DoH 服务器 |

---

## 📁 目录结构

```text
luci-app-ech-workers/
├── Makefile                 # OpenWrt SDK 构建配置
├── README.md                # 说明文档
├── luasrc/
│   ├── controller/          # LuCI 控制器
│   ├── model/cbi/           # CBI 配置模型
│   └── view/ech-workers/    # 视图模板
├── po/                      # 国际化翻译
└── root/                    # 系统配置文件
    └── etc/
        ├── config/          # UCI 默认配置
        ├── init.d/          # procd 服务脚本
        └── uci-defaults/    # 首次安装脚本
```

---

## 📄 许可证

本项目采用 [GPL-3.0](LICENSE) 许可证。

---

## 🔗 相关链接

- **ECH Workers 核心项目**: [byJoey/ech-wk](https://github.com/byJoey/ech-wk)
- **OpenWrt 官网**: [openwrt.org](https://openwrt.org/)
- **LuCI 文档**: [LuCI Wiki](https://openwrt.org/docs/guide-developer/luci)

---

## 🤝 贡献

欢迎提交 Issue 和 Pull Request！
