# Bottom-up Review

面向 Codex / AI Coding 的自底向上代码审阅 Skill。它从 Git 变更中的具体实现出发，沿调用链还原业务流程，并检查实际行为是否达成任务目标。

## 核心能力

- 选择尽可能小且可靠的 Git 审阅范围。
- 按业务触发、核心状态变化和可观察结果划分业务流。
- 为每条业务流识别最低变更检查点；起点可以是 Data、Adapter、Port、Domain、Use Case 或 Entry。
- 将 Data、Adapter、Port、Domain、Use Case、Entry 作为证据类型，不当作必须逐层输出的架构清单。
- 先概括业务变化，再以纵向证据链和带语义的连接线展示“底层事实 → 关键语义 → 用例行为 → 顶层业务结果”的最短可靠链条。
- 将完整结果生成为有硬性篇幅预算的 Markdown 报告，不在聊天中重复长正文。
- 根据证据职责选择字段表、语义 diff、决策表、状态流或接口契约表，避免所有层级使用相同文本框。
- 区分具体代码事实、业务语义与整条链的最终影响，便于按证据节点回到代码核查。
- 在支持本地文件跳转的客户端中，将证据符号输出为可点击的源码行链接。
- 将风险反向关联到它破坏的业务流、业务语义或任务目标。
- 使用代码位置支撑结论，区分事实、推断和无法确认的行为。
- 对照明确的任务目标给出“达成、部分达成、未达成或无法确认”的结论。

## 证据类型

每个证据节点必须标注且只能标注一个规范类型：

| 类型 | 职责 |
| --- | --- |
| Data | 持久化结构、字段、约束、索引及数据迁移 |
| Adapter | SQL、ORM、缓存、序列化或外部系统的读写与转换 |
| Port | 核心拥有的 Repository、Gateway、Clock、Publisher 等能力契约 |
| Domain | 业务规则、不变量、计算和状态迁移 |
| Use Case | 业务动作、调用顺序、事务、权限、幂等和并发编排 |
| Entry | HTTP、事件、任务、CLI 等外部触发及可观察契约 |

类型按变更片段的实际职责判断，不按目录、类名或架构层级套用。项目没有独立 Domain 或 Port 时直接省略。

节点只展示能证明业务语义的字段：共享展示位置、变化、业务含义；存在下一节点时再展示换行的“↓ 传导”。各类型使用固定的信息槽位，但按需字段可以省略；无法用必需字段说明业务作用时，不展示该节点。

## 适用范围

该 Skill 主要面向具有明确业务语义的应用代码，尤其适合审阅后端服务中的 API、持久化、事务、领域规则、消息处理和任务流程，但不限定语言、框架或架构风格。

- 不要求采用 DDD、Clean Architecture 或六边形架构。Data、Adapter、Port、Domain、Use Case、Entry 只是按职责识别证据的标签；项目没有独立 Domain 或 Port 时直接省略。
- Java/Spring 是典型使用场景，Go、C#、TypeScript/Node.js、Python、Rust 等项目只要能够从 Git 变更中识别“触发 → 状态变化 → 可观察结果”，同样适用。
- 前端状态流、CLI、定时任务或数据处理代码如果承载明确业务规则，也可以使用；证据类型按实际职责判断，不按目录名或技术栈套用。
- 不适用于纯格式调整、仅视觉样式修改、无业务语义的机械重构、从零实现功能或脱离具体 Git 变更的通用架构讲解。

## 安装

### Windows PowerShell

将仓库克隆到个人 Skill 目录：

```powershell
git clone https://github.com/co-nexo/bottom-up-review.git `
  "$HOME\.agents\skills\bottom-up-review"
```

### Windows CMD

```bat
git clone https://github.com/co-nexo/bottom-up-review.git "%USERPROFILE%\.agents\skills\bottom-up-review"
```

### macOS / Linux

```bash
git clone https://github.com/co-nexo/bottom-up-review.git \
  "$HOME/.agents/skills/bottom-up-review"
```

安装后重新打开 Codex 会话，并使用 `/skills` 确认列表中出现 `Bottom-up Review`。如果目标目录已经存在，不要重复执行 `git clone`，直接按照下节更新即可。

### 使用 Skill Installer

也可以在 Codex 中直接输入：

```text
$skill-installer 从 https://github.com/co-nexo/bottom-up-review 安装 Skill。Skill 位于仓库根目录（path 为 .），安装名称为 bottom-up-review。
```

使用 Skill Installer 适合一次性安装；如果希望以后通过 Git 持续更新，推荐使用上面的 `git clone` 方式。

## 更新

Windows PowerShell：

```powershell
git -C "$HOME\.agents\skills\bottom-up-review" pull --ff-only
```

Windows CMD：

```bat
git -C "%USERPROFILE%\.agents\skills\bottom-up-review" pull --ff-only
```

macOS / Linux：

```bash
git -C "$HOME/.agents/skills/bottom-up-review" pull --ff-only
```

更新后开启新的 Codex 会话；如果更新没有立即出现，请重启 Codex。

## 使用

显式调用名称为 `$bottom-up-review`。为了得到可靠结论，建议同时提供审阅范围和任务目标。

审阅当前工作区变更：

```text
$bottom-up-review 审阅当前工作区的未提交变更。
任务目标：修复重复扣减用户额度的问题。
```

审阅指定提交：

```text
$bottom-up-review 审阅 abc1234^..abc1234。
任务目标：增加批量查询能力，并保证单个查询失败不会中断整个批次。
```

审阅指定文件：

```text
$bottom-up-review 只审阅以下文件的当前变更：
- src/main/java/example/QuotaService.java
- src/main/java/example/QuotaRepository.java

任务目标：保证额度扣减具有幂等性。
```

以上 Java 路径仅用于示例，不代表语言限制；替换为当前项目中的实际文件即可。

指定 Markdown 报告位置：

```text
$bottom-up-review 审阅当前工作区的未提交变更。
任务目标：验证 ADR-007 的草稿审核与发布设计。
将报告写入 docs/reviews/adr-007.md。
```

报告必须输出到当前项目目录内；未指定路径时默认写入 `docs/reviews/`，并加入 `.git/info/exclude`。项目外路径会改写为项目内路径。文件名使用 `BUR-<业务名称>-<YYYYMMDD>-v<版本>.md`，其中 `BUR` 为 Bottom-up Review 的固定大写前缀，版本从 `v01` 起，同业务同日重新生成时递增，无法识别业务名称时使用审阅范围代替。Codex 在回复中提供可点击文件链接。默认单个报告不超过 180 个非空行和 16 KiB，最多包含 3 条业务流及 5 项风险；超限时按业务流拆分为多个 Markdown 文件，不截断阻断或重要风险。

该 Skill 默认允许隐式调用，因此自然语言请求与其描述匹配时，Codex 也可能自动选择它。正式审阅时推荐显式使用 `$bottom-up-review`，以确保调用行为清晰可见。

## 项目结构

```text
bottom-up-review/
├── SKILL.md
├── agents/
│   └── openai.yaml
└── README.md
```

- `SKILL.md`：Skill 的触发描述、审阅规则和输出格式。
- `agents/openai.yaml`：Codex 中的显示名称、简介和默认提示。
- `README.md`：面向使用者的安装和使用说明。
