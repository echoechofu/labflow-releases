# LabFlow Downloads

LabFlow 是一个面向生物医学湿实验的 local-first 实验管理与电子实验记录本（ELN）。本仓库仅用于公开分发 macOS / Windows 安装包、安装说明和版本更新记录，**不包含 LabFlow 源代码**。

## 下载最新版

当前版本：**LabFlow 0.1.1 — macOS / Windows 测试版**

### Windows 10/11 x64

- [下载 Setup.exe（普通用户推荐）](https://github.com/echoechofu/labflow-releases/releases/download/v0.1.1/LabFlow-0.1.1-Windows-x64-Setup.exe)
- [下载 MSI（管理部署）](https://github.com/echoechofu/labflow-releases/releases/download/v0.1.1/LabFlow-0.1.1-Windows-x64.msi)
- [Windows SHA-256 校验文件](https://github.com/echoechofu/labflow-releases/releases/download/v0.1.1/SHA256SUMS-Windows-x64.txt)

### Apple Silicon macOS 12+

- [下载 DMG](https://github.com/echoechofu/labflow-releases/releases/download/v0.1.1/LabFlow-0.1.1-Apple-Silicon.dmg)
- [下载 ZIP](https://github.com/echoechofu/labflow-releases/releases/download/v0.1.1/LabFlow-0.1.1-Apple-Silicon.zip)
- [macOS SHA-256 校验文件](https://github.com/echoechofu/labflow-releases/releases/download/v0.1.1/SHA256SUMS.txt)

[Release 页面](https://github.com/echoechofu/labflow-releases/releases/tag/v0.1.1) 包含完整的更新记录、SHA-256 值和安装说明。当前没有 Intel Mac 安装包。

## Windows：安装

普通用户下载 `Setup.exe` 后直接双击安装即可。本 MVP 暂未配置 Windows Authenticode 代码签名，因此首次安装时可能出现“Windows 已保护你的电脑”。

1. 确认安装包来自本官方 Release。
2. 点击“更多信息”。
3. 确认应用名称为 `LabFlow`，再点击“仍要运行”。
4. 按安装向导完成安装。

学校、医院或企业电脑可能禁止运行未签名程序。请遵守组织安全策略并联系 IT 管理员，不要尝试绕过管理控制。

### 可选：验证下载文件

如希望确认安装包与官方发布版本完全一致，可额外下载 `SHA256SUMS-Windows-x64.txt`，与 `Setup.exe` 放在同一目录后，在 PowerShell 执行：

```powershell
Get-FileHash -Algorithm SHA256 .\LabFlow-0.1.1-Windows-x64-Setup.exe
Get-Content .\SHA256SUMS-Windows-x64.txt
```

PowerShell 输出的 `Hash` 应与校验文件中对应 `Setup.exe` 的值完全一致；不一致时不要运行安装包，请从本仓库 Release 重新下载。跳过此步骤不会影响安装。

## macOS：安装

下载 DMG 或 ZIP 后打开它，将 `LabFlow.app` 拖入“应用程序”文件夹。本测试版尚未经过 Apple Developer ID 签名与公证；若首次启动被 macOS 拦截：

1. 在“应用程序”文件夹中找到 `LabFlow.app`。
2. 按住 Control 点击 App，选择“打开”。
3. 在弹出的确认窗口中再次选择“打开”。

### 可选：验证下载文件

如希望确认安装包与官方发布版本完全一致，可下载 `SHA256SUMS.txt` 并与 DMG 或 ZIP 放在同一目录。以 ZIP 为例，在终端分别执行：

```bash
shasum -a 256 LabFlow-0.1.1-Apple-Silicon.zip
cat SHA256SUMS.txt
```

第一条命令输出的 Hash 应与校验文件中对应 ZIP 的值完全一致。如果下载的是 DMG，将第一条命令的文件名换成 DMG 文件名。跳过此步骤不会影响安装。

## 本地数据

LabFlow 的数据库和附件保存在：

```text
macOS:   ~/Library/Application Support/LabFlow/
Windows: %APPDATA%\LabFlow\
```

更新或重新安装 App 不会主动覆盖该目录。请通过 LabFlow 的“数据管理”功能迁移或备份工作区，不要直接修改数据库文件。

## 许可

LabFlow 使用 [PolyForm Noncommercial License 1.0.0](LICENSE)。允许个人、教学、学术研究和其他非商业用途；商业使用需要另行授权。

版本变化见 [CHANGELOG.md](CHANGELOG.md)。
