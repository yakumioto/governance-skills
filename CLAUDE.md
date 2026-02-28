# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

---

## 项目概述（Project Overview）

这是一个 **AI 治理技能库（AI Governance Skills Library）**，用于规范和管理 AI 辅助开发过程中的文档生成与任务执行。核心是通过结构化的技能（Skills）和模板（Templates），建立可追溯、可审计的开发流程。

**项目类型：** 通用 AI 技能库（Universal AI Skills Library）
**仓库地址：** https://github.com/yakumioto/governance-skills

**设计目标：**
- 跨 AI 平台兼容（不仅限于 Claude Code）
- 可作为插件独立使用或嵌入其他工具
- 标准化的文档与工作流模板

---

## 核心架构（Core Architecture）

### 四层流程体系

```
design → feat → tasks → execute
   ↓       ↓       ↓        ↓
全局设计 → 功能规格 → 任务清单 → 执行记录
```

### 技能系统（Skills System）

位于 `skills/` 目录，每个技能定义了明确的 Hard Scope 和执行流程：

| 技能 | 用途 | 输出文件 | 命名格式 |
|------|------|----------|----------|
| `design` | 生成全局设计文档 | `docs/*-design.md` | `YYYY-MM-DD-HH-MM-design.md` |
| `feat` | 生成 Feature Spec + Tasks | `docs/feat/*.md` + `docs/task/*.md` | `YYYY-MM-DD-HH-MM-<feat-name>.md` |
| `fix` | 生成修复 Tasks | `docs/task/*.md` | `YYYY-MM-DD-HH-MM-<fix-name>.md` |
| `execute` | 执行 Task 并记录 | `docs/execute/*.md` | `YYYY-MM-DD-HH-MM-<task-name>-<task-id>.md` |

### 模板系统（Templates System）

位于 `templates/` 目录，定义了文档的标准结构：
- `design.md` — 项目级设计文档模板
- `feature.md` — 功能文档模板
- `tasks.md` — 任务清单模板
- `execute.md` — 执行记录模板

**重要约束：** 模板文件（`templates/*`）为只读，任何技能不得修改模板。

---

## 编号与引用体系（Numbering & Traceability）

### 标准编号格式

| 类型 | 格式 | 示例 |
|------|------|------|
| Goals | `[G1]` / `[G2]` | 项目级目标 |
| Non-goals | `[NG1]` / `[NG2]` | 非目标 |
| Decisions | `[D1]` / `[D2]` | 关键决策 |
| Constraints | `[C1]` / `[C2]` | 约束条件 |
| Project-level AC | `[AC1]` / `[AC2]` | 项目级验收标准（三要素：Outcome/Metric/Measurement） |
| Risks | `[R1]` / `[R2]` | 风险 |
| Open Questions | `[Q1]` / `[Q2]` | 未决问题 |

### Feature 层级编号

| 类型 | 格式 | 示例 |
|------|------|------|
| Functional Requirements | `[FR1]` / `[FR2]` | 功能需求 |
| Non-functional Requirements | `[NFR1]` / `[NFR2]` | 非功能需求 |
| Feature-level AC | `[AC1]` / `[AC2]` | Feature 验收标准 |
| Test Cases | `[TC1]` / `[TC2]` | 测试用例 |
| Implementation Plan | `[IP1]` / `[IP2]` | 实现步骤 |
| Dependencies | `[DEP1]` / `[DEP2]` | 依赖 |

### 引用规则

- 每层文档必须显式引用其父层的设计决策
- 格式：`[G1]` `[D2]` `[AC1]`（空格分隔，多引用时合并）
- References 章节必须包含完整引用链

---

## 受保护目录（Protected Directories）

以下目录的文件受严格访问控制：

| 目录 | 创建权限 | 更新权限 | 删除权限 |
|------|----------|----------|----------|
| `docs/spec/` | feat 流程 | 禁止 | 禁止 |
| `docs/tasks/` | feat 流程 | execute (ER 更新) | 禁止 |
| `docs/changes/` | fix 流程 | execute (ER 更新) | 禁止 |

**注：** ER = Execution Record（执行记录）

**规则：**
- ❌ 用户或 AI 不得直接创建、修改、删除受保护目录的文件
- ✅ 只能通过 fix/feat 流程创建
- ✅ 只能通过 execute 更新 Execution Record
- ✅ Read 操作不受限

---

## Scope 定义模式

每个 Task 必须包含明确的 Scope 定义：

```
Allowed:      # 允许修改的白名单
Forbidden:    # 禁止修改的黑名单
Out of scope: # 明确不在本任务范围的内容
```

推荐写法（目录/文件级别）：
- Allowed: `internal/foo/**`, `cmd/bar/**`, `configs/app.yaml`
- Forbidden: `go.mod`, `docs/*-design.md`, `templates/**`

---

## 验收标准三要素（AC Triple）

**项目级 AC 必须包含：**

```
结果（Outcome）：
指标/阈值（Metric/Threshold）：
度量方式（Measurement）：
```

