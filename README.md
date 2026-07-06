# Robust OpenWrt Build (ARM64 / X86_64)

这是一个基于 GitHub Actions 的自适应 OpenWrt 固件在线编译工作流。支持 **ARM64 (ARMv8)** 与 **X86_64** 双重架构，集成四大主流源码仓库，并提供自适应依赖冲突处理、旁路由预配置以及开箱即用的中文与时区环境。

---

## 💡 主要特性

- **多架构支持**：支持编译 `X86_64`（物理软路由、虚拟机、PC）以及 `ARM64`（树莓派、晶晨等通用 ARMv8 设备）固件。
- **主流源码切换**：
  - **Official**：官方标准 OpenWrt。
  - **ImmortalWrt**：天灵衍生源，对国内网络环境、无线驱动和中文包适配较好。
  - **LEDE**：Lean 的 LEDE 源码，插件生态全面。
  - **Lienol**：精简改版源码，侧重轻量运行。
- **自适应预装插件**：
  - **LuCI 网页后台**：默认集成且开箱即用，自动配置为**简体中文**界面与**上海（CST-8）时区**。
  - **精品插件支持**：集成 **OpenClash**、**PassWall**、**MosDNS** 转发器（根据所选源码自适应匹配核心与 LuCI 分支）以及 **Argon** 炫酷主题及配置工具。
- **旁路由一键预置**：勾选旁路由配置后，系统首次开机将自动关闭 DHCP、关闭 IPv6 通告、绑定您设定的静态 IP、网关及 DNS，避免局域网内 IP 冲突。
- **自动冲突处理**：
  - 自动清理编译磁盘空间，降低磁盘爆满风险。
  - 编译前自动处理 Dnsmasq 与 Dnsmasq-Full 冲突。
  - 自动检测并擦除 feeds 源中与本地克隆第三方插件包重名的陈旧文件，提高编译通过率。
  - 编译失败时，自动提取并输出最近 100 行报错日志，方便定位问题。

---

## 🛠️ 参数配置 (Workflow Inputs)

| 参数名称 | 描述 | 默认值 | 备选选项 |
| :--- | :--- | :--- | :--- |
| `arch_choice` | **固件架构** | `arm64` | `arm64`, `x86_64` |
| `repo_choice` | **固件源码仓库** | `official` | `official`, `immortalwrt`, `lede`, `lienol` |
| `repo_branch` | **源码分支** *(LEDE/Lienol 自动修正)* | `openwrt-25.12` | 自定义输入（如 `openwrt-23.05`） |
| `feed_choice` | **软件包源 (Feeds)** | `default` | `default`, `kenzok8` (常用特色插件合集源) |
| `firmware_format` | **固件生成格式** | `both` | `both`, `rootfs_only` (LXC 容器), `efi_only` (EFI 镜像) |
| `include_luci` | 是否包含 LuCI 网页后台 | `true` | `true`, `false` |
| `include_openclash` | 预装 OpenClash 插件 | `false` | `true`, `false` |
| `include_passwall` | 预装 PassWall 插件 | `false` | `true`, `false` |
| `include_argon` | 预装 Argon 主题及配置插件 | `true` | `true`, `false` |
| `include_mosdns` | 预装 MosDNS 转发器 | `false` | `true`, `false` |
| `include_passwall_standalone`| 独立预装 PassWall *(不使用第三方Feed时)* | `false` | `true`, `false` |
| `custom_packages` | 额外自定义安装包（空格分隔） | `luci-theme-argon`| 自定义输入 |
| `is_bypass_router` | 是否一键配置为**旁路由**模式 | `false` | `true`, `false` |
| `lan_ip` | LAN 口自定义 IP 地址 | `192.168.1.253` | 自定义输入 |
| `lan_gateway` | 网关 IP 地址 *(旁路由模式下生效)* | `192.168.1.1` | 自定义输入 |
| `lan_dns` | DNS 服务器 *(空格分隔，旁路由模式下生效)* | `223.5.5.5 114.114.114.114` | 自定义输入 |

---

## 🚀 使用方法

### 1. 准备工作
1. 将本仓库分叉（**Fork**）到您自己的 GitHub 账号下。
2. 进入您的 Fork 仓库 `Settings -> Actions -> General`，在 **Workflow permissions** 区域选择 **Read and write permissions** 并保存。

### 2. 启动编译
1. 点击仓库顶部的 **Actions** 标签页。
2. 在左侧列表中选择 **"Robust OpenWrt Build (ARM64 / X86)"** 工作流。
3. 点击右侧的 **Run workflow** 下拉菜单。
4. 根据您的硬件环境和配置需求调整参数，点击 **Run workflow** 按钮启动编译。

### 3. 下载固件
- 编译完成后，工作流会自动创建或更新一个 **GitHub Release**，其 Tag 格式形如 `openwrt-x86-64-xxxx-run_number` 或 `openwrt-arm64-xxxx-run_number`。
- 您可以直接在 Release 页面下载对应的固件文件：
  - `*-rootfs.tar.gz`：适用于 LXC 容器环境部署。
  - `*-combined-efi.img.gz`：适用于物理刷机、KVM 或 VMware 虚拟机环境部署。

---

## ⚠️ 注意事项与免责声明

- **首次编译耗时**：受 Actions 虚拟环境性能限制，首次完整拉取和编译通常需要 2 到 3 小时。
- **网络插件使用**：本工作流提供的网络代理等第三方插件功能仅供网络技术学习、研究以及局域网优化使用，请在遵守当地法律法规的前提下合理配置。
