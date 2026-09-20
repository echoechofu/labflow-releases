# LabFlow 核心对象、Record 与 Sample Flow 使用指南

本文适用于 LabFlow 0.1.6 macOS（Windows 当前公开版本仍为 0.1.5），面向第一次使用 LabFlow 的实验人员。它集中解释 Project、Task、Protocol、Record、Sample 的关系，以及创建 Record、选择输入 Sample、生成输出 Sample 和理解样本谱系的方法。

> 本文描述的是当前版本已经实现的行为。文中会明确区分“数据已经保存”和“桌面端已有查看入口”。

## 1. 五个核心对象是什么关系

用户口中的 **Project**，在当前 LabFlow 界面和数据模型中叫 **Experiment（实验）**。五个对象可以理解为：

```text
Experiment（一个研究主题或实验系列）
└─ Task（计划在某个时间执行的一次工作）
   └─ Record（这次工作实际做了什么）
      ├─ 使用某个 Protocol 的冻结版本
      ├─ 输入 Sample
      └─ 输出 Sample / 检测结果 / 附件
```

| 对象 | 用途 | 关键规则 |
| --- | --- | --- |
| Experiment / Project | 把同一研究目标下的任务和样本组织在一起 | Task 与 Sample 都归属于一个 Experiment |
| Task | 日历中的实验计划 | 可有多个上级 Task；一个 Task 最多创建一个 Record |
| Protocol | 可复用的实验方法模板 | 定义正文、填写字段、适用输入类型和 Sample Flow |
| Record | 某次真实实验的执行记录 | 保存当时 Protocol 的完整快照，后来修改 Protocol 不会改写历史 Record |
| Sample | 实验使用或产生的实际材料 | 有系统编号、类型、状态，并可保存来源 Sample 和来源 Record |

需要特别区分两种关系：

- **Task 上下级关系**表示计划或工作流依赖，用于 Experiment 页面中的 Task 流程图。
- **Sample 父子关系**表示真实材料从哪里来，由 Record 的输入与输出建立。

因此，某个 Task 有父 Task，不代表创建 Record 时只能使用父 Task 的输出。用户仍可主动选择其他 Task 的输出或登记外部 Sample。

## 2. 创建一次实验 Record 的完整流程

### 2.1 先安排实验

1. 在“日历”中创建 Task。
2. 选择或新建 Experiment。
3. 填写计划时间。
4. 如存在前置步骤，选择一个或多个上级 Task。
5. 保存 Task。

### 2.2 创建 Record

1. 在日历中打开 Task，点击“打开记录”。
2. 搜索并选择本次实际采用的 Protocol。
3. 在 Sample 步骤选择本次实际使用的输入 Sample：
   - 直接上级 Task；
   - 其他 Task；
   - 外部登记。
4. 填写 Protocol 要求的正文和字段。只有满足“什么时候显示”的字段才会出现；可见的必填字段必须填写。
5. 如果 Protocol 会产生新 Sample，按每个输入 Sample 填写输出清单，包括类型、自定义名称、处理方式、处理时间和其他说明。
6. 如果是自建 Protocol 且尚未保存默认输出类型，首次创建 Record 时可选择是否把本次的**输出类型顺序**保存为该 Protocol 以后创建 Record 时的预填值。名称、处理方式、处理时间和其他说明不会作为默认值保存。
7. 确认并创建 Record。

创建成功时，系统会在同一个事务中保存 Record、Protocol 版本快照、输入和输出 Sample、Sample 使用状态及谱系关系，并把 Task 状态推进为进行中。一个 Task 最多有一个 Record。

创建后可在 Record 页面查看正文、输入与输出 Sample、图片和附件；实验完成后再将 Task 标记为完成。

## 3. 输入 Sample 的三种来源

三种入口只是在帮助用户找到 Sample，不会改变 Sample 的类型或谱系规则。

### 3.1 直接上级 Task

显示当前 Task 所选父 Task 对应 Record 的 `outputs`。适合沿着既定实验分支继续，例如“RNA 提取”Task 的上级是“动物取材”Task。

### 3.2 其他 Task

显示同一 Experiment 中、不是当前直接上级 Task 的其他 Record 输出。它是用户主动跨分支选择材料的入口，例如临时使用另一个培养 Task 产生的细胞。

### 3.3 外部登记

用于登记在 LabFlow 外部已经存在、没有上游 Record 的真实材料，例如开始使用软件前已经冻存的 RNA。外部登记 Sample 是谱系根节点，不应为了补齐图而虚构上游 Task 或 Record。

无论来自哪一类，候选 Sample 都必须属于当前 Experiment、满足 Protocol 的适用输入类型，并且没有处于已消耗状态。归档或已消耗 Sample 不会进入正常可选列表。

