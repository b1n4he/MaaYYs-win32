<div align="center">
  <img src="assets/logo/logo.png" alt="MaaYYs Logo" width="256" height="256" />
  <h1>MaaYYs-win32</h1>
  <p>基于 <a href="https://github.com/MaaXYZ/MaaFramework" target="_blank">MaaFramework</a> 与 <a href="https://github.com/MistEO/MXU" target="_blank">MXU</a> 的阴阳师 <b>Windows 官方桌面端</b>自动化资源包</p>
  <p>
    <a href="https://github.com/b1n4he/MaaYYs-win32/actions/workflows/build-release.yml" target="_blank">
      <img src="https://img.shields.io/github/actions/workflow/status/b1n4he/MaaYYs-win32/build-release.yml?branch=main&label=Build" alt="Build Status" />
    </a>
    <img src="https://img.shields.io/badge/Go-1.24-00ADD8?logo=go&logoColor=white" alt="Go 1.24" />
    <img src="https://img.shields.io/badge/License-MIT-green" alt="MIT License" />
  </p>
</div>

---

## 简介

MaaYYs-win32 是 [MaaYYs](https://github.com/TanyaShue/MaaYYs) 的一个分支，**仅面向《阴阳师》Windows 官方桌面端**（即「阴阳师-MuMu模拟器专版」，本质是 MuMu 引擎封装的官方 PC 客户端）。

与上游不同的是，本项目**不依赖模拟器 / ADB / macOS PlayCover**，而是通过 **Win32 窗口控制**直接操作桌面端：

- **截图**：`PrintWindow`（带 `PW_RENDERFULLCONTENT`），能抓到硬件加速渲染的实时画面；
- **点击 / 滑动**：`SendMessage` / `PostMessage` 后台窗口消息，不抢占鼠标焦点；
- **支持 IOS 区登录**：桌面端登录时可选 IOS 区服务器账号；
- 识别逻辑与素材沿用上游（1280×720 基准），与手机 / 模拟器 UI 完全一致。

> 本项目仅用于学习与交流。自动化行为可能受到游戏版本、桌面端更新、庭院皮肤等因素影响，请自行承担使用风险。

## 上游

- MaaYYs 资源 / 任务 / agent：<https://github.com/TanyaShue/MaaYYs>
- MXU 图形界面：<https://github.com/TanyaShue/MXU>（原始上游 <https://github.com/MistEO/MXU>）
- MaaFramework 运行库：<https://github.com/MaaXYZ/MaaFramework>

## 快速开始

### 方式一：直接用 Release 全量包（推荐）

1. 前往本仓库的 [Releases](../../releases) 下载 `MaaYYs-win32-win-x86_64-v*-MXU.zip`。
2. 解压，双击 `mxu.exe`。
3. 添加设备时选择 **「桌面版（Windows）」**（而不是安卓设备），确认能显示游戏画面截图。
4. 选择资源与任务，通常从庭院界面启动。

### 方式二：本地编译运行

1. 下载最新 Release 的 MXU 全量包并解压（提供 `mxu.exe` 与 `maafw/` 运行库）。
2. 编译 agent 并放入 `agent/` 目录：

   ```powershell
   cd agent
   go mod download
   go build -o agent.exe .
   ```

   编译产物 `agent.exe` 必须位于仓库根目录的 `agent/agent.exe`（`interface.json` 里 `child_exec` 写死了 `.\agent\agent.exe`）。
3. 启动根目录的 `mxu.exe`，添加设备选 **「桌面版（Windows）」** 进行验证。

## 支持的资源

`interface.json` 内置以下资源配置（与上游一致）：

- 官服（雷电 / MuMu 下载的选这个）
- 官服2、官服3（TapTap 下载的选这个）
- B 站服、华为渠道服、应用宝渠道服、OPPO 渠道服、VIVO 渠道服

## 主要任务

- 启停与通用：打开游戏、关闭游戏、日常奖励领取、网易大神签到、自动购物、式神图鉴分享。
- 日常与养成：式神委派、寮三十捐材料、结界奖励领取、投喂宠物、肝绘卷。
- 副本与挑战：自动御魂、自动御灵、自动业原火、自动秘闻、自动麒麟、困28、组队副本、英杰试炼、契灵探查。
- PVP / 突破 / 活动：结界突破、自动寮突破、自动道馆突破、自动斗技、自动地鬼、自动逢魔、自动阴界之门、自动悬赏、活动爬塔、限时活动。

任务能力以仓库中的 `tasks/*.json` 与 `resource_pack/*/pipeline` 为准。部分任务需要填写队伍预设、挑战次数等选项，建议尽量避免在队伍预设名称中使用复杂符号和空格，降低 OCR 识别失败概率。

## 常见问题

### 找不到桌面端窗口？

确认「阴阳师」官方桌面端已启动。本项目通过窗口标题 `阴阳师-MuMu模拟器专版`、窗口类名 `Win32Window` 定位窗口；若桌面端窗口标题/类名随版本变化，需同步修改 `interface.json` 中 Win32 控制器的 `window_regex` / `class_regex`。

### 连接上了但点击没反应？

桌面端后台点击用的是 `SendMessage`。若游戏不响应，把 `interface.json` 里 Win32 控制器的 `mouse` / `keyboard` 改成 `PostMessage` 或 `SendMessageWithWindowPos` 等值（见 MaaFramework「控制方式」文档）再试。

### 为什么任务卡在某个界面？

常见原因：没有从任务预期界面启动（普通任务通常需在庭院开始）；游戏更新后按钮 / 图标变化；队伍预设名称 OCR 识别失败。请尽量带上截图、任务名与日志反馈。

## 开发说明

### 编译 agent

```powershell
cd agent
go mod download
go build -o agent.exe .
```

改 `agent/**/*.go` 后必须重新编译并重启 MXU。改 `tasks/*.json`、`resource_pack/**`、`interface.json` 则无需编译，在 MXU 里重新加载 / 重启任务即可。

### 本地运行完整流程

推荐目录形态（源码 + 运行库）：

```text
MaaYYs-win32/
├── mxu.exe            # 来自 Release 全量包
├── maafw/             # MaaFramework 运行库，来自 Release 全量包
├── agent/
│   ├── agent.exe      # 本地编译出的 agent
│   └── *.go           # agent 源码
├── assets/  resource_pack/  tasks/  interface.json  README.md
```

## CI 与自动打包 Release

本仓库的 `.github/workflows/build-release.yml` 会在以下情况自动打包：

- 分支 push / PR：构建 Windows 测试包（不打 tag 不发 release）；
- 推送形如 `v1.2.3`、`v1.2.3-beta.1` 的 tag：触发正式发布。

CI 会编译 Go agent、下载固定版本 MaaFramework 与 MXU，并打包出 Windows x64 / ARM64 的 `MaaYYs-win32-win-*-MXU.zip` 发布到 Releases。

## 贡献

欢迎提交任务、素材、识别修复、文档和 CI 改进。提交 PR 前请确认：JSON 可正常解析、agent 能通过 `go build`、README 与实际行为一致。

## 鸣谢

感谢 [MaaYYs](https://github.com/TanyaShue/MaaYYs)、[MXU](https://github.com/MistEO/MXU)、[MaaFramework](https://github.com/MaaXYZ/MaaFramework) 以及所有提供素材、反馈和代码贡献的朋友。

## 许可

本项目使用 [MIT License](LICENSE) 发布。
