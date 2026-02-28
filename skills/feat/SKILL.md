---
name: feat
description: 读取最新 design + 相关 feat/tasks/execute → brainstorming → 生成 Feature 文档 → 同步生成 Tasks 文档
---

# Feat 技能

生成功能文档（Feature Spec）与任务清单（Tasks），并保证与全局 Design 的可追溯一致性；同时通过读取历史 execute 记录做冲突检查，避免重复实现或引入回归。

---

## 强制限制（Hard Gate）

- 仅允许生成 `docs/features/*.md` `docs/tasks/*.md`
- 仅允许调用 `/superpowers:brainstorm` 命令
- 禁止生成其他任何文件
- 禁止输出实现代码、patch、diff、PR、git 命令
- 禁止描述函数级或行级修改步骤
- 禁止运行会改变工作区的命令

如检测到任何实现性内容（代码/patch/修改步骤），立即停止并报告：
“Feat Skill 违规：检测到实现行为”

---

## 允许操作范围（Hard Scope）
- **允许读取：**
  - `docs/*-design.md`（最新一份）
  - `docs/features/*.md`（只读，用于定位相关需求/接口/约束）
  - `docs/tasks/*.md`（只读，用于避免重复修复与识别阻塞）
  - `docs/executes/*.md`（只读，用于冲突检查与回归点识别）
  - `docs/templates/feature.md`                      # 优先：相对项目根目录
  - `../../templates/feature.md`                # 优先：仓库内模板（相对当前 SKILL.md）
  - `$HOME/.claude/skills/templates/feature.md` # 备用：全局模板目录（若 runner 支持 env 展开）
  - `docs/templates/tasks.md`                        # 优先：相对项目根目录
  - `../../templates/tasks.md`                  # 优先：仓库内模板（相对当前 SKILL.md）
  - `$HOME/.claude/skills/templates/tasks.md`   # 备用：全局模板目录（若 runner 支持 env 展开）
- **允许写入/修改：**
  - 仅允许创建 `docs/features/*.md` 与 `docs/tasks/*.md`
- **禁止修改：**
  - `docs/tasks/*.md`
  - `docs/executes/*.md`
  - `docs/*-design.md`
  - `docs/templates/*`                           # 项目模板文件只读
  - `../../templates/*`                 # 仓库内模板（相对当前 SKILL.md）
  - `$HOME/.claude/skills/templates/*`  # 备用：全局模板目录（若 runner 支持 env 展开）

---

## 文件命名（Naming）
- Feature 输出：`docs/features/YYYY-MM-DD-HH-MM-<feat-name>.md`
- Tasks 输出：`docs/tasks/YYYY-MM-DD-HH-MM-<feat-name>.md`
- `<feat-name>` 规则：小写英文/数字/短横线（kebab-case），例如 `authz-audit-log`

---

## 流程（Process）

### 1) 读取最新 Design
1. 查找 `docs/*-design.md`
2. **以文件名时间戳排序取最新**（`docs/YYYY-MM-DD-HH-MM-design.md`）
3. 提取并作为 Feature 的全局约束来源：
   - Scope / Goals / Non-goals / Key Decisions / Constraints / Project-level AC / Risks / Open Questions

### 2) 读取相关 Feature / Tasks 文档（如有）
- 相关性匹配规则（满足任一即认为相关）：
  1. 文件名包含同一个 `<feat-name>` 前缀或关键词
  2. 文档内 `关联设计（Design Links）` / `关联条目（Refs）` 包含相同的 `[G?]/[D?]/[AC?]`
  3. References 中引用了同一个 Design 版本
- 读取范围：
  - Feature：`docs/features/*.md`
  - Tasks：`docs/tasks/*.md`
- 读取用途：
  - 复用/延续编号体系（FR/NFR/AC/TC/IP 以及 Task ID/Refs）
  - 识别未完成/阻塞任务（pending/blocked）与原因
  - 避免重复拆分与重复实现（相同 FR/AC 已有对应 Task）
  - 对齐任务粒度（一个 Task 对应一个 PR 的约束是否满足）

### 3) 读取相关 Execute 文档（冲突检查，必做）
- 相关性匹配规则（满足任一即可）：
  1. execute 的 `关联条目（Refs）` 含 `<feat-name>` 或引用同一 Feature/Design 编号
  2. execute 的 `Files Changed` / `Notes` 提到相同模块/接口
  3. 与“相关 Tasks”中声明的变更点（模块/文件/接口）有交集
- 冲突判定（出现任一则标记冲突，并在 Feature 的 Risks 或 Notes 明确写出）：
  - 已有 PASS 的 execute 覆盖了本次计划的同一 FR/AC（疑似重复实现）
  - 存在未完成或被标记 blocked 的相关 Task，且本次 Feature 计划再次覆盖同一 FR/AC（疑似重复拆分/重复推进）
  - execute 显示某模块近期变更且存在 FAIL/回滚（高风险区域）
  - 接口/契约字段名或行为在历史 execute 中已固定，与本次设计不一致

### 4) Brainstorming（强制收敛）
**能力检查（Capabilities Check）：**
- 检查是否已启用 `/superpowers:brainstorm` 能力
- 如果未启用：
  - 提示用户安装/启用 Superpowers 插件并停止执行

```
请调用 /superpowers:brainstorm <需求描述>
```
等待 `/superpowers:brainstorm` 完成并获得需求摘要。

- 允许发散，但输出必须收敛到 `templates/feature.md` 的字段与章节，不得写成散文。
- Brainstorming 必须给出：
  - Scope（in/out）、FR/NFR、Feature-level AC、Test Plan 要点、Implementation Plan 步骤草案
  - 与 Design 的 Traceability：明确引用 `[G?] [D?] [AC?]`（项目级）

### 5) 生成 Feature 文档（Template Fill）
- **只能使用** `templates/feature.md` 渲染输出
- 强制写入 References（最少包含）：
  - `Design: <latest design filename>`
  - `Related Feat: ...`（如存在）
  - `Related Execute: ...`（如存在，至少列出冲突相关的）
- Feature 文档必须包含 `Traceability`：
  - 覆盖哪些 `[G?]`
  - 受哪些 `[D?]` 约束
  - 需要满足哪些项目级 `[AC?]`

### 6) 内置生成 Tasks 文档（必做）
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
- 找不到项目模板、插件模板、全局模板目录中的`templates/feature.md`：停止并报告错误（不得生成文档）
- 找不到项目模板、插件模板、全局模板目录中的`templates/tasks.md`：停止并报告错误（不得生成文档）
- 找不到 design：停止并报告错误（不得生成 feature/tasks）
- 冲突检查发现“重复实现/强冲突”：仍可生成文档，但必须在 Feature 的 `Risks` 或 `Notes` 中列出冲突点，并在 Tasks 中加入首个 Task 用于“对齐/清理/确认”。

---

## Execute（手动执行）

本次 `feat` 只生成规格与任务清单，不会执行任何代码变更。

### 1) 选择要执行的 Task
打开本次生成的 Tasks 文件：
- `docs/tasks/YYYY-MM-DD-HH-MM-<feat-name>.md`

从中挑选一个 `Task ID`（建议从 1 开始，按顺序推进）。

### 2) 调用 Execute 技能执行该 Task
在 ClaudeCode 中运行：

```bash
/execute docs/tasks/YYYY-MM-DD-HH-MM-<feat-name>.md:<task-id>
```