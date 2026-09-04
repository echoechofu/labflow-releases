# LabFlow Changelog

## 0.1.3 — 2026-09-04

- 自建 Protocol 的“产生多个 Sample”支持相同条件与按条件分组两种模式。
- 条件分配可记录条件、剂量、处理时间和数量，并可选顺序映射孔板位置；Sample 类型与孔位互不绑定。
- 重做 Protocol 创建器中多 Sample 关系的说明、选项和预览。
- 内置“细胞加刺激”支持多个同类型输入，并禁止在同一条 Record 中混选 CELL、PLATE、DISH、WELL。
- 刺激 Record 只显示当前输入类型对应的设置，不再生成空白或无关正文段落。
- CELL、DISH、WELL 刺激后以原 Sample 身份登记为输出；PLATE 按刺激分组生成 WELL。

## 0.1.2 — 2026-08-31

- 所有 Record 正文支持插入 PNG、JPEG、WebP 和 TIFF 图片；原图保存在工作区，SQLite 只保存元数据。
- 大图/TIFF 生成最长边 1448 px 的预览；预览按视口加载，离开视口释放画布。
- 新增“低内存 PDF”，逐页写盘，支持进度和取消，失败时清理临时文件；文字是图像，不可选中、搜索或复制。
- 系统打印限制最多 8 个正文图片引用；更多图片请使用低内存导出或拆分选择。
- 工作区备份同时包含原图、预览和附件元数据。升级前建议导出完整备份。
- macOS 和 Windows 延续 MVP 无 Developer ID/Authenticode 签名策略，提供 SHA-256 校验文件及安装说明。

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
