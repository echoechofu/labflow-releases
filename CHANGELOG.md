# LabFlow Changelog

## 0.1.7 — 2026-09-26（仅 macOS）

- **仅更新 Apple Silicon macOS 包**：Windows `Setup.exe` 和 MSI 继续维持 0.1.5，本次没有生成或上传 Windows 0.1.7。
- Experiment 支持改名和隐藏；名称同步到日历、Record、Sample 视图及后续导出，隐藏的 Experiment 不再出现在日历、默认实验列表或新建 Task 选项中。
- Record 新增集中式“实验文件”区域，统一显示正文图片、普通附件和检测模块原始数据；支持多选、拖放、复制进度、失败重试、重复检测、打开、另存为及插入正文。
- macOS 版加入 Tauri 应用内更新：启动时发现新版后先确认下载，签名验证通过后再确认安装与重启。
- 更新包使用独立 updater 签名验证；应用继续使用 ad-hoc 签名且未经过 Apple notarization，首次安装或系统重新评估时仍可能出现 Gatekeeper 提示。
- 0.1.6 及更早版本没有 updater，需要手动安装一次 0.1.7；从后续版本开始才可接收应用内更新提醒。

## 0.1.6 — 2026-09-20（仅 macOS）

- **仅更新 Apple Silicon macOS 包**：Windows `Setup.exe`、MSI 和 Windows MCP 状态均维持 0.1.5，未在本次 Release 中重新构建或替换。
- macOS App 内置 `labflow-mcp` 本地 sidecar；安装到“应用程序”后，可直接注册给 Codex、ChatGPT Desktop、WorkBuddy 或其他支持本地 STDIO 的 MCP 客户端。
- 新增公开 MCP 安装与使用说明，说明当前可用的 Experiment、Task、Protocol、Record 工具、权限边界和桌面端仍需完成的操作。
- 日历中修改 Task 名称后，已有 Record 的列表、详情与导出标题会反映当前 Task 名称；冻结的 Record 正文与 Protocol snapshot 保持不变。
- macOS 打包增加 sidecar 存在性、架构与签名验证；发布包继续不包含任何用户 SQLite、附件、导出或工作区备份。

## 0.1.5 — 2026-09-18

- 自建 Protocol 统一为四种 Sample 身份流程：原 Sample 沿用、1→1、1→多和1→0。
- “输入 Sample 类型”改为“适用的输入类型”，支持单类型、多类型或明确不限类型。
- 1→1 和1→多在创建 Record 时逐行登记真实输出 Sample，包含来源输入、类型、自定义名称、处理方式、处理时间和其他说明。
- 输出类型按通用材料分类展开，移除 `OTHER` 兜底和容器类型；新增 `NUCLEI`，保留旧数据兼容。
- 通用目录不能表达时可登记新的通用 Sample 类型；具体解剖部位使用 `TISSUE` 并写入 Sample 名称或其他信息。
- 输出清单支持“复用上一行”；首次创建后可选择是否将类型序列保存为该 Protocol 的默认预填。
- 空白的处理时间不再从父 Sample 元数据自动继承，Record 创建界面支持纵向滚动。
- Desktop 与 MCP 继续共用同一套 Protocol service、validation 和 transaction。

## 0.1.4 — 2026-09-16

- 自建 Protocol 编辑器进一步组件化，内置能力与自定义能力使用统一 schema。
- 支持结构化 Record 字段、多个不同类型的输出 Sample，以及相同条件或按条件分配。
- 条件分配支持孔板和培养皿布局，并可为各位置填写处理条件与方法。
- Record 支持添加图片及其他类型文件附件；原文件保存在 LabFlow 工作区，SQLite 仅保存元数据。
- 合并导出生成低内存 PDF，并将全部附件一起打包为 ZIP。
- 修正父 Task 对应 Record 的输出 Sample 选择，同时保留主动跨分支选择入口。
- 实验流程图支持导出 PNG。

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
