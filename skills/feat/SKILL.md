---
name: feat
description: 在 discover 已完成路径判定后，生成 Feature 文档与对应 Tasks 文档
---

# Feat — 功能规格与任务文档生成

本 Skill 负责：
- 承接 `discover` 已确认的 FEAT 类需求
- 基于最新 Design Baseline 生成新的 Feature 文档（Feature Spec）
- 同步生成对应的 Tasks 文档（Tasks）
- 保证与全局 Design 的约束一致，并与历史 Feature / Tasks / Execute 保持可追溯关系

<HARD-GATE>
本 Skill 仅可在 `discover` 已输出 `NEXT_STATE: FEAT` 后触发。

执行过程中：
- 禁止重新进行需求分流
- 禁止重复执行 discover 职责
- 禁止生成实现代码
- 禁止生成 patch / diff / PR / git 命令
- 禁止描述函数级或行级修改步骤
- 禁止生成除 `docs/features/*.md` 与 `docs/tasks/*.md` 外的任何文件
- 禁止运行会改变工作区的命令
</HARD-GATE>

<HARD-SCOPE>
允许读取：
- `docs/*-design.md`              # 最新全局设计基线
- `docs/features/*.md`            # 既有功能规格，只读
- `docs/tasks/*.md`               # 既有任务文档，只读
- `docs/executes/*.md`            # 既有执行记录，只读
- `templates/feature.md`          # 插件 Feature 模板
- `templates/tasks.md`            # 插件 Tasks 模板

允许写入：
- `docs/features/*.md`
- `docs/tasks/*.md`
</HARD-SCOPE>

---

## 输入前提（Preconditions）

触发本 Skill 前，必须满足：

- 已完成 `discover`
- `discover` 输出结果为：

```text
NEXT_STATE: FEAT
```

---

## 流程（Process）

### 1) 汇总当前输入
- discover 已确认的需求意图
- 可读取的模板字段要求

### 2) 读取相关文档（冲突检查，必做）
- 相关性匹配规则（满足任一即可）：
  1. execute 的 `关联条目（Refs）` 含 `<feat-name>` 或引用同一 Feature/Design 编号
  2. execute 的 `Files Changed` / `Notes` 提到相同模块/接口
  3. 与"相关 Tasks"中声明的变更点（模块/文件/接口）有交集
- 冲突判定（出现任一则标记冲突，并在 Feature 的 Risks 或 Notes 明确写出）：
  - 已有 PASS 的 execute 覆盖了本次计划的同一 FR/AC（疑似重复实现）
  - 存在未完成或被标记 blocked 的相关 Task，且本次 Feature 计划再次覆盖同一 FR/AC（疑似重复拆分/重复推进）
  - execute 显示某模块近期变更且存在 FAIL/回滚（高风险区域）
  - 接口/契约字段名或行为在历史 execute 中已固定，与本次设计不一致

### 3) 生成 Feature 文档（Template Fill）
- **只能使用** `templates/feature.md` 渲染输出
- 强制写入 References（最少包含）：
  - `Design: <latest design filename>`
  - `Related Feat: ...`（如存在）
  - `Related Execute: ...`（如存在，至少列出冲突相关的）
- Feature 文档必须包含 `Traceability`：
  - 覆盖哪些 `[G?]`
  - 受哪些 `[D?]` 约束
  - 需要满足哪些项目级 `[AC?]`

### 4) 生成 Tasks 文档
- **只能使用** `templates/tasks.md` 渲染输出
- Tasks 拆分规则（强制）：
  - 每个 Task 尽量对应一个可合并 PR（或一组连续 commit）
  - 每个 Task 必须包含：
    - `关联条目（Refs）`：至少引用本 Feature 的 `[FR?]` 与 `[AC?]`，必要时补 `[D?]`
    - Scope（Allowed/Forbidden/Out of scope）
    - Acceptance（Commands + Assertions + Evidence）
  - Task 粒度约束：
    - 若 Implementation Plan 超过 7 步，必须拆成多个 Task
- Tasks 的 References（最少包含）：
  - `Design: <latest design filename>`
  - `Feature: docs/features/<this-feat>.md`
  - `Related Execute: ...`（如存在）

---

## 输出（Output）
- 生成 Feature：`docs/features/YYYY-MM-DD-HH-MM-<feat-name>.md`
- 生成 Tasks：`docs/tasks/YYYY-MM-DD-HH-MM-<feat-name>.md`

---

## 失败处理（Failure Modes）
- 找不到 `templates/feature.md`：停止并报告错误（不得生成文档）
- 找不到 `templates/tasks.md`：停止并报告错误（不得生成文档）
- 找不到 design：停止并报告错误（不得生成 feature/tasks）
- 冲突检查发现"重复实现/强冲突"：仍可生成文档，但必须在 Feature 的 `Risks` 或 `Notes` 中列出冲突点，并在 Tasks 中加入首个 Task 用于"对齐/清理/确认"

---

## 手动执行（Manual Execution）

本次 `feat` 只生成规格与任务清单，不会执行任何代码变更。

### 1) 选择要执行的 Task
打开本次生成的 Tasks 文件：
- `docs/tasks/YYYY-MM-DD-HH-MM-<feat-name>.md`

从中挑选一个 `Task ID`（建议从 1 开始，按顺序推进）。

### 2) 调用 Execute 技能执行该 Task

```bash
/execute docs/tasks/YYYY-MM-DD-HH-MM-<feat-name>.md:<task-id>
```
