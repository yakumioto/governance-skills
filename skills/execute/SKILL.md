---
name: execute
description: 读取 task 文档，按 task-id 执行 Scope 内变更，生成可审计 execute 文档（含额外建议）
---

# Execute 技能

按指定 Task 执行变更，并记录可回放的执行证据（Commands/Assertions/Evidence），输出 execute 文档作为审计与回归依据。

---

## 允许操作范围（Hard Scope）
- **允许读取：**
  - `docs/task/<task-file>.md`
  - `docs/*-design.md`（最新一份，仅用于引用补全）
  - `docs/feat/*.md`（只读，用于引用与上下文理解，如 task refs 指向）
  - `templates/execute.md`                       # 优先：相对项目根目录
  - `../../templates/execute.md`                 # 优先：仓库内模板（相对当前 SKILL.md）
  - `$HOME/.claude/skills/templates/execute.md`  # 备用：全局模板目录（若 runner 支持 env 展开）
- **允许写入/修改：**
  - 仅允许创建或更新 `docs/execute/*.md`
- **禁止修改：**
  - `docs/task/*.md`
  - `docs/feat/*.md`
  - `docs/*-design.md`
  - `docs/templates/*`                           # 项目模板文件只读
  - `../../templates/*`                 # 仓库内模板（相对当前 SKILL.md）
  - `$HOME/.claude/skills/templates/*`  # 备用：全局模板目录（若 runner 支持 env 展开）

---

## 文件命名（Naming）
- 输出：`docs/execute/YYYY-MM-DD-HH-MM-<task-name>-<task-id>.md`
- `<task-name>`：从 Task 标题（`## Task <id>: ...`）提取，转为 kebab-case（小写+短横线）

---

## 流程（Process）

### 1) 读取 Task 文档
1. 打开 `docs/task/<task-file>.md`
2. 读取 `Execution Timeline` 表，确认 `<task-id>` 存在
3. 定位到对应段落：`## Task <task-id>: ...`
   - **只接受精确匹配** `## Task {{task-id}}:`，不得模糊匹配，避免错段落

### 2) 提取执行约束与验收信息
从该 Task 段落提取：
- `关联条目（Refs）`
- Scope：Allowed / Forbidden / Out of scope
- Acceptance：Commands / Assertions / Evidence（期望）
- 依赖与风险（Deps/Risks/Rollback）

### 3) 执行前检查（Pre-flight）
- 生成环境快照（必须写入 execute 文档）：
  - repo/workspace、branch、base commit、working tree clean/dirty、OS、toolchain 版本、关键配置（打码）
- **Scope 强制检查**：
  - 仅允许修改 Allowed 指定的文件/目录/模块
  - Forbidden/Out of scope 命中的任何变更：**立即停止**，标记 FAIL，并在 Notes 写明原因与建议拆新 Task

### 4) 按 Scope 执行变更
- 变更必须最小化，仅为满足该 Task 的 Acceptance
- 若发现必须额外改动才能修复：
  - 不得擅自扩 Scope
  - 在 execute 的 Notes/Next Actions 中提出“需新增 Task”的建议

### 5) 运行验收（Acceptance Run）
- 必须执行该 Task 的 Acceptance 命令（Commands）
- 记录：
  - 实际运行命令（按顺序）
  - 关键断言是否满足（Assertions）
  - 输出摘要（Output Summary）
  - 证据（Evidence：日志片段/截图路径/PR/commit）

### 6) 生成 Execute 文档（Template Fill）
- **只能使用** `templates/execute.md`
- References 必须包含（最少）：
  - `Design: <latest design filename>`
  - `Tasks: docs/task/<task-file>.md`
  - `Related Feat: ...`（若 task refs 指向）
  - `Related Execute: ...`（如本次涉及对齐历史冲突）
- Result：
  - PASS：所有 Assertions 满足
  - FAIL：任一 Assertions 不满足或发生 Scope 越界风险

---

## 输出（Output）
- 生成：`docs/execute/YYYY-MM-DD-HH-MM-<task-name>-<task-id>.md`

---

## 失败处理（Failure Modes）
- 找不到项目模板、插件模板、全局模板目录中的`templates/execute.md`：停止并报告错误
- 找不到 `docs/task/<task-file>.md`：停止并报告错误
- 找不到对应 `Task <task-id>` 段落：停止并报告错误
- 发生 Scope 越界：必须 FAIL，且给出 rollback/next actions（不得继续扩改）

---

## 额外建议（Strongly Recommended）
> 为了让 Scope 检查更“机械化”、更不易越界，建议在 tasks 模板中把 Allowed/Forbidden 写成“目录/文件白名单与黑名单”。

示例（推荐写法）：
- Allowed：
  - `internal/foo/**`
  - `cmd/bar/**`
  - `configs/app.yaml`（如需）
- Forbidden：
  - `go.mod`
  - `go.sum`
  - `docs/*-design.md`
  - `templates/**`
  - `docs/feat/**`
  - `docs/task/**`（execute 阶段禁止改 tasks 文档本身）
- Out of scope：
  - “任何新增公共 API”
  - “任何跨模块重构”
  - “任何数据迁移（除非明确写入 Allowed 与 Rollback）”