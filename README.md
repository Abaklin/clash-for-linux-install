# Linux 一键安装 Clash

> 📌 **本仓库为 Fork 版本**
>
> Fork 自上游 [`nelvko/clash-for-linux-install`](https://github.com/nelvko/clash-for-linux-install)（经 [`qiaohaijun123/clash-for-linux-install`](https://github.com/qiaohaijun123/clash-for-linux-install) 转手）。
>
> 与上游差异 / What's different：
>
> | 项目 | 上游 | 本仓库 |
> |---|---|---|
> | mihomo 内核 | `v1.19.2`（2025-02） | **`v1.19.27`**（2026-06） |
> | 支持的协议 | 截止上游打包日 | 新增 **`anytls`**、新版 `hysteria2` / `tuic` / `vless` 等 |
> | 内核变体 | `compatible`（x86_64） | `compatible`（x86_64，保持一致） |
> | 其他源码 | — | **完全保持上游一致，未做任何修改** |
>
> **为什么有这个 fork**：上游打包的 mihomo `v1.19.2` 不识别 `anytls` 协议，订阅含 `anytls` 节点时 `install.sh` 会在配置校验阶段报 `unsupport proxy type: anytls` 并退出。本仓库仅升级内置内核 gz 包 (`resources/zip/mihomo-linux-amd64-compatible-v1.19.27.gz`)，让安装流程能跑通。
>
> **安装方式与上游完全一致**，向下兼容：
>
> ```bash
> git clone --branch main --depth 1 https://gh-proxy.com/https://github.com/Abaklin/clash-for-linux-install.git \
>   && cd clash-for-linux-install \
>   && sudo bash install.sh
> ```
>
> **想自己升级内核（适配未来更新的协议）**：参考 `resources/zip/` 下的 gz 命名格式，从 <https://github.com/MetaCubeX/mihomo/releases/latest> 下载对应 `mihomo-linux-amd64-compatible-*.gz`，替换后重跑 `install.sh` 即可。详细步骤见本 README 末尾的【内核升级】小节。

---

![GitHub License](https://img.shields.io/github/license/nelvko/clash-for-linux-install)
![GitHub top language](https://img.shields.io/github/languages/top/nelvko/clash-for-linux-install)
![GitHub Repo stars](https://img.shields.io/github/stars/nelvko/clash-for-linux-install)

![preview](resources/preview.png)

- 默认安装 `mihomo` 内核，[可选安装](https://github.com/nelvko/clash-for-linux-install/wiki/FAQ#%E5%AE%89%E8%A3%85-clash-%E5%86%85%E6%A0%B8) `clash`。
- 自动使用 [subconverter](https://github.com/tindy2013/subconverter) 进行本地订阅转换。
- 多架构支持，适配主流 `Linux` 发行版：`CentOS 7.6`、`Debian 12`、`Ubuntu 24.04.1 LTS`。

## 快速开始

### 环境要求

- 用户权限：`root`、`sudo`。（无权限可参考：[#91](https://github.com/nelvko/clash-for-linux-install/issues/91)）
- `shell` 支持：`bash`、`zsh`。

### 一键安装

下述命令适用于 `x86_64` 架构，其他架构请戳：[一键安装-多架构](https://github.com/nelvko/clash-for-linux-install/wiki#%E4%B8%80%E9%94%AE%E5%AE%89%E8%A3%85-%E5%A4%9A%E6%9E%B6%E6%9E%84)

```bash
git clone --branch master --depth 1 https://gh-proxy.com/https://github.com/nelvko/clash-for-linux-install.git \
  && cd clash-for-linux-install \
  && sudo bash install.sh
```

> 如遇问题，请在查阅[常见问题](https://github.com/nelvko/clash-for-linux-install/wiki/FAQ)及 [issue](https://github.com/nelvko/clash-for-linux-install/issues?q=is%3Aissue) 未果后进行反馈。

- 上述克隆命令使用了[加速前缀](https://gh-proxy.com/)，如失效请更换其他[可用链接](https://ghproxy.link/)。
- 默认通过远程订阅获取配置进行安装，本地配置安装详见：[#39](https://github.com/nelvko/clash-for-linux-install/issues/39)
- 没有订阅？[click me](https://次元.net/auth/register?code=oUbI)

### 命令一览

执行 `clash` 列出开箱即用的快捷命令。

> 兼容多种命令风格

```bash
$ clash
Usage:
    clash     COMMAND [OPTION]
    mihomo    COMMAND [OPTION]
    clashctl  COMMAND [OPTION]
    mihomoctl COMMAND [OPTION]

Commands:
    on                   开启代理
    off                  关闭代理
    ui                   面板地址
    status               内核状况
    tun      [on|off]    Tun 模式
    mixin    [-e|-r]     Mixin 配置
    secret   [SECRET]    Web 密钥
    update   [auto|log]  更新订阅
```

### 优雅启停

```bash
$ clashoff
😼 已关闭代理环境

$ clashon
😼 已开启代理环境
```

<details>

<summary>原理</summary>

- 使用 `systemctl` 控制 `clash` 启停，并调整代理环境变量的值（http_proxy 等）。应用程序在发起网络请求时，会通过其指定的代理地址转发流量，不调整会造成：关闭代理但未卸载代理变量导致仍转发请求、开启代理后未设置代理地址导致请求不转发。
- `clashon` 等命令封装了上述流程。

</details>

### Web 控制台

```bash
$ clashui
╔═══════════════════════════════════════════════╗
║                😼 Web 控制台                  ║
║═══════════════════════════════════════════════║
║                                               ║
║     🔓 注意放行端口：9090                      ║
║     🏠 内网：http://192.168.0.1:9090/ui       ║
║     🌏 公网：http://255.255.255.255:9090/ui   ║
║     ☁️ 公共：http://board.zash.run.place      ║
║                                               ║
╚═══════════════════════════════════════════════╝

$ clashsecret 666
😼 密钥更新成功，已重启生效

$ clashsecret
😼 当前密钥：666
```

- 通过浏览器打开 Web 控制台，实现可视化操作：切换节点、查看日志等。
- 控制台密钥默认为空，若暴露到公网使用建议更新密钥。

### 更新订阅

```bash
$ clashupdate https://example.com
👌 正在下载：原配置已备份...
🍃 下载成功：内核验证配置...
🍃 订阅更新成功

$ clashupdate auto [url]
😼 已设置定时更新订阅

$ clashupdate log
✅ [2025-02-23 22:45:23] 订阅更新成功：https://example.com
```

- `clashupdate` 会记住上次更新成功的订阅链接，后续执行无需再指定。
- 可通过 `crontab -e` 修改定时更新频率及订阅链接。
- 通过配置文件进行更新：[pr#24](https://github.com/nelvko/clash-for-linux-install/pull/24#issuecomment-2565054701)

### `Tun` 模式

```bash
$ clashtun
😾 Tun 状态：关闭

$ clashtun on
😼 Tun 模式已开启
```

- 作用：实现本机及 `Docker` 等容器的所有流量路由到 `clash` 代理、DNS 劫持等。
- 原理：[clash-verge-rev](https://www.clashverge.dev/guide/term.html#tun)、 [clash.wiki](https://clash.wiki/premium/tun-device.html)。
- 注意事项：[#100](https://github.com/nelvko/clash-for-linux-install/issues/100#issuecomment-2782680205)

### `Mixin` 配置

```bash
$ clashmixin
😼 less 查看 mixin 配置

$ clashmixin -e
😼 vim 编辑 mixin 配置

$ clashmixin -r
😼 less 查看 运行时 配置
```

- 将自定义配置写在 `Mixin` 而不是原配置中，可避免更新订阅后丢失自定义配置。
- 运行时配置是订阅配置和 `Mixin` 配置的并集。
- 相同配置项优先级：`Mixin` 配置 > 订阅配置。

### 卸载

```bash
sudo bash uninstall.sh
```

## 常见问题

[wiki](https://github.com/nelvko/clash-for-linux-install/wiki/FAQ)

## 引用

- [Clash 知识库](https://clash.wiki/)
- [Clash 家族下载](https://www.clash.la/releases/)
- [Clash Premium 2023.08.17](https://downloads.clash.wiki/ClashPremium/)
- [mihomo v1.19.2](https://github.com/MetaCubeX/mihomo)
- [subconverter v0.9.0：本地订阅转换](https://github.com/tindy2013/subconverter)
- [yacd v0.3.8：Web 控制台](https://github.com/haishanh/yacd)
- [yq v4.45.1：处理 yaml](https://github.com/mikefarah/yq)

## Star History

<a href="https://www.star-history.com/#nelvko/clash-for-linux-install&Date">
 <picture>
   <source media="(prefers-color-scheme: dark)" srcset="https://api.star-history.com/svg?repos=nelvko/clash-for-linux-install&type=Date&theme=dark" />
   <source media="(prefers-color-scheme: light)" srcset="https://api.star-history.com/svg?repos=nelvko/clash-for-linux-install&type=Date" />
   <img alt="Star History Chart" src="https://api.star-history.com/svg?repos=nelvko/clash-for-linux-install&type=Date" />
 </picture>
</a>

## Thanks

[@鑫哥](https://github.com/TrackRay)

## 特别声明

1. 编写本项目主要目的为学习和研究 `Shell` 编程，不得将本项目中任何内容用于违反国家/地区/组织等的法律法规或相关规定的其他用途。
2. 本项目保留随时对免责声明进行补充或更改的权利，直接或间接使用本项目内容的个人或组织，视为接受本项目的特别声明。

---

## 🔧 内核升级（Fork 特有章节）

当上游 `mihomo` 内核版本跟不上你的订阅里的新协议（典型现象：`install.sh` 在 `🍃 下载成功：内核验证配置...` 之后报 `proxy 0: unsupport proxy type: <协议名>`，例如 `anytls`、`hysteria2`），按下面步骤升级内置内核即可。

### 步骤

```bash
# 1. 查最新版本号（从官方 releases）
curl -sSL https://api.github.com/repos/MetaCubeX/mihomo/releases/latest \
  | grep '"tag_name"' | head -1
# -> "tag_name": "v1.19.27"

# 2. 下载对应变体的 gz（国内用 gh-proxy 加速）
VERSION=v1.19.27
curl -fL --max-time 120 -o /tmp/mihomo-new.gz \
  "https://gh-proxy.com/https://github.com/MetaCubeX/mihomo/releases/download/${VERSION}/mihomo-linux-amd64-compatible-${VERSION}.gz"

# 3. 验证下载的 gz 可解压且版本正确
gzip -dc /tmp/mihomo-new.gz > /tmp/mihomo-test && chmod +x /tmp/mihomo-test
/tmp/mihomo-test -v
# 应输出: Mihomo Meta v1.19.27 linux amd64 ...

# 4. 在项目根目录替换 gz（注意：旧 gz 要先删，避免 glob 同时匹配两个文件）
cd /path/to/clash-for-linux-install
rm -f resources/zip/mihomo-*.gz
cp /tmp/mihomo-new.gz resources/zip/mihomo-linux-amd64-compatible-${VERSION}.gz

# 5. 如果之前有半安装残留，清掉再重装
sudo bash uninstall.sh 2>/dev/null   # 若 /opt/clash 不存在会报错，无所谓
rm -f resources/bin/mihomo resources/bin/yq resources/bin/yq.1 resources/config.yaml
rm -rf resources/bin/subconverter

# 6. 重跑安装
sudo bash install.sh
```

### 内核变体说明

| 文件名包含 | 适用 CPU |
|---|---|
| `compatible` | 任何 x86_64（默认，推荐） |
| `v1`（不含 compatible） | 需 SSE4.2 |
| `v3` | 需 AVX2，性能略好 |
| `arm64` | ARM 设备（树莓派等） |

不确定 CPU 能力时选 `compatible`，性能损失对代理场景可忽略。

### 验证内核已生效

```bash
/opt/clash/bin/mihomo -v
# 应该是新版本号，不是 v1.19.2

# 实际跑流量
curl -x http://127.0.0.1:7890 -I https://www.google.com
# 应返回 HTTP 200/301/302
```

---

## 致谢

- 上游原作者：[@nelvko](https://github.com/nelvko) — 感谢提供原始项目
- 中间 fork：[@qiaohaijun123](https://github.com/qiaohaijun123)
- 内核维护：[@MetaCubeX/mihomo](https://github.com/MetaCubeX/mihomo)

## 特别声明

本 fork 仅为解决个人订阅中 `anytls` 协议无法识别的问题而创建，**只替换了内置内核的 gz 包**，其他所有源码与上游完全一致。本项目的一切声明、责任、许可证均沿用上游 [`nelvko/clash-for-linux-install`](https://github.com/nelvko/clash-for-linux-install) 的版本。

使用者自行承担因使用本项目导致的任何后果。本项目不得用于违反所在地区法律法规的用途。