## 4. Sample 的 Type、Label、名称和编号

| 名称 | 含义 | 是否必填 | 是否必须唯一 |
| --- | --- | --- | --- |
| Type | 材料的通用类别，如 `TISSUE`、`RNA`、`PROTEIN` | 必填 | 类型代码在类型库中唯一；不同 Sample 可以使用同一 Type |
| Label | 外部登记时输入的人工标签 | 外部登记时必填 | 不要求唯一 |
| 自定义名称 | 创建输出时为具体材料写的名称，如“鼠 03 鼻黏膜” | 可选；留空时系统自动生成 | 不要求唯一 |
| 系统编号 | LabFlow 自动生成的 `sample_code` | 自动生成 | 在当前工作区内唯一 |
| 内部 ID | 系统内部用于关联的 Sample ID | 自动生成 | 唯一，不需要用户填写 |

“Label”和“自定义名称”在数据中都保存为 Sample 的显示名称 `display_name`，只是出现在不同创建场景。显示名称用于让人识别，**不能替代系统编号作为唯一标识**。

建议 Sample Type 保持通用：动物取材选择 `TISSUE`，把“鼻黏膜”“肺”“骨骼肌”等具体部位写在自定义名称中，不要为每个部位创建新 Type。

## 5. Sample Flow 如何影响输入和输出

自建 Protocol 必须明确选择一种 Sample Flow。

### 5.1 原 Sample 沿用

- 不创建新的 Sample。
- 输入 Sample 同时登记为本 Record 的输出。
- 输入 Sample 保持可用。
- 适合材料身份没有改变、只是状态或处理条件变化的操作，例如细胞加刺激。

处理条件应写在 Record 字段中。不要仅因为加了刺激就创建一个“新细胞类型”。

### 5.2 1→1

- 每个输入 Sample 必须产生且只产生一个新 Sample。
- 原输入 Sample 被消耗，不能再被后续 Record 选择。
- 新 Sample 保存其父 Sample 与来源 Record。
- 适合明确转化，例如组织提取为 RNA。

多个输入 Sample 时，每个输入都要各自填写一个输出。

### 5.3 1→多

- 每个输入 Sample 至少产生一个新 Sample，可以产生多个。
- Protocol 创建时选择原输入 Sample 保留或消耗；该策略作用于本次 Record 的全部输入，而不是逐个输入单独决定。
- 选择“保留”时，原 Sample 仍是本 Record 的输入并继续可用，但不会被复制到输出清单；输出清单只显示新建的子 Sample。
- 每个新输出都关联到产生它的那个输入 Sample。
- 适合分装、取材或把一份材料分成多个后续用途。

多个输入时，界面会按输入 Sample 分段显示输出清单。每个输入的输出数量、类型和描述可以分别填写。“复用上一行”只复制类型和处理信息，不复制自定义名称，避免产生难以区分的重名 Sample。

### 5.4 1→0

- 不创建输出 Sample。
- 输入 Sample 被消耗。
- 适合破坏性终末实验，或材料已全部用于检测且没有剩余可继续使用。

### 5.5 多输入能否独立设置输出

可以独立填写**输出行**，但不能改变 Protocol 的结构规则：

- 1→1：每个输入固定一个输出；
- 1→多：每个输入分别填写一个或多个输出；
- 输出类型、自定义名称、处理方式、处理时间和其他说明均可逐行不同；
- 原输入是保留还是消耗，是整个 Protocol/Record 的统一策略，不能对同一 Record 的不同输入分别设置。

当前自建 Protocol 的多输入采用同类型策略：同一个 Record 选择的多个输入 Sample 必须属于同一 Type，即使 Protocol 的“适用输入类型”允许多个候选类型。

## 6. 谱系、消耗与历史记录

### 6.1 输出是否保存 Parent Sample ID

会。创建派生 Sample 时，系统会持久保存：

- 父 Sample ID；
- 来源 Record ID；
- 来源 Protocol 及版本信息；
- `derived_from` 父子关系；
- Record 的输入、输出角色和 Sample 使用事件。

Record 详情页目前会显示输入和输出 Sample，并显示输出的名称、编号、类型和处理信息。但当前桌面端还没有独立的 Sample 详情页或完整 Sample 谱系图，Record 页面也不会直接展示 Parent Sample ID。

Experiment 页面中的图是 **Task 依赖图**，不是 Sample 谱系图。底层已经保存 Sample 父子关系并具备详情/谱系查询能力，但当前 UI 和 MCP 尚未开放完整的 Sample lineage 浏览入口。

### 6.2 Sample 被消耗后会怎样

