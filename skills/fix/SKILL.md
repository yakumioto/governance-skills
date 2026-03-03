---
name: fix
description: 读最新 design + 相关 feat/task/execute → brainstorming → 直接生成修复 Tasks（不生成 fix 设计文档）
---

# Fix — 修复任务清单生成

本 Skill 负责：
- 承接 `discover` 已确认的 FIX 类需求
- 生成修复任务清单（Tasks），用于 Bug 修复/回归修复/小范围稳定性改进
- **不生成** fix 设计文档
- 保证与 Design/已有 Feature 的约束一致
- 通过 execute 历史记录做冲突与重复修复检查

<HARD-GATE>
本 Skill 仅可在 `discover` 已输出 `NEXT_STATE: FIX` 后触发。

执行过程中：
- 禁止重新进行需求分流
- 禁止重复执行 discover 职责
- 禁止生成实现代码
- 禁止生成 patch / diff / PR / git 命令
- 禁止描述函数级或行级修改步骤
- 禁止生成除 `docs/tasks/*.md` 外的任何文件
- 禁止运行会改变工作区的命令
</HARD-GATE>

<HARD-SCOPE>
允许读取：
- `docs/*-design.md`              # 最新全局设计基线
- `docs/features/*.md`            # 既有功能规格，只读
- `docs/tasks/*.md`               # 既有任务文档，只读
- `docs/executes/*.md`            # 既有执行记录，只读
- `templates/tasks.md`            # 插件 Tasks 模板

允许写入：
- `docs/tasks/*.md`
</HARD-SCOPE>

---

## 输入前提（Preconditions）

触发本 Skill 前，必须满足：

- 已完成 `discover`
- `discover` 输出结果为：

```text
NEXT_STATE: FIX
```

---

## 流程（Process）

### 1) 读取最新 Design
- 查找 `docs/*-design.md`
- 以文件名时间戳排序取最新
- 提取与本修复相关的：Constraints、Key Decisions、Project-level AC、Risks

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
- 调用 `/brainstorming` 进行需求分析
- 必须产出以下"修复闭环"要素：
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
  - 若问题极小可合并 Task，但必须在同一 Task 内包含"复现→修复→回归"的验收链条

---

## 输出（Output）
- 生成：`docs/tasks/YYYY-MM-DD-HH-MM-<fix-name>.md`

命名规则：
- `<fix-name>` 规则：小写英文/数字/短横线（kebab-case），例如 `panic-on-empty-input`

---

## 失败处理（Failure Modes）
- 找不到 `templates/tasks.md`：停止并报告错误（不得生成 tasks）
- 找不到 design：停止并报告错误（不得生成 tasks）
- 无法确定复现/验收：允许在 Task 中标注 `TBD`，但必须将首个 Task 设为"补齐复现与验收"

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

## 手动执行（Manual Execution）

本次 `fix` 只生成任务清单，不会执行任何代码变更。

### 1) 选择要执行的 Task
打开本次生成的 Tasks 文件：
- `docs/tasks/YYYY-MM-DD-HH-MM-<fix-name>.md`

从中挑选一个 `Task ID`（建议从 1 开始，按顺序推进）。

### 2) 调用 Execute 技能执行该 Task
在 ClaudeCode 中运行：

```bash
/execute docs/tasks/YYYY-MM-DD-HH-MM-<fix-name>.md:<task-id>
```
