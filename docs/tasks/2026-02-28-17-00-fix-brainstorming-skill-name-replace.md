# 任务清单（Tasks）：修复 Brainstorming 技能名称替换

**创建时间（Created）：** 2026-02-28-17-00

**关联文档（Traceability）：**
- 设计文档（Design）：
  - docs/2026-02-28-12-51-design.md（覆盖：[D1] / 受：[AC5]）
- 功能文档（Feature）：
  - 无
- 其他参考（Docs / ADR / RFC / Tickets）：
  - Related Execute: docs/executes/2026-02-28-13-37-fix-superpowers-capability-check-1.md（参考：已完成的技能修改）

---

## 写作/执行约束（Rules）
- 任务必须"可独立交付"：每个 Task 尽量对应 1 个可合并的 PR 或一组连续 commit。
- 不允许扩大范围：任何超出 Scope 的变更必须新开 Task。
- 验收（Acceptance）必须包含：**命令（Commands）+ 关键断言（Assertions）+ 证据（Evidence）**。
- 每个 Task 完成后更新：状态、完成时间、证据链接。

---

## Execution Timeline

| Task ID | 状态 | 优先级 | 摘要 | 关联（Refs） | 依赖 | 完成时间 | 证据 |
|---------|------|--------|------|--------------|------|----------|------|
| 1 | completed | P0 | 替换 /superpowers:brainstorm 为 /brainstorming 并更新版本号 | Design:2026-02-28-12-51-design.md [D1][AC5] | - | 2026-02-28-17-05 | docs/executes/2026-02-28-17-05-fix-brainstorming-skill-name-replace-1.md |

> 优先级（Priority）：P0 必做 / P1 应做 / P2 可选
> Refs 示例：`Feature:xxx [FR1][AC2]`，`Design:yyy [D3][AC1]`

---

## Task 1: 替换 /superpowers:brainstorm 为 /brainstorming 并更新版本号

**关联条目（Refs）：** Design:docs/2026-02-28-12-51-design.md [D1][AC5]

### 1.1 Scope（范围）
**允许修改（Allowed）：**
- `skills/design/SKILL.md`
- `skills/feat/SKILL.md`
- `skills/fix/SKILL.md`
- `.claude-plugin/plugin.json`
- `.claude-plugin/marketplace.json`

**禁止修改（Forbidden）：**
- `templates/*`
- `docs/*-design.md`
- `skills/execute/SKILL.md`
- `docs/features/*`

**不在本任务范围（Out of scope）：**
- 功能性变更或新增功能
- 修改 brainstorming 调用的具体行为逻辑

---

### 1.2 实现要点（Implementation Notes）
- **文本替换**：全局替换 `/superpowers:brainstorm` → `/brainstorming`
- **受影响的文件**：
  - `skills/design/SKILL.md`：4 处替换（Hard Gate + 能力检查）
  - `skills/feat/SKILL.md`：4 处替换（Hard Gate + 能力检查）
  - `skills/fix/SKILL.md`：4 处替换（Hard Gate + 能力检查）
- **版本号更新**：
  - `.claude-plugin/plugin.json`：`0.2.0` → `0.2.1`
  - `.claude-plugin/marketplace.json`：`plugins[0].version` `0.2.0` → `0.2.1`
- **修复闭环**：
  - 复现：验证当前技能文件包含 `/superpowers:brainstorm` 引用
  - 修复：执行全局替换并更新版本号
  - 回归：验证替换后技能行为正常、版本号正确

### 1.3 变更点清单（Change List）
- 文件/模块：skills/design/SKILL.md, skills/feat/SKILL.md, skills/fix/SKILL.md, .claude-plugin/plugin.json, .claude-plugin/marketplace.json
- 行为变化：无，仅技能名称变更，调用行为保持一致
- 配置/接口：插件版本号从 0.2.0 升级至 0.2.1

---

### 1.4 Dependencies & Risks（依赖与风险）
- 依赖（Deps）：无
- 风险（Risk）：替换可能导致技能无法正确调用 /brainstorming → 缓解（Mitigation）：通过验收命令验证替换正确性
- 回滚（Rollback）：`git checkout -- skills/*/SKILL.md .claude-plugin/plugin.json .claude-plugin/marketplace.json`

---

### 1.5 Acceptance（验收）
**Commands（执行命令）：**
- `grep -r "/superpowers:brainstorm" skills/`（验证无旧引用）
- `grep -r "/brainstorming" skills/`（验证新引用存在，应有 12 处）
- `cat .claude-plugin/plugin.json | grep '"version"'`（验证版本号）
- `cat .claude-plugin/marketplace.json | grep '"version"'`（验证 marketplace 版本号）

**Assertions（关键断言）：**
- 断言 1：`grep -r "/superpowers:brainstorm" skills/` 无输出（旧引用已全部替换）
- 断言 2：`grep -r "/brainstorming" skills/` 有 12 处匹配（每个技能文件 4 处）
- 断言 3：.claude-plugin/plugin.json 版本号为 `0.2.1`
- 断言 4：.claude-plugin/marketplace.json 中 plugins[0].version 为 `0.2.1`

**Evidence（证据）：**
- 输出日志/截图：上述命令输出
- PR / commit：待提交

---
