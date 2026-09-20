# LabFlow MCP 安装与使用指南（macOS 0.1.6）

LabFlow MCP 是运行在你的 Mac 上的本地 Agent 接口。它让 Codex、ChatGPT 桌面应用、WorkBuddy 或其他支持本地 STDIO MCP 的客户端调用 LabFlow 的既有领域服务；Agent 不会直接读取或修改 LabFlow 的 SQLite 数据库。

```text
Agent client → LabFlow MCP → shared domain service → validation + transaction → SQLite
```

## 1. 适用版本与边界

本指南适用于 **Apple Silicon macOS 12+ 的 LabFlow 0.1.6**。0.1.6 的 `LabFlow.app` 内置 `labflow-mcp`，无需下载源码或单独安装 MCP 二进制。

本次没有更新 Windows 安装包；Windows 公开版本仍为 0.1.5，尚未随 App 提供 MCP。不要将 macOS 二进制复制到 Windows。

MCP 目前支持：

| 模块 | 可做的事 |
| --- | --- |
| Experiment | 查询、创建或修改；受保护删除 |
| Task / Calendar | 查询、创建、改期、修改状态、设置多个上级 Task；受保护删除 |
| Protocol | 查询内置或自建 Protocol、创建自建 Protocol、保存新版本、删除自建 Protocol |
| Record | 查询列表和详情、修改正文、受保护删除 |

MCP 当前**不能**创建 Record、选择真实输入 Sample、填写输出 Sample、查看完整 Sample 谱系或操作 Terminal Assay。上述操作请在 LabFlow Desktop 中完成。

## 2. 安装 LabFlow

从本仓库的 [v0.1.6 Release](https://github.com/echoechofu/labflow-releases/releases/tag/v0.1.6) 下载 DMG 或 ZIP，把 `LabFlow.app` 放入“应用程序”文件夹，并至少启动一次。

当前测试版未经过 Apple Developer ID 签名与公证。若 macOS 拦截，前往“系统设置 → 隐私与安全性”，在“安全性”区域点击 LabFlow 旁的“仍要打开”（Open Anyway）。这与 MCP 无关；不要删除 `~/Library/Application Support/LabFlow/`，其中保存数据库与附件。

## 3. 在 Codex 中添加 LabFlow

打开终端，执行：

```bash
codex mcp add labflow -- /Applications/LabFlow.app/Contents/MacOS/labflow-mcp
codex mcp get labflow
```

重启 Codex。在 Codex 中输入 `/mcp`，确认 `labflow` 显示为已连接。以后用新版覆盖安装同一个 `/Applications/LabFlow.app` 时，通常不需要重新注册。

如果你的 Codex 没有命令行工具，可在图形化 MCP 设置中添加一个 `STDIO` server：名称填 `labflow`，Command 填：

```text
/Applications/LabFlow.app/Contents/MacOS/labflow-mcp
```

Args 保持为空。

## 4. ChatGPT Desktop、WorkBuddy 与其他客户端

支持本机 STDIO MCP 的客户端都可使用同一 Command 路径。添加服务器时：

1. 名称填 `labflow`。
2. 类型选择 `STDIO`。
3. Command 填 `/Applications/LabFlow.app/Contents/MacOS/labflow-mcp`。
4. Args 留空，保存后重启客户端。

ChatGPT 网页版不能直接运行 Mac 上的本地 STDIO server；LabFlow 当前也没有远程 MCP 或 LabFlow 云端服务。

## 5. 怎么使用

先从只读请求开始，例如：

```text
列出 LabFlow 中最近的 Task，不要修改任何内容。
```

需要写入时，让 Agent 在执行前复述目标对象和字段。例如：

```text
在 Experiment「炎症模型」中创建一个明天上午 10 点的 Task，标题为「收集上清」。先告诉我将要创建的内容，等我确认后再写入。
```

首次使用空工作区时，先在 Desktop 创建 Experiment，或让 Agent 提出 Experiment 的名称、代码和 ID 后确认。不要要求 Agent 猜测已有 Experiment、Task、Protocol 或 Record 的 ID。

Protocol 是可复用的版本化模板；Record 是一次实际实验的冻结快照。修改 Protocol 不会改写已经保存的 Record。删除自建 Protocol 会删除它的模板版本，但不会删除历史 Record 的快照；内置 Protocol 不能通过 MCP 删除。

## 6. 数据与安全

LabFlow MCP 和 Desktop 读取同一系统用户的数据目录：

```text
~/Library/Application Support/LabFlow/
```

Desktop 不需要一直打开；MCP 写入后，Desktop 重新获得焦点会重新读取数据。MCP 不接受数据库路径，不执行直接 SQL，也不会把工作区自动上传给 LabFlow。

不过，你发送给 Agent 的文字以及 MCP 返回的结果可能会依照所用 Agent 客户端的隐私政策发送给其模型服务。不要在不了解客户端策略时粘贴受限患者信息、密钥或敏感实验数据。

## 7. 常见问题

**找不到 `labflow-mcp`**：确认安装的是 macOS 0.1.6，并且 App 位于 `/Applications/LabFlow.app`。若 App 在其他位置，请在 MCP 配置中使用它实际的绝对路径。

**Desktop 看不到 Agent 刚创建的内容**：切换离开 LabFlow 窗口后再切回；Desktop 会在重新获得焦点时刷新。

**Agent 说已经创建了 Record 或追踪完整 Sample 谱系**：当前 MCP 没有这些工具。请查看 Agent 实际调用的 LabFlow 工具，或在 Desktop 中完成该操作。

**想移除 MCP**：在 Agent 客户端删除 `labflow` 服务器配置即可。删除 MCP 配置或 `labflow-mcp` 不会删除 LabFlow App、数据库、附件或备份。