- Sample 不会被删除，历史 Record、编号和谱系仍然保留。
- 状态会登记为已消耗，并从以后创建 Record 的可选输入列表中排除。
- 当前没有直接“撤销消耗”按钮。
- 在 Record 尚未导出、且输出没有被下游使用等安全条件满足时，删除该 Record 可以回滚相关写入；系统会拒绝破坏已有下游谱系的删除。

因此，“消耗”表示不能再作为新的实验输入，不表示数据被抹除。

### 6.3 当前有哪些查看能力

| 能力 | 当前状态 |
| --- | --- |
| Record 中查看输入和输出 Sample | 已有 |
| Experiment Task 流程图 | 已有，但不是 Sample 谱系 |
| 父 Sample、来源 Record 等谱系数据持久化 | 已有 |
| 独立 Sample 详情页 | 暂无 |
| 可交互 Sample 谱系图 | 暂无 |
| Sample 全局搜索与元数据筛选 | 暂无 |
| 完整 Sample 历史操作时间线 | 暂无用户界面 |

## 7. Protocol 正文与 Record 字段

### 7.1 四种内容模块

| 模块 | 用途 |
| --- | --- |
| 正文段落 | 保存固定的方法步骤、注意事项和说明；可拖动排序，不要求用户在 Record 中逐项填写 |
| 文字字段 | 收集自由文本，如批号、观察结果、处理条件 |
| 数字字段 | 收集数值，可设置单位、最小值和最大值 |
| 下拉字段 | 让用户从预先定义的选项中单选，适合状态、方法或固定分组 |

正文中的 `{{key}}` 占位符会在创建 Record 时被对应字段值替换。系统摘要占位符（如日期、输入 Sample、输出 Sample）由系统生成；被锁定的系统结构字段不能随意修改或删除。

### 7.2 字段设置如何工作

| 设置 | 作用 |
| --- | --- |
| Key | 字段的稳定机器标识，也是正文占位符名称；在同一 Protocol 内必须唯一 |
| 标签 | 用户在创建 Record 时看到的名称，可以使用中文，不承担唯一标识作用 |
| 默认值 | 打开 Record 表单时预填，用户仍可修改；必须符合字段类型和范围 |
| 必填 | 字段可见时必须填写；被条件隐藏时不要求填写 |
| 什么时候显示 | 仅当另一个字段的值与设定值完全相等时显示当前字段 |

Key 长度为 1–64 位，必须以英文字母或下划线开头，只能包含英文字母、数字和下划线。建议使用稳定、易懂的英文，例如 `treatment_time`，不要把中文标签直接当 Key。

下拉字段的选项不能为空且不能重复。数字字段只接受有效数字，并可按设置检查上下限。

## 8. 输出 Sample 的信息保存在哪里

创建 Record 时填写的输出信息会保存到 Sample 及 Record 快照中：

| 表单项 | 保存含义 |
| --- | --- |
| 类型 | Sample 的 `sample_type` |
| 自定义名称 | Sample 的 `display_name` |
| 处理方式 | Sample metadata 的 `treatment_method` |
| 处理时间 | Sample metadata 的 `treatment_duration` |
| 其他 | Sample metadata 的 `other` |

这些信息还会冻结在 Record 的输出详情中，Record 详情页可以查看。处理方式、处理时间或“其他”留空时保持为空，不会自动继承父 Sample，也不会自动填入 `24h`。

当前版本尚不能在 Sample 创建后单独修改其类型、名称、处理信息或父关系，也没有按这些元数据进行全局检索的 Sample 页面。创建前应认真核对；如确需更正，只能在满足安全删除条件时删除错误 Record 后重新创建。Record 的正文仍可通过“修改正文”单独修订。

## 9. 内置与自定义 Sample Type

### 9.1 当前新建输出可选的内置类型

| 分类 | Type |
| --- | --- |
| 实验对象与培养物 | `ANIMAL` 动物、`CELL` 细胞、`BACTERIA` 细菌、`FUNGI` 真菌、`VIRUS` 病毒制备物、`ORGANOID` 类器官、`SPHEROID` 细胞球 |
| 组织与体液 | `TISSUE` 组织、`WHOLE_BLOOD` 全血、`SERUM` 血清、`PLASMA` 血浆、`FECES` 粪便 |
| 细胞组分与产物 | `NUCLEI` 细胞核、`SUP` 上清、`FRACTION` 分离组分、`EV` 细胞外囊泡 |
| 核酸与文库 | `DNA`、`RNA`、`CDNA`（界面显示 cDNA）、`PLASMID` 质粒、`AMPLICON` 扩增产物、`LIBRARY` 测序文库 |
| 蛋白与小分子 | `PROTEIN` 蛋白、`PEPTIDE` 多肽、`LIPID` 脂质、`METABOLITE` 代谢物 |

