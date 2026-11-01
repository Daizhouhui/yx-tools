# yx-tools - Cloudflare IP 优选测速工具

[![Go](https://img.shields.io/badge/Go-1.22+-00ADD8.svg?style=flat-square&logo=go)](https://go.dev)
[![License](https://img.shields.io/badge/License-MIT-green.svg?style=flat-square)](LICENSE)
[![Platform](https://img.shields.io/badge/Platform-Windows%20|%20macOS%20|%20Linux%20|%20Android-lightgrey.svg?style=flat-square)](https://github.com/DaiZhouHui/yx-tools/releases)
[![Release](https://img.shields.io/github/v/release/DaiZhouHui/yx-tools?style=flat-square&label=Release)](https://github.com/DaiZhouHui/yx-tools/releases)

> 🚀 一款轻量级、跨平台的 Cloudflare 优选 IP 测速工具，支持命令行和网页双界面，单个二进制文件即可运行。

## ✨ 核心特性

- **🎯 精准测速**：基于 [XIU2/CloudflareSpeedTest](https://github.com/XIU2/CloudflareSpeedTest) 内核，支持延迟 + 下载速度双重测速
- **🌍 全球覆盖**：支持全球 97 个 Cloudflare 数据中心，可按机场码筛选特定地区
- **🔌 反代友好**：原生支持 `IP:端口` 格式，完美适配反代场景
- **📊 双模界面**：同时提供现代化 Web 界面和命令行模式，满足不同使用习惯
- **☁️ 云端同步**：一键上报结果到 [cfnew](https://github.com/byJoey/cfnew) 面板或 GitHub 仓库
- **⏰ 定时任务**：内置 Cron 支持，自动定期执行测速任务
- **📱 移动支持**：提供 Android APK，随时随地测速
- **🐳 容器化**：官方 Docker 镜像，开箱即用

## 📦 快速开始

### 下载安装

从 [Releases](https://github.com/DaiZhouHui/yx-tools/releases) 页面下载对应平台的最新版本：

<details>
<summary><b>🖥️ 桌面端/服务器端</b></summary>

| 平台 | 架构 | 下载文件 |
|------|------|----------|
| Windows | x86_64 (AMD64) | `yx_windows_amd64.zip` |
| Windows | ARM64 | `yx_windows_arm64.zip` |
| macOS | Apple Silicon (M1/M2/M3/M4) | `yx_darwin_arm64.tar.gz` |
| macOS | Intel | `yx_darwin_amd64.tar.gz` |
| Linux | x86_64 (AMD64) | `yx_linux_amd64.tar.gz` |
| Linux | ARM64 (树莓派/甲骨文免费机) | `yx_linux_arm64.tar.gz` |
| Linux | 32-bit | `yx_linux_386.tar.gz` |
| FreeBSD | x86_64 | `yx_freebsd_amd64.tar.gz` |

💡 **如何确认我的架构？**
- **macOS**: 点击左上角  → "关于本机"，查看芯片类型
- **Linux**: 终端运行 `uname -m`，`x86_64` = amd64，`aarch64` = arm64

```bash
# Linux/macOS 用户：解压并赋予执行权限
tar -xzf yx_linux_amd64.tar.gz
chmod +x yx_linux_amd64
./yx_linux_amd64

# 可选：重命名为简短名称
mv yx_linux_amd64 yx
```

```powershell
# Windows 用户：解压后直接双击运行 yx_windows_amd64.exe
# 首次运行如被拦截，请在"系统设置 → 隐私与安全性"中允许运行
```

</details>

<details>
<summary><b>🐳 Docker 部署</b></summary>

**方式一：Docker Compose（推荐）**

```yaml
# docker-compose.yml
version: '3'
services:
  yx-tools:
    image: ghcr.io/dazhouhui/yx-tools:latest
    container_name: yx-tools
    ports:
      - "8080:8080"
    volumes:
      - ./data:/data
    environment:
      - YX_DATA_DIR=/data
    restart: unless-stopped
```

```bash
docker compose up -d
# 访问 http://localhost:8080
```

**方式二：Docker Run**

```bash
docker run -d \
  --name yx-tools \
  -p 8080:8080 \
  -v $(pwd)/data:/data \
  -e YX_DATA_DIR=/data \
  ghcr.io/dazhouhui/yx-tools:latest
```

> 💡 数据持久化在 `./data` 目录，包含测速结果、配置文件等。如需修改存储位置，设置 `YX_DATA_DIR` 环境变量。

</details>

<details>
<summary><b>🔨 从源码编译</b></summary>

```bash
# 前置条件：安装 Go 1.22+
git clone https://github.com/DaiZhouHui/yx-tools.git
cd yx-tools
go build -o yx ./cmd/yx

# 验证安装
./yx --help
```

</details>

<details>
<summary><b>📱 Android 移动端</b></summary>

Android 版本将 Go 引擎通过 [gomobile](https://pkg.go.dev/golang.org/x/mobile) 编译进 APK，使用 WebView 展示内嵌网页界面。

**获取方式：**
- 从 [Releases](https://github.com/DaiZhouHui/yx-tools/releases) 下载最新 APK
- CI 自动构建：推送 `v*` 标签时自动生成并附带 SHA-256 校验和

**自行构建（Windows）：**
```bash
# 双击运行
build-apk.bat

# 或手动执行
cd android
./gradlew assembleRelease
```

详见 [android/](android/) 和 [androidlib/](androidlib/) 目录。

</details>

## 🚀 使用指南

### Web 界面模式

启动本地服务并自动打开浏览器：

```bash
./yx
# 默认监听 127.0.0.1:8080
```

远程访问或自定义端口：

```bash
./yx web -listen 0.0.0.0:8080
```

**界面功能：**
- 🎛️ 左侧参数配置面板
- 📈 右侧实时测速进度和结果展示
- 🔍 地区搜索框（支持中文地名或机场码）
- ⚙️ 高级选项展开/收起

### 命令行模式

**基础测速：**

```bash
# 测速 10 个 IP，速度下限 1MB/s
./yx test -n 10 -sl 1

# 仅测试香港和新加坡节点
./yx test -colo HKG,SIN -n 20

# 启用 IPv6 测速
./yx test -ipv6 -n 15

# 只测延迟，跳过下载测速（更快）
./yx test -nodl -n 50
```

**高级用法：**

```bash
# 自定义测速参数
./yx test \
  -n 30 \           # 测速数量
  -sl 2 \           # 速度下限 2MB/s
  -tl 200 \         # 延迟上限 200ms
  -t 500 \          # 并发线程数
  -dt 15 \          # 单 IP 测速时长 15 秒
  -mt 300 \         # 总时长上限 5 分钟
  -o my_result.csv  # 输出文件名

# 使用自定义 IP 列表（支持 IP:端口 格式）
./yx test -f custom_ips.txt -n 20
```

**结果上报：**

```
# 上报到 API 接口
./yx test -n 10 -upload api -domain your.domain.com -uuid YOUR_UUID -clear

# 推送到 GitHub 仓库
./yx test -n 10 -upload github -repo username/repo -token ghp_xxx -path results/ip_list.txt -clear

# 单独执行上报（使用已有结果）
./yx upload api -domain your.domain.com -uuid YOUR_UUID -limit 20 -clear
```

**生成反代列表：**

```bash
# 从 result.csv 生成 ips_ports.txt
./yx proxy -limit 20
```

### 定时任务

**Linux/macOS 内置 Cron：**

```bash
# 添加定时任务：每 6 小时测速一次并上报
./yx cron -add "test -n 10 -sl 2 -upload api -clear" -at "0 */6 * * *"

# 查看所有定时任务
./yx cron

# 移除所有由本程序添加的任务
./yx cron -remove
```

配置文件位于 `yx-config.json`，首次填写 `-domain` 和 `-uuid` 后可省略。任务日志保存在 `yx-cron.log`。

**Windows 任务计划程序：**

```powershell
# 创建基本任务（图形界面操作）
# 1. 打开"任务计划程序"
# 2. 创建基本任务 → 设置触发器（如每天 8:00）
# 3. 操作：启动程序 → 选择 yx.exe
# 4. 添加参数：test -n 10 -sl 2 -upload api -clear
```

## 📋 完整参数参考

### 测速参数 (`test`)

| 参数 | 说明 | 默认值 |
|------|------|--------|
| `-colo` | 机场代码，逗号分隔（如 `HKG,SIN,NRT`），留空则不限制 | 全部 |
| `-ipv6` | 启用 IPv6 测速 | `false` |
| `-n` | 需要测速的 IP 数量 | `10` |
| `-sl` | 下载速度下限（MB/s） | `1` |
| `-tl` | 平均延迟上限（ms） | `1000` |
| `-t` | 延迟测速并发线程数（路由器建议 ≤200） | `200` |
| `-port` | 测速端口 | `443` |
| `-url` | 自定义测速 URL | 内置默认 |
| `-f` | 自定义 IP 文件路径（每行一个 IP 或 IP:端口） | 自动下载官方列表 |
| `-nodl` | 仅测延迟，跳过下载测速 | `false` |
| `-dt` | 单个 IP 下载测速时长上限（秒） | `10` |
| `-mt` | 整轮测速总时长上限（秒），0 表示无限制 | `0` |
| `-o` | 结果输出文件名 | `result.csv` |

### 上报参数 (`upload` / 配合 `test`)

| 参数 | 说明 | 必填 |
|------|------|------|
| `-upload` | 上报方式：`api`（cfnew）或 `github` | ✅ |
| `-domain` | cfnew Worker 域名 | API 模式必填 |
| `-uuid` | cfnew UUID | API 模式必填 |
| `-repo` | GitHub 仓库（格式：`owner/repo`） | GitHub 模式必填 |
| `-token` | GitHub Personal Access Token | GitHub 模式必填 |
| `-path` | 仓库内文件路径 | `cloudflare_ips.txt` |
| `-limit` | 上报的 IP 数量上限 | `10` |
| `-clear` | 上报前清空已有数据（⚠️ 强烈建议带上） | `false` |

### Web 界面参数 (`web`)

| 参数 | 说明 | 默认值 |
|------|------|--------|
| `-listen` | 监听地址 | `127.0.0.1:8080` |
| `-no-open` | 禁止自动打开浏览器 | `false` |

### 定时任务参数 (`cron`)

| 参数 | 说明 |
|------|------|
| `-add` | 添加定时任务（后面跟具体命令） |
| `-at` | Cron 表达式（如 `"0 */6 * * *"`） |
| `-remove` | 移除所有由本程序添加的任务 |

> 💡 所有命令均可使用 `-h` 或 `--help` 查看详细帮助信息。

## 📁 文件说明

程序运行时会产生以下文件（优先级：当前目录 → 程序目录 → `~/.yx-tools` → 临时目录）：

| 文件名 | 说明 |
|--------|------|
| `result.csv` | 完整测速结果（CSV 格式） |
| `ips_ports.txt` | 反代专用列表（`IP:端口` 格式，一行一个） |
| `yx-config.json` | 配置文件（⚠️ 含敏感信息，勿泄露） |
| `Cloudflare.txt` | 缓存的 IPv4 官方 IP 段 |
| `Cloudflare_ipv6.txt` | 缓存的 IPv6 官方 IP 段 |
| `yx-cron.log` | 定时任务执行日志 |

**固定数据存储位置：**

```bash
# 设置环境变量
export YX_DATA_DIR=/path/to/your/data

# 或在 systemd/Docker 中配置
Environment=YX_DATA_DIR=/opt/yx-tools/data
```

## 🔧 常见问题

<details>
<summary><b>Q: macOS 提示"无法验证开发者"？</b></summary>

前往"系统设置 → 隐私与安全性"，找到被阻止的应用，点击"仍要打开"。或者右键应用选择"打开"。

</details>

<details>
<summary><b>Q: Linux 权限不足？</b></summary>

```bash
chmod +x yx_linux_amd64
# 或使用 sudo 运行（不推荐）
sudo ./yx_linux_amd64
```

</details>

<details>
<summary><b>Q: Docker 容器内无法访问宿主机网络？</b></summary>

确保使用 `-p 8080:8080` 映射端口，并通过 `http://宿主机IP:8080` 访问。

</details>

<details>
<summary><b>Q: 测速结果不理想？</b></summary>

- 增加 `-n` 参数测试更多 IP
- 降低 `-sl` 速度下限
- 提高 `-tl` 延迟上限
- 使用 `-colo` 指定优质节点（如 `HKG,NRT,SIN`）
- 调整 `-t` 并发线程数（过高可能导致路由器过载）

</details>

<details>
<summary><b>Q: 如何备份配置和结果？</b></summary>

```bash
# 备份整个数据目录
tar -czf yx-backup.tar.gz data/

# 或仅备份关键文件
cp result.csv backup/result_$(date +%Y%m%d).csv
cp yx-config.json backup/
```

</details>

## 🤝 相关项目

- **[XIU2/CloudflareSpeedTest](https://github.com/XIU2/CloudflareSpeedTest)** - 测速内核来源（MIT 许可）

## 🙏 致谢

- **测速内核**：感谢 [XIU2](https://github.com/XIU2) 开发的 [CloudflareSpeedTest](https://github.com/XIU2/CloudflareSpeedTest)
- **Android 支持**：特别感谢 [HERO-WPC](https://github.com/HERO-WPC) 贡献 Android 版本（TG: [@hero_wpc](https://t.me/hero_wpc)）
- **社区支持**：感谢所有 Issue 提交者和 PR 贡献者

## 📄 开源协议

本项目采用 [MIT License](LICENSE) 开源协议。

---

<div align="center">

**⭐ 如果这个项目对你有帮助，欢迎 Star 支持一下！**

Made with ❤️ by [DaiZhouHui](https://github.com/DaiZhouHui)

</div>
