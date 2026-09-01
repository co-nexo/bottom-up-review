# Bottom-up Review

面向 Codex / AI Coding 的自底向上代码审阅 Skill。它从 Git 变更中的具体实现出发，沿调用链还原业务流程，并检查实际行为是否达成任务目标。

## 核心能力

- 选择尽可能小且可靠的 Git 审阅范围。
- 按业务触发、核心状态变化和可观察结果划分业务流。
- 为每条业务流识别最低变更检查点；起点可以是 Data、Adapter、Port、Domain、Use Case 或 Entry。
- 将 Data、Adapter、Port、Domain、Use Case、Entry 作为证据类型，不当作必须逐层输出的架构清单。
- 先概括业务变化，再以纵向证据卡片和带语义的连接线展示“底层事实 → 关键语义 → 用例行为 → 顶层业务结果”的最短可靠链条。
- 区分具体代码事实、业务语义与整条链的最终影响，便于按证据节点回到代码核查。
- 在支持本地文件跳转的客户端中，将证据符号输出为可点击的源码行链接。
- 将风险反向关联到它破坏的业务流、业务语义或任务目标。
- 使用代码位置支撑结论，区分事实、推断和无法确认的行为。
- 对照明确的任务目标给出“达成、部分达成、未达成或无法确认”的结论。

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
