# LabFlow MCP 安装与使用指南（测试版）

本文面向希望使用 Codex、ChatGPT 桌面应用、WorkBuddy 或其他 MCP 客户端操作 LabFlow 的用户。

LabFlow MCP 是运行在用户电脑上的本地 Agent 接口。连接后，你可以用自然语言查询或管理 Experiment、日历 Task、自建 Protocol 和已有 Record。MCP 不要求 Agent 直接打开 SQLite，也不会复制一套独立的业务规则。

```text
Codex / ChatGPT Desktop / WorkBuddy
                ↓ MCP
          LabFlow shared service
                ↓
       validation + transaction
                ↓
              SQLite
```

## 1. 安装前先确认当前发布状态

从 **LabFlow 0.1.6 macOS** 起，官方 macOS App 已内置 `labflow-mcp`，普通用户安装 App 后即可注册到本机 Agent。无需源码、无需单独下载 MCP 二进制，也不需要 LabFlow 账号或 API Key。

LabFlow 0.1.7 的 macOS 与 Windows 官方安装包均内置 MCP sidecar。普通用户安装 App 后即可注册到本机 Agent，无需取得源码或单独下载 `labflow-mcp`；不要从非官方来源下载 MCP 二进制。

桌面版和 MCP 最好使用相同版本。版本不一致时，工具参数、验证规则或数据库 schema 可能不匹配。

## 2. MCP 能做什么

| 模块                      | 当前支持                                                                  | 当前不支持                                                                  |
| ------------------------- | ------------------------------------------------------------------------- | --------------------------------------------------------------------------- |
| Experiment                | 查询、新建或修改、受保护删除                                              | 强制删除仍有 Task、Sample 或谱系历史的 Experiment                           |
| Task / Calendar           | 查询、新建、改期、修改状态、设置多个上级 Task、受保护删除                 | 绕过时间、跨 Experiment、循环依赖和删除保护                                 |
| Protocol                  | 查询内置及自建 Protocol；创建自建 Protocol；保存新版本；删除自建 Protocol | 删除内置 Protocol；通过修改 Protocol 改写历史 Record                        |
| Record                    | 查询列表和详情、修改正文、受保护删除                                      | **创建 Record**；修改已冻结的 Protocol snapshot；绕过下游 Sample 或导出保护 |
| Sample lineage            | 尚未开放 MCP 工具                                                         | 查询完整谱系、修改父子 Sample、恢复已消耗 Sample                            |
| Terminal Assay / Analysis | 尚未开放 MCP 工具                                                         | qPCR、ELISA、CCK-8 的映射、原始数据和分析操作                               |

创建 Record、选择真实输入 Sample、逐行填写输出 Sample，以及 Terminal Assay 操作目前仍应在 LabFlow Desktop 中完成。

## 3. 支持哪些 Agent 客户端

### 3.1 Codex CLI、Codex IDE 扩展和桌面应用

LabFlow MCP 使用本地 STDIO 传输。Codex 可以通过命令行或 MCP 设置页面启动这个本地进程。