示例：
```
结果：系统吞吐量满足生产负载
指标/阈值：>= 1000 QPS，P99 < 100ms
度量方式：压测工具（如 k6）持续运行 10 分钟
```

---

## 变更边界（Change Boundaries）

### Fix 定义

**允许：**
- Bug 修复、崩溃修复、逻辑错误
- 性能回退修复
- 兼容性修复
- 补充测试/日志（仅定位与验证）

**禁止：**
- 新增功能、需求扩展（应走 feat）
- 大规模重构（应走 feat/design）
- 变更公共接口契约（除非向后兼容修复）

### Fix 闭环

Fix Tasks 必须包含三步：
1. **复现与定位** — 增加最小复现/测试/日志
2. **修复实现** — 最小变更
3. **回归与加固** — 补测试/检查点

极小问题可合并，但验收链条必须完整。

---

## 冲突检查（Conflict Detection）

Feat 和 Fix 技能必须读取历史 Execute 记录进行冲突检查：

**相关性匹配（满足任一即相关）：**
1. execute 的 `关联条目（Refs）` 含相同关键词/编号
2. execute 的 `Files Changed` 涉及同一模块/文件
3. execute 的 FAIL/回滚/Notes 指向同一现象

**冲突判定（出现任一则标记冲突）：**
- 已有 PASS 的 execute 覆盖同一 FR/AC（疑似重复实现）
- 存在未完成/blocked 的相关 Task，且再次覆盖同一 FR/AC（疑似重复拆分）
- execute 显示某模块近期变更且存在 FAIL/回滚（高风险）
- 接口/契约字段名或行为在历史 execute 中已固定，与设计不一致

---

## 命名规范（Naming Conventions）

### 文件命名

- **时间戳格式：** `YYYY-MM-DD-HH-MM`（24 小时制）
- **kebab-case：** 小写英文/数字/短横线
- **示例：**
  - `2026-02-27-15-30-governance-system-design.md`
  - `2026-02-27-15-30-authz-audit-log.md`
  - `2026-02-27-15-30-fix-panic-on-empty-input.md`

### 技能调用

- `/design` — 生成全局设计文档
- `/feat <需求>` — 功能开发流程
- `/fix <需求>` — 小变更/修复
- `/execute fix <file>` — 执行 fix Change
- `/execute feat <task-id>` — 执行 feat Task

---

## 插件元数据（Plugin Metadata）

此项目是一个通用 AI 技能库，元数据定义在 `.claude-plugin/plugin.json`：

```json
{
  "name": "governance-skills",
  "version": "0.1.0",
  "description": "Governance skills library for AI coding agents",
  "keywords": ["skills", "governance", "workflow", "design", "planning", "execution", "auditing"]
}
```

---

## 技能内部流程（Skill Internal Workflows）

### Design 技能流程

1. 发现并读取最新的 Design 文档（按时间戳排序）
2. 调用 brainstorming 进行需求分析
3. 使用 `templates/design.md` 生成新文档
4. 输出到 `docs/YYYY-MM-DD-HH-MM-design.md`

### Feat 技能流程

1. 读取最新 Design
2. 读取相关 Feature/Tasks 文档
3. **执行冲突检查**（读取相关 Execute 记录）
4. 调用 brainstorming
5. 生成 Feature 文档（使用 `templates/feature.md`）
6. 生成 Tasks 文档（使用 `templates/tasks.md`）

### Fix 技能流程

1. 读取最新 Design
2. 读取相关 Feature/Tasks 文档
3. **执行冲突检查**（读取相关 Execute 记录）
4. 调用 brainstorming
5. 生成 Tasks 文档（必须包含复现→修复→回归闭环）

### Execute 技能流程

1. 读取指定的 Task 文档
2. 提取 Scope 和 Acceptance 信息
3. 生成环境快照
4. **执行 Scope 强制检查**
5. 按 Scope 执行变更
6. 运行验收命令
7. 生成 Execute 文档（使用 `templates/execute.md`）

---

## 能力检查（Capabilities Check）

所有技能在执行变更前必须进行能力检查：

- 若执行需要具备命令运行/文件变更能力，先确认运行环境已启用所需能力
- 若未启用/不可用：必须停止执行变更，仍需生成文档标记为 FAIL，提示用户安装/启用相应能力后重试

---

## 跨平台兼容性（Cross-Platform Compatibility）

此技能库设计为跨 AI 平台使用，以下为平台适配建议：

### 平台特定处理

| 平台 | 需要适配的组件 | 备注 |
|------|----------------|------|
| Claude Code | `/command` 调用、Skill 工具 | 原生支持 |
| GitHub Copilot | 工作流文件、AI 助手提示 | 需适配调用方式 |
| Cursor | `.cursorrules`、指令文件 | 需适配调用方式 |
| 通用 CLI | 脚本包装器 | 需自行实现 |

### 通用接口契约

技能内部定义的流程与数据结构是平台无关的：
- 模板文件结构
- 编号引用体系
- Scope 定义模式
- 冲突检查规则

平台适配层只需处理：
1. 技能调用入口
2. 文件读写权限
3. 环境能力检测
4. 输出格式渲染
