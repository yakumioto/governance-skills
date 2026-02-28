# 任务清单（Tasks）：{{title}}

**创建时间（Created）：** {{datetime}}
**类型（Type）：** {{task_type}}（feat / fix / refactor / doc / test / chore）

**关联文档（Traceability）：**
- 设计文档（Design）：
  - {{design_ref_1}}（覆盖：[G?] / 受：[D?] / 支撑：项目级[AC?]）
  - {{design_ref_2}}
- 功能文档（Feature）：
  - {{feature_ref_1}}（需求：[FR?] / 验收：[AC?]）
  - {{feature_ref_2}}
- 其他参考（Docs / ADR / RFC / Tickets）：
  - {{other_ref_1}}
  - {{other_ref_2}}

---

## 任务摘要（Summary）
{{task_summary}}

---

## 写作/执行约束（Rules）
- 任务必须“可独立交付”：每个 Task 尽量对应 1 个可合并的 PR 或一组连续 commit。
- 不允许扩大范围：任何超出 Scope 的变更必须新开 Task。
- 验收（Acceptance）必须包含：**命令（Commands）+ 关键断言（Assertions）+ 证据（Evidence）**。
- 每个 Task 完成后更新：状态、完成时间、证据链接。

---

## Execution Timeline

| Task ID | 状态 | 优先级 | 摘要 | 关联（Refs） | 依赖 | 完成时间 | 证据 |
|---------|------|--------|------|--------------|------|----------|------|
| 1 | pending | P0 | {{task1-summary}} | {{task1-refs}} | - | - | - |
| 2 | pending | P1 | {{task2-summary}} | {{task2-refs}} | 1 | - | - |

> 优先级（Priority）：P0 必做 / P1 应做 / P2 可选
> Refs 示例：`Feature:xxx [FR1][AC2]`，`Design:yyy [D3][AC1]`

---

## Task 1: {{task1-summary}}

**关联条目（Refs）：** {{task1-refs}}
- 示例：`Feature: {{feature_ref_1}} [FR1][AC1]`；`Design: {{design_ref_1}} [D2][AC3]`

### 1.1 Scope（范围）
**允许修改（Allowed）：**
- ...

**禁止修改（Forbidden）：**
- ...

**不在本任务范围（Out of scope）：**
- ...

---

### 1.2 实现要点（Implementation Notes）
- ...
- ...

### 1.3 变更点清单（Change List）
- 文件/模块：...
- 行为变化：...
- 配置/接口：...

---

### 1.4 Dependencies & Risks（依赖与风险）
- 依赖（Deps）：...
- 风险（Risk）：... → 缓解（Mitigation）：...
- 回滚（Rollback）：如何撤销/关闭该变更：...

---

### 1.5 Acceptance（验收）
**Commands（执行命令）：**
- `...`
- `...`

**Assertions（关键断言）：**
- 断言 1：...
- 断言 2：...

**Evidence（证据）：**
- 输出日志/截图：...
- PR / commit：...

---

## Task 2: {{task2-summary}}

**关联条目（Refs）：** {{task2-refs}}

### 2.1 Scope（范围）
**允许修改（Allowed）：**
- ...

**禁止修改（Forbidden）：**
- ...

**不在本任务范围（Out of scope）：**
- ...

---

### 2.2 实现要点（Implementation Notes）
- ...
- ...

### 2.3 变更点清单（Change List）
- 文件/模块：...
- 行为变化：...
- 配置/接口：...

---

### 2.4 Dependencies & Risks（依赖与风险）
- 依赖（Deps）：...
- 风险（Risk）：... → 缓解（Mitigation）：...
- 回滚（Rollback）：...

---

### 2.5 Acceptance（验收）
**Commands（执行命令）：**
- `...`
- `...`

**Assertions（关键断言）：**
- 断言 1：...
- 断言 2：...

**Evidence（证据）：**
- 输出日志/截图：...
- PR / commit：...