OpenAI 官方说明中，ChatGPT 桌面应用、Codex CLI 和 IDE 扩展可使用同一 Codex 主机上的 MCP 配置；ChatGPT 网页版不会读取本地 Codex 配置。参见 [OpenAI MCP 文档](https://developers.openai.com/docs/extend/mcp)。

### 3.2 ChatGPT 桌面应用

如果当前 ChatGPT 桌面应用提供“设置 → MCP 服务器”，可以添加本地 STDIO 服务器。填写 `labflow-mcp` 的绝对路径后保存并重启。

### 3.3 ChatGPT 网页版

当前本地 LabFlow MCP **不能直接用于 ChatGPT 网页版**。网页版需要通过已安装插件提供的远程 MCP 工具，而 LabFlow 当前只有本机 STDIO server，没有公开远程服务。

### 3.4 WorkBuddy

源码仓库的一键脚本会写入 WorkBuddy 的 MCP 配置，并安装 LabFlow Agent 与 Calendar skills。

### 3.5 其他 MCP 客户端

只要客户端支持本地 STDIO MCP，就可以把 command 指向 `labflow-mcp`。不同客户端的配置文件位置不同，请以该客户端文档为准。

## 4. 准备工作

当前源码测试通道需要：

- LabFlow Desktop 0.1.6（macOS 内置 MCP 方式）；
- `labflow-eln` 私有源码仓库的合法访问权限；
- Node.js 24；
- Rust 1.98.0；
- macOS 构建时需要 Xcode Command Line Tools；
- Windows 构建时需要 Microsoft C++ Build Tools。

建议先启动一次 LabFlow Desktop。MCP 会使用同一系统用户下的 LabFlow 数据目录：

```text
macOS:   ~/Library/Application Support/LabFlow/
Windows: %APPDATA%\LabFlow\
```

LabFlow Desktop 不需要一直保持打开。MCP 可独立启动并连接同一工作区；当 Desktop 窗口重新获得焦点时，会重新读取 MCP 已提交的变化。

## 5. macOS 安装：推荐方式

### 5.1 打开源码仓库

在终端进入 `labflow-eln` 仓库根目录，也就是能看到 `eln-app`、`scripts` 和 `.agents` 的目录。

### 5.2 安装构建环境

确认版本：

```bash
node --version
cargo --version
```

如尚未安装 Rust 1.98.0：

```bash
rustup toolchain install 1.98.0
```

### 5.3 运行一键安装

```bash
./scripts/install-labflow-mcp.sh
```

脚本会：

1. 构建 release 版 `labflow-mcp`；
2. 把 LabFlow Agent 和 Calendar skills 安装到 WorkBuddy；
3. 更新 `~/.workbuddy/mcp.json`；
4. 如果找到 Codex CLI，自动运行 `codex mcp add labflow`；
5. 启动 MCP 并执行初始化、工具列表和 Experiment 查询 smoke test。

看到类似以下输出表示 MCP server 可以正常启动：

```text
[smoke] ... tools: ["labflow_list_experiments", ...]
[smoke] labflow_list_experiments → ... rows
```

安装结束后，完全退出并重新打开 Agent 客户端。

### 5.4 使用内置于 macOS App 的 MCP

对于已经包含 MCP sidecar 的新版 LabFlow，用户不需要源码构建。把 `LabFlow.app` 拖入“应用程序”后，MCP 的固定路径是：

```text
/Applications/LabFlow.app/Contents/MacOS/labflow-mcp
```

注册到 Codex：

```bash
codex mcp add labflow -- /Applications/LabFlow.app/Contents/MacOS/labflow-mcp
codex mcp get labflow
```

在图形化 MCP 设置中，也可以把同一路径填入 STDIO Server 的 Command。以后用新版 `LabFlow.app` 覆盖旧版时，只要 App 名称和安装位置不变，就不需要重新注册。

## 6. Windows 安装：当前手动方式

当前 Bash 一键脚本主要面向 macOS。Windows 源码测试用户应在 Windows 本机原生构建，不要把 macOS 二进制复制到 Windows。

### 6.1 构建 MCP

在 PowerShell 中进入源码仓库：

```powershell
cd .\eln-app
npm install
npm run mcp:build
```

生成文件通常位于：

```text
eln-app\src-tauri\target\release\labflow-mcp.exe
```

### 6.2 注册到 Codex

```powershell
codex mcp add labflow -- "C:\完整路径\labflow-mcp.exe"
codex mcp get labflow
```

必须填写绝对路径。移动或删除 EXE 后，客户端将无法启动 LabFlow MCP。

### 6.3 注册到 WorkBuddy

编辑：

```text
%USERPROFILE%\.workbuddy\mcp.json
```

加入：

```json
{
  "mcpServers": {
    "labflow": {
      "command": "C:\\完整路径\\labflow-mcp.exe",
      "args": [],
      "env": {}
    }
  }
}
```

如果文件中已经有其他 MCP server，应只增加 `labflow` 项，不要覆盖其他配置。

## 7. 通过设置页面添加

对于提供图形化 MCP 设置的 Codex、IDE 扩展或 ChatGPT 桌面应用：

1. 打开“设置”或齿轮菜单。
2. 进入“MCP 服务器”。
3. 选择“添加服务器”。
4. 名称填写 `labflow`。
5. 类型选择 `STDIO`。
6. Command 填写 `labflow-mcp` 或 `labflow-mcp.exe` 的绝对路径。
7. Args 保持为空。
8. 保存并重启客户端。

LabFlow MCP 不需要 OAuth、LabFlow 账号或 API Key。你所使用的 Agent 客户端本身可能需要自己的账号或订阅。

## 8. 验证安装

### 8.1 Codex 命令行检查

```bash
codex mcp get labflow
codex mcp list
```

在 Codex 或 ChatGPT 桌面应用中，也可以输入：

```text
/mcp
```

确认 `labflow` 处于 enabled/connected 状态。

### 8.2 第一次只做读取测试

向 Agent 输入：

> 列出 LabFlow 里的所有 Experiment，不要修改任何数据。

正确情况下，Agent 会调用 `labflow_list_experiments`，返回当前工作区的 Experiment；新工作区可能返回空数组，这不代表安装失败。

然后可以测试：

> 列出 LabFlow 最近一周的 Task，不要修改任何数据。

如果 Desktop 正在打开，切回 Desktop 窗口即可触发刷新。

## 9. 常用自然语言示例

### 9.1 查询日历

> 查看本周 LabFlow 日历，按日期列出 Task、状态和所属 Experiment。

> 查看“细胞实验”Experiment 中所有计划中和进行中的 Task。

### 9.2 创建 Task

> 在 LabFlow 的 EXP001 Experiment 中创建一个明天下午 2 点到 4 点的“RNA 提取”Task。创建前先把时间和 Experiment 给我确认。

> 把“逆转录”Task 的直接上级设置为“RNA 提取”，其他信息不变。

Agent 会使用本地时区。时间格式由 MCP 转换为 LabFlow 使用的本地日历时间，不应自行改成 UTC。

如果工作区还没有 Experiment，Agent 应先提出 Experiment 的 code、名称和内部 ID，获得确认后再创建 Experiment 和第一个 Task。

### 9.3 创建自建 Protocol

> 我下面会粘贴一个实验方法。请先帮我判断适用的输入 Sample 类型和 Sample Flow，等我确认后再创建 LabFlow Protocol。

> 创建一个“组织 RNA 提取”Protocol：输入类型是 TISSUE，Sample Flow 为 1→1；创建 Record 时再填写实际输出 RNA。正文和字段如下……

创建 Protocol 前，Agent 应明确询问：

- 适用一个、多个还是任意输入 Sample 类型；
- 原 Sample 沿用、1→1、1→多或1→0；
- 1→多时原输入保留还是消耗；
- 哪些内容是固定正文，哪些应成为文字、数字或下拉字段。

孔板、培养皿和孔位是容器或位置信息，不应仅因为方法中写了“96 孔板”就把输入 Sample Type 设置成 `PLATE`。具体肺、鼻黏膜等取材部位使用 `TISSUE` 类型，并写入 Sample 自定义名称或 Record 字段。

### 9.4 查询 Record

> 列出 EXP001 中最近创建的 Record，包括 Task、Protocol、输入和输出 Sample 编号。

> 查看这个 Record 的正文、附件目录和修改历史，不要修改。

### 9.5 修改 Record 正文

> 把 Record `...` 的正文改为下面这版。修改前先显示现有正文和差异，得到我确认后再写入。

正文修改会写入审计记录。修改 Protocol 模板不会改写已经创建的 Record。

### 9.6 删除操作

> 删除这个 Task。先检查它是否已有 Record 或下游 Task，不要用其他删除操作绕过保护。

> 删除这个自建 Protocol。先告诉我会删除哪个 Protocol 和多少个版本，再等待确认。

删除可能被 LabFlow 拒绝，例如 Task 已有 Record、Record 输出已被下游使用，或者 Record 已进入导出清单。这些保护不应被 Agent 绕过。

## 10. 数据与隐私

- `labflow-mcp` 是本地进程，不监听公共网络端口。
- MCP 直接调用 LabFlow 共用的 domain/service，不允许 Agent 直接读写 SQLite。
- 所有写入仍经过 LabFlow validation 和 transaction。
- MCP 本身不会把工作区上传到 LabFlow 云端，因为当前没有 LabFlow 云端服务。
- 但是，你输入给 Agent 的文字以及 MCP 返回给 Agent 的内容，可能会按所用 Agent 客户端的隐私政策发送给其模型服务。不要在不了解客户端数据策略时粘贴敏感患者信息、密钥或受限实验数据。
- 不要要求 Agent 打开 `labflow.sqlite` 或直接修改应用数据目录。

推荐先使用读取请求验证连接，再逐步开放创建、修改或删除请求。

## 11. 常见问题

### 11.1 Agent 找不到 LabFlow 工具

依次检查：

1. 是否已经重启 Agent 客户端；
2. `/mcp` 或 MCP 设置中是否显示 `labflow`；
3. Command 是否为仍然存在的绝对路径；
4. 二进制是否与当前系统匹配；
5. 更新源码后是否重新运行安装脚本或重新构建。

### 11.2 `labflow_list_experiments` 返回空数组

这表示当前工作区还没有 Experiment，不是连接失败。可以先在 Desktop 创建，也可以让 Agent 提出 code、名称和 ID，确认后通过 MCP 创建。

### 11.3 Desktop 看不到 MCP 刚创建的内容

切换到其他窗口，再重新聚焦 LabFlow；Desktop 会在重新获得焦点时刷新。如果仍未出现，重新打开应用并让 Agent先执行一次只读查询确认写入结果。

### 11.4 出现 `validation_error`

输入不符合 LabFlow 规则，例如标题为空、结束时间早于开始时间、字段值超出范围或 Protocol 模板无效。根据错误信息修正后再提交。

### 11.5 出现 `conflict`

操作与现有数据冲突，例如跨 Experiment 父 Task、形成循环、删除仍有依赖的数据。不要让 Agent通过删除其他对象或直接改数据库来绕过。

### 11.6 出现 `persistence_error` 或调用超时

结果可能不确定。先用列表或详情工具检查刚才的操作是否已经成功，再决定是否重试，避免重复创建。

### 11.7 Agent 声称已经创建 Record 或追踪完整 Sample 谱系

当前 MCP 尚未开放 Record 创建和 Sample lineage 工具。要求 Agent 列出它实际调用的工具；如果没有相应 LabFlow 工具，该说法不能视为已经写入系统。

## 12. 更新 MCP

拉取包含 MCP 改动的新源码后，在仓库根目录重新执行：

```bash
git pull
./scripts/install-labflow-mcp.sh
```

Windows 用户重新运行：

```powershell
cd .\eln-app
npm install
npm run mcp:build
```

然后重启 Agent 客户端。更新 MCP 不会删除 LabFlow 工作区。

## 13. 停用或卸载

Codex CLI：

```bash
codex mcp remove labflow
```

图形化客户端可在 MCP 设置中关闭或删除 `labflow`。WorkBuddy 用户可以从 `~/.workbuddy/mcp.json` 中删除 `labflow` 项。

移除 MCP 配置或删除 `labflow-mcp` 二进制**不会删除** LabFlow Desktop、SQLite、附件或备份。不要为了卸载 MCP 而删除 LabFlow 用户数据目录。

## 14. 相关文档

- [LabFlow 用户手册](user-guide.md)
- [核心对象、Record 与 Sample Flow 使用指南](core-objects-and-sample-flow-guide.md)
- [源码构建与客户端配置说明](../setup/labflow-agent-install.md)
- [OpenAI MCP 官方文档](https://developers.openai.com/docs/extend/mcp)
