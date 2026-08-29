# LabFlow Downloads

LabFlow 是一个面向生物医学湿实验的 local-first 实验管理与电子实验记录本（ELN）。本仓库仅用于公开分发 macOS 安装包、安装说明和版本更新记录，**不包含 LabFlow 源代码**。

## 下载最新版

当前版本：**LabFlow 0.1.1 — Apple Silicon 测试版**

- [下载 DMG](https://github.com/echoechofu/labflow-releases/releases/download/v0.1.1/LabFlow-0.1.1-Apple-Silicon.dmg)
- [下载 ZIP](https://github.com/echoechofu/labflow-releases/releases/download/v0.1.1/LabFlow-0.1.1-Apple-Silicon.zip)
- [SHA-256 校验文件](https://github.com/echoechofu/labflow-releases/releases/download/v0.1.1/SHA256SUMS.txt)
- [查看全部版本](https://github.com/echoechofu/labflow-releases/releases)

## 系统要求

- Apple Silicon Mac（M1/M2/M3/M4 等）
- macOS 12 或更高版本

当前没有 Intel Mac 安装包。

## 安装

### DMG

1. 下载并打开 DMG。
2. 将 `LabFlow.app` 拖入“应用程序”文件夹。
3. 从“应用程序”启动 LabFlow。

### ZIP

1. 下载并解压 ZIP。
2. 将 `LabFlow.app` 拖入“应用程序”文件夹。
3. 从“应用程序”启动 LabFlow。

## 首次启动提示

当前测试版尚未经过 Apple Developer ID 签名与公证。如果 macOS 拦截首次启动：

1. 在“应用程序”文件夹中找到 `LabFlow.app`。
2. 按住 Control 点击 App，选择“打开”。
3. 在弹出的确认窗口中再次选择“打开”。

## 本地数据

LabFlow 的数据库和附件保存在：

```text
~/Library/Application Support/LabFlow/
├── labflow.sqlite
└── files/
```

更新或重新安装 App 不会覆盖这个目录。请通过 LabFlow 的“数据管理”功能迁移或备份工作区，不要直接修改数据库文件。

## 校验下载文件

```bash
shasum -a 256 -c SHA256SUMS.txt
```

## 许可

LabFlow 使用 [PolyForm Noncommercial License 1.0.0](LICENSE)。允许个人、教学、学术研究和其他非商业用途；商业使用需要另行授权。

版本变化见 [CHANGELOG.md](CHANGELOG.md)。

