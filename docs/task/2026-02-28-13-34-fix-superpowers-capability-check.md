# 任务清单（Tasks）：修复 Superpowers 未安装但还继续执行问题

**创建时间（Created）：** 2026-02-28-13-34

**关联文档（Traceability）：**
- 设计文档（Design）：
  - docs/2026-02-28-12-51-design.md（覆盖：[D1] / 受：[AC5]）
- 功能文档（Feature）：
  - 无
- 其他参考（Docs / ADR / RFC / Tickets）：
  - 无

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
| 1 | completed | P0 | 简化能力检查逻辑并更新版本号 | Design:2026-02-28-12-51-design.md [D1][AC5] | - | 2026-02-28-13-37 | docs/execute/2026-02-28-13-37-fix-superpowers-capability-check-1.md |

> 优先级（Priority）：P0 必做 / P1 应做 / P2 可选
> Refs 示例：`Feature:xxx [FR1][AC2]`，`Design:yyy [D3][AC1]`

---

## Task 1: 简化能力检查逻辑并更新版本号

**关联条目（Refs）：** Design:docs/2026-02-28-12-51-design.md [D1][AC5]

### 1.1 Scope（范围）
**允许修改（Allowed）：**
- `skills/design/SKILL.md`
- `skills/feat/SKILL.md`
- `skills/fix/SKILL.md`
- `.claude-plugin/plugin.json`

**禁止修改（Forbidden）：**
- `templates/*`
- `docs/*-design.md`
- `skills/execute/SKILL.md`

**不在本任务范围（Out of scope）：**
- execute 技能的能力检查逻辑（保持现状）
- 新增功能或大规模重构

---

### 1.2 实现要点（Implementation Notes）
- 原逻辑问题：要求"仍需生成 execute 文档"，但 design/feat/fix 流程不涉及 execute
- 修复方案：简化为检查是否已启用 `/superpowers:brainstorm` 能力，未启用则停止并提示
- 版本更新：0.1.0 → 0.1.1（错误修复）

### 1.3 变更点清单（Change List）
- 文件/模块：skills/design/SKILL.md, skills/feat/SKILL.md, skills/fix/SKILL.md, .claude-plugin/plugin.json
- 行为变化：能力检查逻辑简化，移除了不合理的 execute 文档生成要求
- 配置/接口：插件版本号从 0.1.0 更新到 0.1.1

---

### 1.4 Dependencies & Risks（依赖与风险）
- 依赖（Deps）：无
- 风险（Risk）：无 → 缓解（Mitigation）：无
- 回滚（Rollback）：git checkout -- skills/*.md && git checkout -- .claude-plugin/plugin.json

---

### 1.5 Acceptance（验收）
**Commands（执行命令）：**
- `git diff skills/`
- `cat .claude-plugin/plugin.json | grep version`

**Assertions（关键断言）：**
- 断言 1：skills/design/SKILL.md、skills/feat/SKILL.md、skills/fix/SKILL.md 中的能力检查逻辑已简化
- 断言 2：.claude-plugin/plugin.json 中的版本号已更新为 0.1.1

**Evidence（证据）：**
- 输出日志/截图：git diff 输出
- PR / commit：待提交
