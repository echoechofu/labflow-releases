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

## Windows：校验后安装

本 MVP 暂未配置 Windows Authenticode 代码签名。安装前请将 `Setup.exe` 和 `SHA256SUMS-Windows-x64.txt` 放在同一目录，在 PowerShell 执行：

```powershell
Get-FileHash -Algorithm SHA256 .\LabFlow-0.1.1-Windows-x64-Setup.exe
Get-Content .\SHA256SUMS-Windows-x64.txt
```

两处 Hash 必须完全一致。不一致时不要运行安装包，请从本仓库 Release 重新下载。

如果 Windows 显示“Windows 已保护你的电脑”：

1. 确认 SHA-256 已匹配，且文件来自本官方 Release。
2. 点击“更多信息”。
3. 确认应用名称为 `LabFlow`。
4. 点击“仍要运行”，按向导完成安装。

学校、医院或企业电脑可能禁止运行未签名程序。请遵守组织安全策略并联系 IT 管理员，不要尝试绕过管理控制。

## macOS：首次启动

当前 macOS 测试版尚未经过 Apple Developer ID 签名与公证。如果系统拦截首次启动：

1. 在“应用程序”文件夹中找到 `LabFlow.app`。
2. 按住 Control 点击 App，选择“打开”。
3. 在弹出的确认窗口中再次选择“打开”。

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
