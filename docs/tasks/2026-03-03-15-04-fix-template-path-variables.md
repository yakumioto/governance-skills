# 任务清单（Tasks）：修复技能模板查找路径

**创建时间（Created）：** 2026-03-03-15-04
**类型（Type）：** fix（feat / fix / refactor / doc / test / chore）

**关联文档（Traceability）：**
- 设计文档（Design）：
  - docs/2026-02-28-12-51-design.md（受：[D2]模板文件作为只读资源 / 支撑：项目级[AC1][AC5]）
- 其他参考（Docs / ADR / RFC / Tickets）：
  - skills/design/SKILL.md
  - skills/feat/SKILL.md
  - skills/fix/SKILL.md
  - skills/execute/SKILL.md

---

## 任务摘要（Summary）
修复 design/feat/fix/execute 技能的模板查找路径问题。当前使用 `../../templates/` 作为相对路径，在插件运行时指向不正确位置。需要引入 `{plugin_root}` 变量，明确查找顺序为：项目 templates → 全局 templates → 插件 templates。

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
| 1 | completed | P0 | 修改 skills/design/SKILL.md 模板路径变量 | Design:docs/2026-02-28-12-51-design.md [D2] | - | 2026-03-03-15-33 | docs/executes/2026-03-03-15-33-fix-design-skill-template-path-1.md |
| 2 | completed | P0 | 修改 skills/feat/SKILL.md 模板路径变量 | Design:docs/2026-02-28-12-51-design.md [D2] | 1 | 2026-03-03-15-37 | docs/executes/2026-03-03-15-37-fix-feat-skill-template-path-2.md |
| 3 | completed | P0 | 修改 skills/fix/SKILL.md 模板路径变量 | Design:docs/2026-02-28-12-51-design.md [D2] | 2 | 2026-03-03-15-39 | docs/executes/2026-03-03-15-39-fix-fix-skill-template-path-3.md |
| 4 | completed | P0 | 修改 skills/execute/SKILL.md 模板路径变量 | Design:docs/2026-02-28-12-51-design.md [D2] | 3 | 2026-03-03-15-47 | docs/executes/2026-03-03-15-47-fix-execute-skill-template-path-4.md |
| 5 | completed | P0 | 更新插件版本号至 0.2.3 | Design:docs/2026-02-28-12-51-design.md | 4 | 2026-03-03-15-51 | docs/executes/2026-03-03-15-51-fix-plugin-version-update-5.md |

> 优先级（Priority）：P0 必做 / P1 应做 / P2 可选
> Refs 示例：`Feature:xxx [FR1][AC2]`，`Design:yyy [D3][AC1]`

---

## Task 1: 修改 skills/design/SKILL.md 模板路径变量

**关联条目（Refs）：** Design:docs/2026-02-28-12-51-design.md [D2]
- 示例：`Feature: {{feature_ref_1}} [FR1][AC1]`；`Design: {{design_ref_1}} [D2][AC3]`

### 1.1 Scope（范围）
**允许修改：**
- `skills/design/SKILL.md`

**禁止修改：**
- 其他 SKILL.md 文件
- templates/ 目录下的任何文件

**不在本任务范围（Out of scope）：**
- 模板文件内容修改
- 其他技能的 SKILL.md 文件

---

### 1.2 实现要点（Implementation Notes）
- 修改 Allowed Operations 中的模板查找路径
- 将 `../../templates/design.md` 改为 `{plugin_root}/templates/design.md`
- 更新注释说明优先级：项目 templates → 全局 templates → 插件 templates

---

### 1.3 变更点清单（Change List）
- 文件/模块：skills/design/SKILL.md
- 行为变化：模板查找路径使用明确的插件根目录变量
- 配置/接口：N/A

---

### 1.4 Dependencies & Risks（依赖与风险）
- 依赖（Deps）：无
- 风险（Risk）：无明显风险
- 回滚（Rollback）：`git checkout -- skills/design/SKILL.md`

---

### 1.5 Acceptance（验收）
**Commands（执行命令）：**
- `cat skills/design/SKILL.md | grep -A 2 "Allowed Operations"`

**Assertions（关键断言）：**
- 断言 1：skills/design/SKILL.md 包含 `{plugin_root}/templates/design.md`
- 断言 2：skills/design/SKILL.md 注释说明优先级为"项目 templates → 全局 templates → 插件 templates"

**Evidence（证据）：**
- 输出日志/截图：grep 输出显示新的路径变量
- PR / commit：N/A

---

## Task 2: 修改 skills/feat/SKILL.md 模板路径变量

**关联条目（Refs）：** Design:docs/2026-02-28-12-51-design.md [D2]

### 2.1 Scope（范围）
**允许修改：**
- `skills/feat/SKILL.md`

**禁止修改：**
- 其他 SKILL.md 文件
- templates/ 目录下的任何文件

**不在本任务范围（Out of scope）：**
- 模板文件内容修改
- 其他技能的 SKILL.md 文件

---

### 2.2 实现要点（Implementation Notes）
- 修改 Allowed Operations 中的模板查找路径（feature.md 和 tasks.md）
- 将 `../../templates/feature.md` 改为 `{plugin_root}/templates/feature.md`
- 将 `../../templates/tasks.md` 改为 `{plugin_root}/templates/tasks.md`
- 更新注释说明优先级

---

