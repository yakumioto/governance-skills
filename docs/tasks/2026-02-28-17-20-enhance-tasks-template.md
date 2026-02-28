# 任务清单（Tasks）：增强 tasks 模板（添加任务摘要和 type 字段）

**创建时间（Created）：** 2026-02-28-17-20

**关联文档（Traceability）：**
- 设计文档（Design）：
  - docs/2026-02-28-12-51-design.md（覆盖：[G3] / 受：[D2][AC5]）
- 功能文档（Feature）：
  - 无
- 其他参考（Docs / ADR / RFC / Tickets）：
  - 无

---

## 任务摘要（Summary）
为 tasks 模板添加任务摘要和 type 字段，方便读者快速了解文档用途和类型，同时支持自动化工具解析和分类。

## 写作/执行约束（Rules）
- 任务必须"可独立交付"：每个 Task 尽量对应 1 个可合并的 PR 或一组连续 commit。
- 不允许扩大范围：任何超出 Scope 的变更必须新开 Task。
- 验收（Acceptance）必须包含：**命令（Commands）+ 关键断言（Assertions）+ 证据（Evidence）**。
- 每个 Task 完成后更新：状态、完成时间、证据链接。

---

## Execution Timeline

| Task ID | 状态 | 优先级 | 摘要 | 关联（Refs） | 依赖 | 完成时间 | 证据 |
|---------|------|--------|------|--------------|------|----------|------|
| 1 | pending | P0 | 修改 templates/tasks.md 添加 type 字段和任务摘要章节 | Design:docs/2026-02-28-12-51-design.md [D2][AC5] | - | - | - |

> 优先级（Priority）：P0 必做 / P1 应做 / P2 可选
> Refs 示例：`Feature:xxx [FR1][AC2]`，`Design:yyy [D3][AC1]`

---

## Task 1: 修改 templates/tasks.md 添加 type 字段和任务摘要章节

**关联条目（Refs）：** Design:docs/2026-02-28-12-51-design.md [D2][AC5]
- 示例：`Design: docs/2026-02-28-12-51-design.md [D2][AC5]`

### 1.1 Scope（范围）
**允许修改（Allowed）：**
- `templates/tasks.md`

**禁止修改（Forbidden）：**
- 其他模板文件（`templates/design.md`, `templates/feature.md`, `templates/execute.md`）
- `docs/*`
- `skills/*`
- `.claude-plugin/*`

**不在本任务范围（Out of scope）：**
- 迁移现有已生成的 tasks 文档
- 修改其他模板文件

---

### 1.2 实现要点（Implementation Notes）
- **type 字段添加位置**：在 `**关联文档（Traceability）：**` 行之前新增一行
  ```markdown
  **类型（Type）：** {{task_type}}（feat / fix / refactor / doc / test / chore）
  ```
- **任务摘要章节添加位置**：在 `## 写作/执行约束（Rules）` 标题之前新增章节
  ```markdown
  ## 任务摘要（Summary）
  {{task_summary}}
  ```
- type 可选值：feat, fix, refactor, doc, test, chore（类似 Conventional Commits）
- 任务摘要格式：1-2 句话，50 字以内

### 1.3 变更点清单（Change List）
- 文件/模块：templates/tasks.md
- 行为变化：
  - 模板生成的新 tasks 文档包含 type 字段和任务摘要
  - 支持自动化工具解析和分类
- 配置/接口：
  - 新增模板变量 `{{task_type}}`
  - 新增模板变量 `{{task_summary}}`

---

### 1.4 Dependencies & Risks（依赖与风险）
- 依赖（Deps）：无
- 风险（Risk）：模板修改导致使用旧模板的技能生成失败 → 缓解（Mitigation）：修改向后兼容，旧模板变量仍可正常工作
- 回滚（Rollback）：`git checkout -- templates/tasks.md`

---

### 1.5 Acceptance（验收）
**Commands（执行命令）：**
- `cat templates/tasks.md | grep "类型（Type）"`（验证 type 字段存在）
- `cat templates/tasks.md | grep "任务摘要（Summary）"`（验证任务摘要章节存在）
- `grep -A 1 "创建时间（Created）" templates/tasks.md | head -2 | grep "类型（Type）"`（验证 type 字段在 Traceability 标题之前）
- `grep -B 2 "写作/执行约束（Rules）" templates/tasks.md | grep "任务摘要（Summary）"`（验证任务摘要在 Rules 标题之前）

**Assertions（关键断言）：**
- 断言 1：templates/tasks.md 包含 `**类型（Type）：** {{task_type}}（feat / fix / refactor / doc / test / chore）`
- 断言 2：templates/tasks.md 包含 `## 任务摘要（Summary）` 和 `{{task_summary}}`
- 断言 3：type 字段位于 `**关联文档（Traceability）：**` 行之前
- 断言 4：任务摘要章节位于 `## 写作/执行约束（Rules）` 标题之前

**Evidence（证据）：**
- 输出日志/截图：上述 grep 命令输出
- PR / commit：待提交

---
