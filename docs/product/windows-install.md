# LabFlow Windows 安装与完整性校验

本说明适用于 LabFlow MVP 的 **Windows 10/11 x64 未签名测试版**。安装包由私有源码仓库在 GitHub 托管的 Windows 虚拟机中原生构建，并发布到公开的 [LabFlow Downloads](https://github.com/echoechofu/labflow-releases/releases)。

## 1. 下载哪个文件

普通用户优先下载：

```text
LabFlow-<版本>-Windows-x64-Setup.exe
```

`.msi` 是供学校、实验室或企业 IT 部署的备选格式。两种格式安装的是同一版 LabFlow，不需要重复安装。

## 2. 可选：校验 SHA-256

如希望确认安装包与官方发布文件完全一致，可同时下载 `SHA256SUMS-Windows-x64.txt`，然后在下载目录打开 PowerShell，执行：

```powershell
Get-FileHash -Algorithm SHA256 .\LabFlow-*-Windows-x64-Setup.exe
Get-Content .\SHA256SUMS-Windows-x64.txt
```

PowerShell 输出的 `Hash` 应与校验文件中对应文件的 64 位字符完全一致（大小写可忽略）。不一致时不要运行安装包，请删除后从公开 Release 页重新下载。跳过此步骤不会影响安装。

## 3. 通过 SmartScreen 安装未签名测试版

LabFlow MVP 当前没有 Windows Authenticode 代码签名。因此 Windows 可能显示“Windows 已保护你的电脑”或“未知发布者”，这不代表校验失败。

请确认文件来自 `echoechofu/labflow-releases`。如果执行了 SHA-256 校验，还应确认校验结果匹配，然后按以下步骤继续：

1. 双击 `LabFlow-<版本>-Windows-x64-Setup.exe`。
2. 如出现蓝色 SmartScreen 界面，点击“更多信息”。
3. 确认应用名称为 `LabFlow`，再点击“仍要运行”。
4. 按安装向导完成安装并启动 LabFlow。

如果电脑由学校、医院或企业管理，“仍要运行”可能被管理策略禁用。此时不要尝试绕过组织安全策略，请联系 IT 管理员，或暂时在个人电脑上参与测试。

## 4. WebView2 和首次启动

LabFlow 使用 Microsoft Edge WebView2 显示桌面界面。Windows 10/11 通常已包含该运行时；如未安装，LabFlow 安装程序会在联网状态下下载微软官方 bootstrapper。

## 5. 数据位置与卸载

Windows 用户数据位于：

```text
%APPDATA%\LabFlow\
├── labflow.sqlite
└── files\
```

安装、更新或卸载应用不等于删除实验数据。请不要直接编辑该目录中的 SQLite 和附件；迁移或备份时使用 LabFlow“数据管理”中的工作区备份功能。

## 6. 如何报告问题

请记录 Windows 版本、LabFlow 版本、安装包文件名、SHA-256 校验是否通过，以及出错界面截图。不要公开上传 `labflow.sqlite` 或包含实验数据的备份。
