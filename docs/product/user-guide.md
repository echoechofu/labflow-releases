# LabFlow 0.1.5 用户手册

本手册适用于 **LabFlow 0.1.5 — macOS / Windows x64 测试版**，重点说明日常使用、自建 Protocol，以及实验已经进行到中途时如何开始使用 LabFlow。关于 Project、Task、Protocol、Record、Sample 的完整关系和 Sample Flow 示例，请阅读[核心对象、Record 与 Sample Flow 使用指南](core-objects-and-sample-flow-guide.md)。

## 1. 安装与数据位置

从公开的 [LabFlow Downloads](https://github.com/echoechofu/labflow-releases/releases/latest) 下载与系统匹配的最新版文件：

- Apple Silicon macOS 12+：文件名包含 `Apple-Silicon` 的 ZIP；
- Windows 10/11 x64：优先下载文件名包含 `Windows-x64-Setup` 的 EXE。

如希望确认安装包与 Release 中发布的文件完全一致，可以同时下载 `SHA256SUMS.txt`：对 ZIP 或 DMG 执行 `shasum -a 256 <文件名>`，再用 `cat SHA256SUMS.txt` 查看官方值。两处 Hash 应完全一致；这是可选的完整性校验，不是安装的强制步骤。当前版本尚未经过 Apple Developer ID 签名和公证；解压并将 `LabFlow.app` 拖入“应用程序”后，先尝试打开一次。若被拦截，请打开“系统设置”→“隐私与安全性”，在“安全性”区域点击 `LabFlow was blocked to protect your Mac` 旁的“仍要打开”（Open Anyway），再在确认窗口中点击“打开”。

Windows MVP 当前没有 Authenticode 签名。可按需核对 SHA-256，然后按 [Windows 安装与 SmartScreen 说明](windows-install.md)安装。

实验数据不会写入 App 安装目录，而是保存在：

```text
macOS:   ~/Library/Application Support/LabFlow/
Windows: %APPDATA%\LabFlow\
```

两个平台的目录内都包含 `labflow.sqlite` 和 `files/`。

删除 App 或重新下载 App 不等于删除用户数据。请不要手工移动、修改或删除上述目录；迁移数据时应使用“数据管理”中的工作区备份。

## 2. 认识主界面

左侧导航包含五个入口：

| 入口 | 用途 |
| --- | --- |
| 日历 | 创建、查看和编辑 Task；从 Task 打开实验记录。 |
| 实验 | 查看每个 Experiment 内 Task 的只读网状关系。 |
| Protocols | 查看内置/自建 Protocol、新建 Protocol、编辑 Record 正文模板、删除自建 Protocol。 |
| Records | 按 Task 实验日期查看记录、修改单条正文、选择并合并导出。 |
| 数据管理 | 导出完整工作区备份或恢复备份。 |

几个核心对象的关系是：

```text
Experiment
  └─ Task
       └─ Record（使用某个 Protocol 的版本快照）
            ├─ 输入 Sample
            ├─ 实验正文
            └─ 输出 Sample / Result / 附件
```

Task 的上级关系表达实验安排或工作流依赖；实际使用了什么材料由 Record 的输入 Sample 单独记录。两者可以不同。

## 3. 推荐的首次使用流程

### 3.1 创建 Task 和 Experiment

1. 进入“日历”，点击右上角“新建任务”。
2. 填写任务名称、开始时间和结束时间。
3. 选择已有 Experiment；如果还没有 Experiment，点击“在此新建 Experiment”。
4. 如有需要，选择一个或多个上级 Task。候选列表只显示同一 Experiment 中、开始时间早于当前 Task 的任务。
5. 点击“保存任务”。

上级 Task 是可选的。没有上级 Task 的 Task 仍然可以使用已有 Sample 或登记外部 Sample。

### 3.2 从 Task 创建 Record

1. 在日历中点击 Task。
2. 点击“打开记录”。
3. 输入 Protocol 的名称、描述或分类关键词。
4. 选择匹配的 Protocol。
5. 选择本次实际使用的 Sample，或登记当前已经存在的 Sample。
6. 填写该 Protocol 要求的字段。
7. 点击“创建实验记录”。

创建成功后，LabFlow 会在同一次数据库 transaction 中保存 Record、Protocol snapshot、实验正文、ProcessEvent、输入使用情况，以及适用时的输出 Sample。任一步失败时，本次新登记的外部 Sample 也不会留下半成品。

同一个 Task 最多创建一个 Record。再次点击“打开记录”会打开已有 Record，不会重复创建。

### 3.3 完成 Task

在 Task 详情中点击“标记为完成”。创建 Record 时 Task 会进入进行中状态；“完成”用于表示该 Task 已结束。

## 4. 中途开始使用：登记现实中已经存在的 Sample

### 4.1 什么时候使用“外部登记 Sample”

当现实实验已经进行了一段时间，但此前没有在 LabFlow 中记录时，不需要补造历史 Task，也不要创建假的“导入实验”。

例如，开始使用 LabFlow 时已经有：

```text
A549 siNC 24 h
A549 siARH 24 h
```

下一步准备提取 RNA。此时可以把两份现有细胞登记为当前 Experiment 的 external Sample，并从 RNA Extraction 这一步开始记录。

external 的含义是：

- Sample 在现实世界中已经存在；
- 它是 LabFlow 所知谱系的起点；
- LabFlow 不推测或伪造它之前的实验历史；
- 从它进入 LabFlow 以后产生的输入、输出和消耗关系会正常记录。

一个 Experiment 可以有多个互不关联的 external root，例如 Cell、培养上清和已经铺好的 Well。

### 4.2 操作步骤

以“已有处理后的细胞，下一步提取 RNA”为例：

1. 创建或打开“RNA Extraction”Task。这个 Task 可以没有上级 Task。
2. 点击“打开记录”，搜索并选择 RNA Extraction Protocol。
3. 在“本次实验使用什么 Sample？”中展开“外部登记 Sample”。
4. 点击“登记新的当前已有 Sample”。
5. 在 Protocol 允许的类型中选择 `CELL`。
6. 数量填写 `2`。
7. 分别填写 Label，例如 `A549 siNC`、`A549 siARH`。
8. 在“已有实验条件（可选）”中填写当前状态，例如 `siNC，24 h`、`siARH，24 h`。
9. 填写 Protocol 其余字段，点击“创建实验记录”。

系统会创建两个 external Sample，并立即将它们作为本次 Record 的输入。它们的 Sample 编号由系统生成；实验条件放在 metadata 中，不堆进编号。

### 4.3 三类 Sample 来源如何选择

Record 启动界面把可用 Sample 分为：

1. **直接上级 Task 输出**：来自当前 Task 所选直接上级 Task 的输出。
2. **其他 Task 输出**：来自同一 Experiment 的其他 Task，但不是直接上级。
3. **外部登记 Sample**：此前由用户登记、没有 LabFlow 内部来源 Task 的 Sample；也可在这里登记新的当前已有 Sample。

列表会标注来源 Task 和时间。选择时应以“本次实验现实中实际使用了什么”为准，不要仅因为某个 Task 是上级就默认选择其全部输出。

LabFlow 只显示以下可用输入：

- 属于当前 Experiment；
- 类型符合所选 Protocol；
- 尚未消耗；
- 尚未归档。

如果找不到某个 Sample，优先检查 Experiment、Sample 类型以及它是否已被消耗。

### 4.4 保留与消耗

- **保留 / non-destructive**：使用后仍可被后续实验选择。
- **视为已转化/消耗**：使用后不再出现在后续可用 Sample 列表中。
- 某些内置 Protocol 使用 aliquot 语义，表示使用了部分材料，原 Sample 仍可继续使用。

消耗规则来自所选 Protocol。创建 Record 前应确认 Protocol 的 Sample Flow 符合真实实验过程。

## 5. 自建 Protocol

### 5.1 当前自建 Protocol 能做什么

当前向导适合描述“选择一种输入 Sample，然后保留、消耗、派生或仅检测”的常规流程。它支持：

- 一种或多种适用的输入 Sample 类型，或显式选择不限类型；
- 从当前 Experiment 选择已有 Sample，或登记 external Sample；
- 保留或消耗输入 Sample；
- 原 Sample 继续；
- 每个输入产生一个新 Sample；
- 每个输入产生多个新 Sample；
- 仅检测、不产生 Sample；
- 派生 Sample 继承对应父 Sample 的 metadata；
- 保存可渲染的 Record 实验正文模板。

当前向导**不支持**上传 Word/PDF 后自动生成 Protocol，也不能为自建 Protocol 配置任意动态表单、图形化手动选孔、终末检测或专属计算逻辑。自建 Protocol 的 1→1/1→多输出在创建 Record 时通过清单逐行登记。

### 5.2 从哪里进入

有两个入口：

- 进入“Protocols”，点击“新增 Protocol”；
- 创建 Record 时搜索不到 Protocol，点击“新增 Protocol”。

第二种方式会把当前搜索词带入 Protocol 名称，创建完成后可返回 Record 流程重新搜索使用。

### 5.3 Step 1：基本信息

填写：

- **Protocol 名称**：例如 `Reverse Transcription — Custom Kit`；
- **描述**：说明它用于什么实验过程。

名称和描述均为必填项。描述也会用于创建 Record 时的 Protocol 搜索。

### 5.4 Step 2：Sample Flow

#### 适用的输入类型

选择已有类型，例如 `RNA`、`CELL`、`SUP`；也可以直接输入新类型，例如 `MICE` 或 `TISSUE`。

可按分类选择一种或多种适用类型；创建 Record 时只显示匹配的 Sample，同一条 Record 的多个输入仍必须属于同一种类型。冻存、转移等真正通用的操作可选择“不限类型”。新类型会在保存 Protocol 时注册。数据库中的 canonical type 会规范为大写；界面展示名可以保留用户输入形式。Sample 编号和 Sample 类型是两个概念，不要为了保存组别、刺激或时间而创建冗长类型名。

#### 完成以后

| 选项 | 含义 | 例子 |
| --- | --- | --- |
| 原 Sample 沿用 | 保留原 Sample 身份；通过 Record 字段描述刺激、观察等变化。 | 加刺激、非破坏性观察。 |
| 1→1 | 每个输入产生一个新 Sample，原输入消耗。 | RNA → cDNA。 |
| 1→多 | 每个输入产生多个新 Sample，原输入可保留或消耗。 | 动物取材、分装、同时收集多种材料。 |
| 1→0 | 原输入消耗，不产生需要继续追踪的 Sample。 | 破坏性终点检测。 |

输出类型不在 Protocol 中预先固定。创建 Record 时，输出类型按材料分类展开，输出清单逐行填写来源、类型、自定义名称、处理方式、处理时间和其他说明；“复用上一行”用于批量填写。目录中没有合适的通用材料类别时，才登记带有明确显示名和 canonical code 的新类型，类型注册与 Record 创建在同一事务中完成。肺、鼻黏膜、肝等具体部位都选择 `TISSUE`，部位写在 Sample 名称或其他信息中。孔板、培养皿和孔位属于容器与位置，不作为新输出的材料类型。

内置材料类型面向细胞、动物和微生物基础实验：`ANIMAL`、`CELL`、`TISSUE`、`NUCLEI`、`BACTERIA`、`FUNGI`、`VIRUS`、`ORGANOID`、`SPHEROID`、`WHOLE_BLOOD`、`SERUM`、`PLASMA`、`FECES`、`SUP`、`FRACTION`、`DNA`、`RNA`、`CDNA`、`PLASMID`、`AMPLICON`、`LIBRARY`、`PROTEIN`、`PEPTIDE`、`LIPID`、`METABOLITE` 和 `EV`。不提供兜底 `OTHER`；`LYSATE`、`HOMOGENATE`、`EXTRACT` 也不属于默认目录。已有用户自定义类型不会被删除。

#### 输入 Sample

- 原 Sample 沿用固定为保留；
- 1→1 与 1→0 固定为消耗；
- 1→多由创建者选择保留或消耗。

“原 Sample 继续”不能同时选择“视为已转化/消耗”，界面会阻止这种矛盾设置。

可以按下面的判断设置：

| 真实实验情况 | 完成以后 | 输入 Sample |
| --- | --- | --- |
| 只是记录原对象的新状态，仍是同一个 Sample | 原 Sample 沿用 | 保留 |
| 每份输入转化为一份新材料 | 1→1 | 消耗 |
| 每份输入拆分或产生若干个新对象 | 1→多 | 按实际情况选择保留或消耗 |
| 输入被用尽且没有后续材料 | 1→0 | 消耗 |

右侧 Sample Flow Preview 用于核对方向，例如：

```text
RNA-001
  │
  │ Reverse Transcription
  ↓
cDNA-001
```

派生的新 Sample 默认继承父 Sample metadata。Protocol 只需要声明输出类型，不需要让实验者重复填写父 Sample 已有的 group、stimulus 或 time。

### 5.5 Step 3：Record Template

在“实验正文”中填写以后创建 Record 时要保存的文字。可使用三个占位符：

| 占位符 | 创建 Record 时替换为 |
| --- | --- |
| `{{date}}` | 关联 Task 的实验日期。 |
| `{{input_sample_summary}}` | 本次实际选择的输入 Sample 摘要。 |
| `{{output_sample_summary}}` | 本次实际生成的输出 Sample 摘要。 |

示例：

```text
日期：{{date}}
输入 Sample：{{input_sample_summary}}

Procedure:
1. 加入反应体系。
2. 按设定程序孵育。
3. 保存产物。

输出 Sample：{{output_sample_summary}}
```

右侧 Record Preview 只用于预览。点击“创建 Protocol v1”后，新的 Protocol 会保存到当前本地工作区。

### 5.6 修改 Protocol 正文

1. 进入“Protocols”。
2. 在目标 Protocol 卡片上点击“编辑 Record 正文”。
3. 修改模板并保存。

保存会创建新的用户版本，例如 v2，不会覆盖旧版本。已经创建的 Record 保存的是当时的 Protocol snapshot 和已经渲染的独立正文，因此不会随 Protocol 后续修改而变化。

如果只想纠正某一条实验记录，应在“Records”中打开该 Record，点击“修改正文”。这只修改单条 Record，不会写回 Protocol。

### 5.7 删除自建 Protocol

1. 进入“Protocols”，在用户自建 Protocol 卡片上点击“删除”。内置 Protocol 没有删除入口。
2. 确认窗口会显示已有多少条 Record 使用过该 Protocol。确认后会删除 Protocol 及全部模板版本。
3. 已有 Record 不会被删除或改写：名称、版本、正文、schema 与 Terminal Assay 定义均来自创建时冻结的 snapshot，仍可查看和导出。
4. 创建 Protocol 时注册的 Sample Type 不随 Protocol 删除，因为它可能仍被其他 Protocol 或 Sample 使用。

若非常早期的 Record 缺少完整 snapshot，系统会阻止删除并显示错误，以免历史记录功能受损。

## 6. 常见 Protocol 的输入与输出提示

| Protocol | 典型输入 | 输出/使用结果 |
| --- | --- | --- |
| 细胞复苏 | 无已有 Sample | 新建 CELL。 |
| 细胞传代 | CELL | 消耗输入，产生一个或多个 CELL。 |
| 细胞铺板 | CELL | 产生 PLATE 或 DISH。 |
| 细胞加刺激 | CELL / PLATE / DISH / WELL（每条 Record 只选一种类型，可选多个同类型 Sample） | 每个 Plate 可按同一套刺激因素、时间和孔数生成 WELL；Cell/Dish/Well 为状态变化，并以原 Sample 身份登记为输出。 |
| RNA Extraction | CELL / WELL / DISH | 消耗输入，每个输入产生一个 RNA。 |
| Reverse Transcription | RNA | 使用 aliquot，每个输入产生一个 cDNA。 |
| qPCR | cDNA | 不产生 Sample；使用 Plate Mapping、Raw Cq 和 qPCR Analysis。 |
| 培养上清收集 | CELL / WELL / DISH | 不消耗输入，每个输入产生一个 SUP。 |
| ELISA | SUP | 不产生 Sample；使用 Plate Mapping 与 Raw OD。 |
| CCK-8 | WELL | 消耗输入，不产生 Sample；使用 Plate Mapping 与 Raw OD。 |

内置 Protocol 可能包含专属校验和字段。自建 Protocol 的条件分配目前按行优先自动映射位置；需要图形化手动选孔或 qPCR/ELISA/CCK-8 Mapping 时，仍应使用已经存在的专属流程。

## 7. Record 的查看、修改、删除与导出

### 查看与修改正文

进入“Records”，按 Task 实验日期找到记录并打开。点击“修改正文”可以编辑当前 Record 的实验正文；修改不会改变 Protocol 或其他 Record。

编辑时可将光标放在需要的位置，点击“在光标处插入图片”，选择 PNG、JPEG、WebP 或 TIFF。LabFlow 会立即保存当前正文，把原图复制到本工作区，并在正文中显示预览；大尺寸图片和 TIFF 会另外生成最长边不超过 1448 px 的 8 位 RGBA PNG 预览，原图不会被压缩或覆盖。新生成预览的解码像素缓冲区不超过 8 MiB（原 2048 px 方案约为 16 MiB）；这不是整个应用的内存上限，不包含生成预览时解码原图、浏览器缓存和 GPU 副本等开销。已有预览不会自动重新生成。Record 列表不加载图片，打开正文后图片进入可见区域时才加载。

### 删除 Record

打开 Record，点击“删除记录”并确认。删除成功后，关联 Task 会恢复为计划中。如果该 Record 的输出 Sample 已被下游使用，系统会阻止删除，以免破坏 Sample lineage。

### 合并导出

1. 在“Records”选择开始日期和结束日期。
2. 勾选需要的日期或单条 Record。
3. 点击“合并导出”。
4. 核对图文预览后，大量图片请选择“低内存 PDF”，选择新的 `.pdf` 文件名。它按页生成、逐页写盘，显示已写入页数及已处理图片数，并支持取消。
5. 需要可选中、可复制的文字时，可使用“打印 / 保存 PDF”，在系统打印面板中保存。该路径最多支持 8 个正文图片引用；超过时请使用低内存模式或拆分导出。

Record 按关联 Task 的实验日期排序，而不是按最后编辑时间排序。两种 PDF 均使用预览图，工作区保留原图。低内存 PDF 每页为 1240 × 1754 的 A4 图像（约 150 dpi，JPEG 质量 94%），保持图文顺序，但文字不能选中、搜索或复制，也不用于原始实验图片的定量分析。需要原始图像请使用完整工作区备份。

低内存模式一次只解码一张图片、复用一张约 8.3 MiB 的页画布，不缓存已处理图片，也不在内存拼接整份 PDF。正常新预览的单张 RGBA8 像素缓冲区约不超过 8 MiB；旧的 2048 px 预览仍可使用，单张约 16 MiB。编码、传输、浏览器和 GPU 有额外开销，这不是整个应用的绝对内存上限。正文浏览最多保留 4 张可见预览画布（约 32 MiB），离开视口后清空；导出期间卸载正文预览。

缺图、读图或写盘失败会中止导出并清理未完成的临时文件，不会默默跳过图片；取消也会清理。本模式不覆盖已有 PDF。请优先保存到本机磁盘（完成时需要文件系统支持硬链接），再复制到外部设备。若强制退出或断电，保存目录可能遗留 `.labflow-<id>.pdf-part` 临时文件，可在确认 LabFlow 已退出后删除；它不是完整 PDF。单次最多 10000 页、文件不超过 8 GiB，超出请拆分导出。

## 8. 工作区备份与迁移

### 导出

进入“数据管理”，点击“一键导出”，选择保存位置。生成的 `.labflow-backup` 包含 SQLite、附件、Sample lineage 和用户 Protocol。

建议在以下时间备份：

- 第一次正式录入前；
- 完成一批重要实验后；
- 升级测试版前；
- 迁移到另一台 Mac 前。

### 恢复

1. 进入“数据管理”，点击“选择备份文件”。
2. 核对备份时间和对象数量。
3. 点击“确认替换当前工作区”。

恢复是完整替换，不会合并两个工作区。LabFlow 会先为当前工作区创建恢复点，再校验并替换数据。

## 9. 当前测试版的重要边界

- 这是 local-first 单机版本，没有云同步和多人协作。
- Experiment 的 Task 网络当前只读；关系在新建/编辑 Task 时设置。
- Sample lineage 主要用于内部完整性，不提供面向用户的独立谱系图。
- 用户自建 Protocol 是受限 Sample Flow 向导，不是任意 Protocol DSL 或文档导入器。
- ELISA、CCK-8 当前有 Mapping 与 Raw Data 骨架，但尚无专属计算层。
- 安装包尚未签名、公证。
- 本版本采用 PolyForm Noncommercial License 1.0.0，不允许商业使用。

遇到问题时，请先记录：LabFlow 版本、Task/Protocol 名称、操作步骤和界面报错；在可能的情况下，先通过“数据管理”导出工作区备份，再进行测试版升级或故障处理。
