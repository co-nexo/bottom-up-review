# Bottom-up Review

面向 Codex / AI Coding 的自底向上代码审阅 Skill。它从 Git 变更中的具体实现出发，沿调用链还原业务流程，并检查实际行为是否达成任务目标。

## 核心能力

- 选择尽可能小且可靠的 Git 审阅范围。
- 按业务触发、核心状态变化和可观察结果划分业务流。
- 按“数据层 → 领域状态层 → 用例编排层 → 业务场景层”追踪变更影响。
- 使用代码位置支撑结论，区分事实、推断和无法确认的行为。
- 对照明确的任务目标给出“达成、部分达成、未达成或无法确认”的结论。

适用于聚焦 Git 差异的业务代码审阅，不适用于从零实现功能或通用架构讲解。

## 安装

### Windows PowerShell

将仓库克隆到个人 Skill 目录：

```powershell
git clone https://github.com/co-nexo/bottom-up-review.git `
  "$HOME\.agents\skills\bottom-up-review"
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
