# LabFlow Changelog

## 0.1.1 — 2026-08-29

- 新增 Windows 10/11 x64 原生 NSIS `Setup.exe` 和 MSI 安装包。
- Windows 发布链路在 GitHub 托管的 Windows runner 上运行测试、构建、未签名状态检查并生成 SHA-256。
- 新增 Windows SmartScreen 安装与文件完整性校验说明。
- 新增共享 MCP Agent Interface。Task、Experiment、Protocol 和 Record 工具复用 Desktop 的同一套 domain/service、validation 与 SQLite transaction。
- Protocol 支持通过 Desktop 和 MCP 删除用户自建模板；已保存 Record 的完整版本快照继续保留。
- 删除首页侧边栏中硬编码的“本地数据 8%”占位显示。
- 保留现有 local-first 数据模型和工作区备份能力。

## 0.1.0 — 2026-08-27

- 首个 Apple Silicon macOS 测试版。
- 支持 Experiment、日历 Task、Protocol、Record、Sample、Terminal Assay 与本地工作区备份等核心流程。