### 2.3 变更点清单（Change List）
- 文件/模块：skills/feat/SKILL.md
- 行为变化：模板查找路径使用明确的插件根目录变量
- 配置/接口：N/A

---

### 2.4 Dependencies & Risks（依赖与风险）
- 依赖（Deps）：Task 1
- 风险（Risk）：无明显风险
- 回滚（Rollback）：`git checkout -- skills/feat/SKILL.md`

---

### 2.5 Acceptance（验收）
**Commands（执行命令）：**
- `cat skills/feat/SKILL.md | grep -A 2 "Allowed Operations"`

**Assertions（关键断言）：**
- 断言 1：skills/feat/SKILL.md 包含 `{plugin_root}/templates/feature.md`
- 断言 2：skills/feat/SKILL.md 包含 `{plugin_root}/templates/tasks.md`

**Evidence（证据）：**
- 输出日志/截图：grep 输出显示新的路径变量

---

## Task 3: 修改 skills/fix/SKILL.md 模板路径变量

**关联条目（Refs）：** Design:docs/2026-02-28-12-51-design.md [D2]

### 3.1 Scope（范围）
**允许修改：**
- `skills/fix/SKILL.md`

**禁止修改：**
- 其他 SKILL.md 文件
- templates/ 目录下的任何文件

**不在本任务范围（Out of scope）：**
- 模板文件内容修改
- 其他技能的 SKILL.md 文件

---

### 3.2 实现要点（Implementation Notes）
- 修改 Allowed Operations 中的模板查找路径
- 将 `../../templates/tasks.md` 改为 `{plugin_root}/templates/tasks.md`
- 更新注释说明优先级

---

### 3.3 变更点清单（Change List）
- 文件/模块：skills/fix/SKILL.md
- 行为变化：模板查找路径使用明确的插件根目录变量
- 配置/接口：N/A

---

### 3.4 Dependencies & Risks（依赖与风险）
- 依赖（Deps）：Task 2
- 风险（Risk）：无明显风险
- 回滚（Rollback）：`git checkout -- skills/fix/SKILL.md`

---

### 3.5 Acceptance（验收）
**Commands（执行命令）：**
- `cat skills/fix/SKILL.md | grep -A 2 "Allowed Operations"`

**Assertions（关键断言）：**
- 断言 1：skills/fix/SKILL.md 包含 `{plugin_root}/templates/tasks.md`

**Evidence（证据）：**
- 输出日志/截图：grep 输出显示新的路径变量

---

## Task 4: 修改 skills/execute/SKILL.md 模板路径变量

**关联条目（Refs）：** Design:docs/2026-02-28-12-51-design.md [D2]

### 4.1 Scope（范围）
**允许修改：**
- `skills/execute/SKILL.md`

**禁止修改：**
- 其他 SKILL.md 文件
- templates/ 目录下的任何文件

**不在本任务范围（Out of scope）：**
- 模板文件内容修改
- 其他技能的 SKILL.md 文件

---

### 4.2 实现要点（Implementation Notes）
- 修改 Allowed Operations 中的模板查找路径
- 将 `../../templates/execute.md` 改为 `{plugin_root}/templates/execute.md`
- 更新注释说明优先级

---

### 4.3 变更点清单（Change List）
- 文件/模块：skills/execute/SKILL.md
- 行为变化：模板查找路径使用明确的插件根目录变量
- 配置/接口：N/A

---

### 4.4 Dependencies & Risks（依赖与风险）
- 依赖（Deps）：Task 3
- 风险（Risk）：无明显风险
- 回滚（Rollback）：`git checkout -- skills/execute/SKILL.md`

---

### 4.5 Acceptance（验收）
**Commands（执行命令）：**
- `cat skills/execute/SKILL.md | grep -A 2 "Allowed Operations"`

**Assertions（关键断言）：**
- 断言 1：skills/execute/SKILL.md 包含 `{plugin_root}/templates/execute.md`

**Evidence（证据）：**
- 输出日志/截图：grep 输出显示新的路径变量

---

## Task 5: 更新插件版本号至 0.2.3

**关联条目（Refs）：** Design:docs/2026-02-28-12-51-design.md

### 5.1 Scope（范围）
**允许修改：**
- `.claude-plugin/plugin.json`
- `.claude-plugin/marketplace.json`（如存在）
- `package.json`（如存在）

**禁止修改：**
- 技能代码逻辑
- 模板文件

**不在本任务范围（Out of scope）：**
- 发布到市场

---

### 5.2 实现要点（Implementation Notes）
- 更新 version 字段从 0.2.2 到 0.2.3
- 更新 marketplace.json 中的版本号（如存在）

---

### 5.3 变更点清单（Change List）
- 文件/模块：.claude-plugin/plugin.json
- 行为变化：版本号更新
- 配置/接口：N/A

---

### 5.4 Dependencies & Risks（依赖与风险）
- 依赖（Deps）：Task 4
- 风险（Risk）：无明显风险
- 回滚（Rollback）：`git checkout -- .claude-plugin/plugin.json`

---

### 5.5 Acceptance（验收）
**Commands（执行命令）：**
- `cat .claude-plugin/plugin.json | grep version`

**Assertions（关键断言）：**
- 断言 1：version 字段为 "0.2.3"

**Evidence（证据）：**
- 输出日志/截图：grep 输出显示版本号 0.2.3
