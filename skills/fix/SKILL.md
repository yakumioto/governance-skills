---
name: fix
description: 读最新 design + 相关 feat/task/execute → brainstorming → 直接生成修复 Tasks（不生成 fix 设计文档）
---

# Fix 技能

生成修复任务清单（Tasks），用于 Bug 修复/回归修复/小范围稳定性改进。**不生成** fix 设计文档；但必须保证与 Design/已有 Feature 的约束一致，并通过 execute 历史记录做冲突与重复修复检查。

---

## 允许操作范围（Hard Scope）
- **允许读取：**
  - `docs/*-design.md`（最新一份）
  - `docs/feat/*.md`（只读，用于定位相关需求/接口/约束）
  - `docs/task/*.md`（只读，用于避免重复修复与识别阻塞）
  - `docs/execute/*.md`（只读，用于冲突检查与回归点识别）
  - `templates/tasks.md`                       # 优先：相对项目根目录
  - `../../templates/tasks.md`                 # 优先：仓库内模板（相对当前 SKILL.md）
  - `$HOME/.claude/skills/templates/tasks.md`  # 备用：全局模板目录（若 runner 支持 env 展开）
- **允许写入/修改：**
  - 仅允许创建或更新 `docs/task/*.md`
- **禁止修改：**
  - `docs/feat/*.md`
  - `docs/execute/*.md`
  - `docs/*-design.md`
  - `templates/*`                      # 项目模板文件只读
  - `../../templates/*`                # 仓库内模板（相对当前 SKILL.md）
  - `$HOME/.claude/skills/templates/*` # 全局模板目录只读

---

## 文件命名（Naming）
- 输出：`docs/task/YYYY-MM-DD-HH-MM-<fix-name>.md`
- `<fix-name>` 规则：小写英文/数字/短横线（kebab-case），例如 `panic-on-empty-input`

---

## Fix 边界（Definition of Fix）
**允许（Allowed Fix）：**
- 修复 bug、空指针/崩溃、逻辑错误、兼容性问题、性能回退、可靠性缺陷
- 增补测试、加日志/观测点（仅限定位与验证）
- 小范围重构（仅为修复所必需，且需在 Task Scope 明确列出）

**禁止（Not a Fix / Out of scope）：**
- 新增功能、扩展需求范围（应走 feat）
- 大规模重构、架构调整（应走 feat/design）
- 变更公共接口契约（除非是向后兼容修复，并在 Task 中写清迁移/回滚）

---

## 流程（Process）

### 1) 读取最新 Design
1. 查找 `docs/*-design.md`
2. 以文件名时间戳排序取最新（`docs/YYYY-MM-DD-HH-MM-design.md`）
3. 提取与本修复相关的：Constraints、Key Decisions、Project-level AC、Risks

### 2) 读取相关 Feature / Tasks（如有）
- 相关性匹配规则（满足任一即相关）：
  1. 文件名包含同一个 `<fix-name>` 或相关关键词/模块名
  2. 文档内 `关联条目（Refs）` 包含相同的 `[D?]/[AC?]` 或同一模块/接口
  3. References 引用同一 Design 版本
- 用途：
  - 确认是否已有任务在修同一问题（避免重复修复）
  - 找到既有验收点/回归点（FR/AC/TC）

### 3) 读取相关 Execute（冲突检查，必做）
- 相关性匹配规则（满足任一即相关）：
  1. execute 的 `关联条目（Refs）` 命中相同模块/接口/关键词
  2. execute 的 `Files Changed` 涉及同一目录/文件
  3. execute 的 FAIL/回滚/Notes 指向同一现象或回归点
- 冲突/风险判定（出现任一必须在 Tasks 风险中写明）：
  - 已有 PASS 的 execute 已覆盖同一修复点（疑似重复）
  - 最近存在 FAIL/回滚，且触达同一模块（高回归风险）
  - execute 记录的接口契约/行为与本次修复预期不一致

### 4) Brainstorming（强制收敛）
**能力检查（Capabilities Check）：**
- 检查是否已启用 `/superpowers:brainstorm` 能力
- 如果未启用：
  - 提示用户安装/启用 Superpowers 插件并停止执行

```
请调用 /superpowers:brainstorm <需求描述>
```
等待 `/superpowers:brainstorm` 完成并获得需求摘要。

必须产出以下“修复闭环”要素：
- **复现步骤（Repro）**：最小复现输入/环境
- **根因假设（Root Cause Hypothesis）**：可能原因与排查路径
- **修复策略（Fix Strategy）**：最小变更原则
- **回归面（Regression Surface）**：可能受影响的旧行为
- **验收点（Acceptance）**：复现失败→修复后通过→回归测试通过

### 5) 直接生成 Tasks（Template Fill）
- **只能使用** `templates/tasks.md`
- Tasks 的 References 必须包含（最少）：
  - `Design: <latest design filename>`
  - `Related Feat: ...`（如存在）
  - `Related Execute: ...`（如存在，尤其是 FAIL/回滚相关）
- Tasks 拆分强制规则（修复闭环）：
  - **Task 1：复现与定位**（增加最小复现/测试/日志，Acceptance 证明能稳定复现）
  - **Task 2：修复实现**（最小变更，Acceptance 证明修复生效）
  - **Task 3：回归与加固**（补测试/检查点，Acceptance 证明无回归）
  - 若问题极小可合并 Task，但必须在同一 Task 内包含“复现→修复→回归”的验收链条

---

## 输出（Output）
- 生成：`docs/task/YYYY-MM-DD-HH-MM-<fix-name>.md`

---

## 失败处理（Failure Modes）
- 找不到项目模板、插件模板、全局模板目录中的`templates/tasks.md`：停止并报告错误（不得生成 tasks）
- 找不到 design：停止并报告错误（不得生成 tasks）
- 无法确定复现/验收：允许在 Task 中标注 `TBD`，但必须将首个 Task 设为“补齐复现与验收”。

## 手动执行（Manual Execution）

本次 `feat` 只生成规格与任务清单，不会执行任何代码变更。

### 1) 选择要执行的 Task
打开本次生成的 Tasks 文件：
- `docs/task/YYYY-MM-DD-HH-MM-<fix-name>.md`

从中挑选一个 `Task ID`（建议从 1 开始，按顺序推进）。

### 2) 调用 Execute 技能执行该 Task
在 ClaudeCode 中运行：

```bash
/execute docs/task/YYYY-MM-DD-HH-MM-<fix-name>.md:<task-id>
```