`PLATE`、`DISH`、`WELL` 是旧版本兼容类型。当前设计把孔板、培养皿和孔位作为容器或位置信息，不建议把它们新建为材料 Type。`OTHER` 已归档并隐藏；`LYSATE`、`HOMOGENATE`、`EXTRACT` 不在当前默认目录中。

### 9.2 自定义类型

只有当材料本身无法归入上述通用类别时才创建自定义 Type。自定义类型可在 Protocol 的适用输入类型或 Record 输出类型中登记，随后会进入当前工作区的自定义类型列表；删除 Protocol 不会删除已注册类型。

自定义类型要求：

- 显示名称描述一种通用材料类别，而不是具体部位、容器、处理条件或单个样本；
- 类型代码 1–32 位，以英文字母开头，只使用大写英文字母、数字和下划线；
- 不要用 `NASAL_MUCOSA`、`LUNG` 这类具体部位代替 `TISSUE`；
- 不要用 `PLATE`、`DISH`、`WELL` 这类容器名作为新材料类型。

当前没有独立的 Sample Type 管理页。工作区实际注册了哪些自定义类型，可在类型选择器的“自定义类型”分类中查看。

## 10. 动物实验双分支示例

目标是表达两条真实材料链：

```text
ANIMAL → TISSUE（鼻黏膜）→ RNA → cDNA → qPCR
                     └────→ PROTEIN → WB
```

其中“鼻黏膜”不是 Type，而是 `TISSUE` 的自定义名称。

### 10.1 动物取材

创建“动物取材”Protocol：

- 适用输入类型：`ANIMAL`；
- Sample Flow：1→多；
- 如果动物在取材后终末处置，选择消耗原 Sample；如果动物仍继续参与实验，选择保留；
- 对每只动物分别创建两个 `TISSUE` 输出，例如“鼠03 鼻黏膜 RNA用”和“鼠03 鼻黏膜 Protein用”。

这样两份组织都有同一只 ANIMAL 作为 Parent，但后续可进入不同分支。

### 10.2 RNA 分支

1. 自建“组织 RNA 提取”Protocol：输入 `TISSUE`，1→1，输出 `RNA`。
2. 使用内置 Reverse Transcription：输入 `RNA`，输出 `CDNA`。内置流程按取样使用，剩余 RNA 可继续选择。
3. 使用内置 SYBR Green qPCR：输入 `CDNA`，保存 Plate Mapping 和 Raw Cq；它是终末检测，不产生新 Sample，剩余 cDNA 按取样逻辑保留。

当前内置 RNA Extraction 的适用输入主要是 CELL/WELL/DISH，尚未直接覆盖 `TISSUE`，所以组织到 RNA 应使用自建 Protocol。

### 10.3 Protein / WB 分支

1. 自建“组织蛋白提取”Protocol：输入 `TISSUE`，1→1，输出 `PROTEIN`。
2. 如希望严格表达 `PROTEIN → WB`，自建 WB Protocol 并设置输入 `PROTEIN`：
   - 还有剩余 Protein 时选“原 Sample 沿用”；
   - Protein 全部用于检测时选“1→0”；
   - 把 WB 图片插入 Record，原始文件作为附件保存。

当前内置 Western Blot 流程面向 CELL/WELL/DISH 输入并产生 PROTEIN 及结果，不等同于“已有 PROTEIN 作为输入”。自建 WB Protocol 可以表达准确谱系，但不会生成内置 WB 的专属结构化结果对象。

### 10.4 一个组织样本后来再分成两份

应使用 **1→多**：

- 输入：原 `TISSUE`；
- 输出：两个新的 `TISSUE`，名称分别注明 RNA 用和 Protein 用；
- 完全分完时消耗原组织；仍有剩余时保留原组织。

不要用“原 Sample 沿用”，因为那不会创建两份可独立追踪、可分别被下游实验选择的新 Sample。

## 11. 常见判断速查

| 实验情形 | 推荐 Sample Flow |
| --- | --- |
| 细胞加刺激，材料身份仍是同一批细胞 | 原 Sample 沿用 |
| 一份组织完整转化为一份 RNA | 1→1 |
| 一份组织分成 RNA 用和 Protein 用两份 | 1→多 |
| 多只动物分别取多种组织 | 1→多，并按每只输入动物逐行填写输出 |
| 样本全部用于破坏性检测且无剩余 | 1→0 |
| 只取一部分 RNA/cDNA 做检测，剩余材料还在 | 使用支持取样保留的内置检测流程，或按实际材料身份选择原 Sample 沿用 |

选择 Flow 的判断标准不是“实验名字”，而是本次操作后：原材料是否还是同一个实体、是否仍有剩余，以及是否产生了需要独立编号和追踪的新材料。
