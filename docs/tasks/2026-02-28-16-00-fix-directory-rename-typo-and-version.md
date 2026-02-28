# 任务清单（Tasks）：修复目录重命名遗留问题

**创建时间（Created）：** 2026-02-28-16-00

**关联文档（Traceability）：**
- 设计文档（Design）：
  - docs/2026-02-28-12-51-design.md（覆盖：[D3] / 受：[AC5]）
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
| 1 | completed | P0 | 修复目录重命名、拼写错误、更新版本号 | Design:2026-02-28-12-51-design.md [D3][AC5] | - | 2026-02-28-16-15 | docs/executes/2026-02-28-16-15-fix-directory-rename-typo-and-version-1.md |

> 优先级（Priority）：P0 必做 / P1 应做 / P2 可选
> Refs 示例：`Feature:xxx [FR1][AC2]`，`Design:yyy [D3][AC1]`

---

## Task 1: 修复目录重命名、拼写错误、更新版本号

**关联条目（Refs）：** Design:docs/2026-02-28-12-51-design.md [D3][AC5]

### 1.1 Scope（范围）
**允许修改（Allowed）：**
- `CLAUDE.md`
- `docs/2026-02-28-12-51-design.md`
- `docs/tasks/2026-02-28-13-34-fix-superpowers-capability-check.md`（随目录移动）
- `docs/executes/` → `docs/executes/`（重命名）
- `docs/tasks/` → `docs/tasks/`（重命名）
- `skills/feat/SKILL.md`（更新路径引用）
- `skills/fix/SKILL.md`（更新路径引用）
- `skills/execute/SKILL.md`（更新路径引用）
- `.claude-plugin/plugin.json`
- `.claude-plugin/marketplace.json`

**禁止修改（Forbidden）：**
- `templates/*`
- `README.md`
- `docs/features/*`（如存在）

**不在本任务范围（Out of scope）：**
- 其他文档或代码的修改
- 功能性变更

---

### 1.2 实现要点（Implementation Notes）
- **拼写错误修复**：CLAUDE.md 第 40 行 `docs/executessss/*.md` → `docs/executes/*.md`
- **目录重命名**：使用 `git mv` 保留 git 历史
  - `docs/executes/` → `docs/executes/`
  - `docs/tasks/` → `docs/tasks/`
- **文档引用更新**：
  - `docs/2026-02-28-12-51-design.md`：架构图中的目录路径 `docs/executes/` → `docs/executes/`
- **Skills 路径更新**（`docs/tasks/` → `docs/tasks/`）：
  - `skills/feat/SKILL.md`：行 16, 25, 38, 58, 122, 140, 148
  - `skills/fix/SKILL.md`：行 16, 22, 34, 111, 126, 134
  - `skills/execute/SKILL.md`：行 14, 23, 41, 78, 94, 114
- **版本号更新**：
  - `.claude-plugin/plugin.json`：`version` 字段从 `0.1.1` 更新为 `0.2.0`
  - `.claude-plugin/marketplace.json`：`metadata.version` 从 `0.1.0` 更新为 `1.0.0`，`plugins[0].version` 从 `0.1.1` 更新为 `0.2.0`

### 1.3 变更点清单（Change List）
- 文件/模块：CLAUDE.md, docs/2026-02-28-12-51-design.md, docs/tasks/2026-02-28-13-34-fix-superpowers-capability-check.md, skills/feat/SKILL.md, skills/fix/SKILL.md, skills/execute/SKILL.md, .claude-plugin/plugin.json, .claude-plugin/marketplace.json
- 目录结构：
  - `docs/executes/` → `docs/executes/`
  - `docs/tasks/` → `docs/tasks/`
- 行为变化：无
- 配置/接口：插件版本升级至 0.2.0

---

### 1.4 Dependencies & Risks（依赖与风险）
- 依赖（Deps）：无
- 风险（Risk）：目录重命名可能影响现有文档引用 → 缓解（Mitigation）：已检查所有文档，仅 design.md 和 skills/*.md 有硬编码引用需更新
- 回滚（Rollback）：`git mv docs/executes docs/execute && git mv docs/tasks docs/task && git checkout -- CLAUDE.md docs/2026-02-28-12-51-design.md skills/*/SKILL.md .claude-plugin/plugin.json .claude-plugin/marketplace.json`

---

### 1.5 Acceptance（验收）
**Commands（执行命令）：**
- `grep "executessss" CLAUDE.md`（应无匹配）
- `ls -la docs/executes/`（应存在）
- `ls -la docs/tasks/`（应存在）
- `ls -la docs/executes/ 2>&1`（应提示不存在）
- `ls -la docs/tasks/ 2>&1`（应提示不存在）
- `grep "docs/executes/\|docs/tasks/" docs/2026-02-28-12-51-design.md | grep -v "docs/executes/\|docs/tasks/"`（应无匹配）
- `grep -n "docs/tasks/" skills/feat/SKILL.md skills/fix/SKILL.md skills/execute/SKILL.md`（应无匹配）
- `cat .claude-plugin/plugin.json | grep -E '"version":|"version"\s*:'`
- `cat .claude-plugin/marketplace.json | grep -E '"version"'`

**Assertions（关键断言）：**
- 断言 1：CLAUDE.md 中不再包含拼写错误 `executessss`
- 断言 2：`docs/executes/` 目录存在且包含原有文件
- 断言 3：`docs/tasks/` 目录存在且包含原有文件
- 断言 4：`docs/executes/` 和 `docs/tasks/` 目录不存在
- 断言 5：design.md 中目录引用已更新为 `docs/executes/`
- 断言 6：skills/feat/SKILL.md、skills/fix/SKILL.md、skills/execute/SKILL.md 中不再包含 `docs/tasks/` 引用
- 断言 7：.claude-plugin/plugin.json 版本号为 0.2.0
- 断言 8：.claude-plugin/marketplace.json 中 metadata.version 为 1.0.0，plugins[0].version 为 0.2.0

**Evidence（证据）：**
- 输出日志/截图：上述命令输出
- PR / commit：待创建

---
