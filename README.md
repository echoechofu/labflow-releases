# LabFlow Downloads

LabFlow 是一个面向生物医学湿实验的 local-first 实验管理与电子实验记录本（ELN）。本仓库仅用于公开分发 macOS / Windows 安装包、安装说明和版本更新记录，**不包含 LabFlow 源代码或任何用户数据**。

## 下载最新版

当前 macOS 版本：**LabFlow 0.1.7 — Experiment 管理、实验文件与应用内更新**

**本次只更新 Apple Silicon macOS 安装包。Windows 安装包没有更新，仍为 0.1.5。** Windows 用户请继续从 v0.1.5 页面下载 `Setup.exe` 或 MSI；不要把 macOS 的 App 或 MCP 文件复制到 Windows 使用。

本次更新允许修改 Experiment 名称并在日历、Record、Sample 和导出中同步显示；Experiment 也可隐藏，隐藏后不会出现在日历、默认实验列表或新建 Task 选项中，数据不会删除。

Record 新增“实验文件”区域，可集中查看正文图片、普通附件和检测模块原始数据，支持多选、拖放、复制进度、重复检测、打开、另存为及插入正文。

macOS 0.1.7 首次加入应用内更新组件。以后发现新版时会先询问是否下载，签名验证通过后再次询问是否安装并重启。0.1.6 及更早版本需要手动安装一次 0.1.7。

升级前请先通过旧版“数据管理”导出完整工作区备份，再退出旧版并安装新版。历史 Record 保存既有 Protocol snapshot，不会被新版模板改写。

### Windows 10/11 x64（仍为 0.1.5，未更新）

- [下载 Setup.exe（普通用户推荐）](https://github.com/echoechofu/labflow-releases/releases/download/v0.1.5/LabFlow-0.1.5-Windows-x64-Setup.exe)
- [下载 MSI（管理部署）](https://github.com/echoechofu/labflow-releases/releases/download/v0.1.5/LabFlow-0.1.5-Windows-x64.msi)
- [Windows SHA-256 校验文件](https://github.com/echoechofu/labflow-releases/releases/download/v0.1.5/SHA256SUMS-Windows-x64.txt)

### Apple Silicon macOS 12+（0.1.7）

- [下载 DMG](https://github.com/echoechofu/labflow-releases/releases/download/v0.1.7/LabFlow-0.1.7-Apple-Silicon.dmg)
- [下载 ZIP](https://github.com/echoechofu/labflow-releases/releases/download/v0.1.7/LabFlow-0.1.7-Apple-Silicon.zip)
- [macOS SHA-256 校验文件](https://github.com/echoechofu/labflow-releases/releases/download/v0.1.7/SHA256SUMS-macOS-aarch64.txt)

[打开 v0.1.7 Release 下载页面](https://github.com/echoechofu/labflow-releases/releases/tag/v0.1.7)，可查看 macOS 安装包、更新记录和安装说明。当前没有 Intel Mac 安装包。

## Windows：安装

普通用户下载 `Setup.exe` 后直接双击安装即可。本 MVP 暂未配置 Windows Authenticode 代码签名，因此首次安装时可能出现“Windows 已保护你的电脑”。

1. 确认安装包来自本官方 Release。
2. 点击“更多信息”。
3. 确认应用名称为 `LabFlow`，再点击“仍要运行”。
4. 按安装向导完成安装。

学校、医院或企业电脑可能禁止运行未签名程序。请遵守组织安全策略并联系 IT 管理员，不要尝试绕过管理控制。

### 可选：验证下载文件

如希望确认安装包与官方发布版本完全一致，可额外下载 `SHA256SUMS-Windows-x64.txt`，与 `Setup.exe` 放在同一目录后，在 PowerShell 分别执行：

```powershell
Get-FileHash -Algorithm SHA256 .\LabFlow-0.1.5-Windows-x64-Setup.exe
Get-Content .\SHA256SUMS-Windows-x64.txt
```

PowerShell 输出的 `Hash` 应与校验文件中对应 `Setup.exe` 的值完全一致；不一致时不要运行安装包，请从本仓库 Release 重新下载。跳过此步骤不会影响安装。

## macOS：安装

下载 DMG 或 ZIP 后打开它，将 `LabFlow.app` 拖入“应用程序”文件夹。本测试版使用 ad-hoc 本地签名，尚未经过 Apple Developer ID 签名与公证。请先尝试打开一次；如果 macOS 拦截：

1. 打开“系统设置”→“隐私与安全性”。
2. 滚动到“安全性”区域，找到 `LabFlow was blocked to protect your Mac`。
3. 点击右侧“仍要打开”（Open Anyway）。
4. 在弹出的确认窗口中点击“打开”。

### 可选：验证下载文件

如希望确认安装包与官方发布版本完全一致，可下载 `SHA256SUMS.txt` 并与 DMG 或 ZIP 放在同一目录。以 ZIP 为例，在终端分别执行：

```bash
shasum -a 256 LabFlow-0.1.7-Apple-Silicon.zip
cat SHA256SUMS-macOS-aarch64.txt
```

第一条命令输出的 Hash 应与校验文件中对应 ZIP 的值完全一致。如果下载的是 DMG，将第一条命令的文件名换成 DMG 文件名。跳过此步骤不会影响安装。

## 本地数据

LabFlow 的数据库和附件保存在：

```text
macOS:   ~/Library/Application Support/LabFlow/
Windows: %APPDATA%\LabFlow\
```

更新或重新安装 App 不会主动覆盖该目录。请通过 LabFlow 的“数据管理”功能迁移或备份工作区，不要直接修改数据库文件。

## 使用文档

- [LabFlow 0.1.5 用户手册](docs/product/user-guide.md)
- [核心对象、Record 与 Sample Flow 使用指南](docs/product/core-objects-and-sample-flow-guide.md)
- [MCP 安装与使用指南（macOS 0.1.7 内置 sidecar）](docs/product/mcp-user-guide.md)

详细指南解释 Experiment（Project）、Task、Protocol、Record 和 Sample 的关系，并覆盖 Record 创建流程、输入 Sample 来源、四种 Sample Flow、消耗与谱系、Protocol 字段设置，以及动物取材到 RNA、cDNA、qPCR、Protein 和 WB 的完整示例。

## 许可

LabFlow 使用 [PolyForm Noncommercial License 1.0.0](LICENSE)。允许个人、教学、学术研究和其他非商业用途；商业使用需要另行授权。

版本变化见 [CHANGELOG.md](CHANGELOG.md)。
