---
name: design
description: 使用 brainstorming 生成全局设计文档（Design Baseline）
---

# Design 技能

生成全局设计文档（Design Baseline），作为项目级“单一事实来源（Source of Truth）”，为后续 Feature/Tasks 提供约束与追溯依据。

---

## 允许操作范围（Hard Scope）
- **允许读取：**
  - `docs/*-design.md`
  - `docs/templates/design.md`                 # 优先：相对项目根目录
  - `../../templates/design.md`                # 优先：仓库内模板（相对当前 SKILL.md）
  - `$HOME/.claude/skills/templates/design.md` # 备用：全局模板目录（若 runner 支持 env 展开）
- **允许写入/修改：**
  - 仅允许创建 `docs/*-design.md`
- **禁止修改：**
  - 任何非 `docs/*-design.md`           #文件（包括但不限于治理文档、README、CLAUDE.md、AGENTS.md、SPEC.md、TASKS.md 等）
  - `docs/templates/*`                 # 项目模板文件只读
  - `../../templates/design.md`        # 仓库内模板（相对当前 SKILL.md）
  - `$HOME/.claude/skills/templates/*` # 全局模板目录只读

---

## 流程（Process）

### 1) 发现现有 Design
1. 查找 `docs/*-design.md`
2. **若存在多个：以文件名中的时间戳排序，取最新一份**
   - 约定文件名格式：`docs/YYYY-MM-DD-HH-MM-design.md`
   - 最新判定：按 `YYYY-MM-DD-HH-MM` 进行字典序排序即可

### 2) 读取最新 Design（如果存在）
- 提取关键信息作为“现状输入”：
  - 项目概述、Scope、Goals、Non-goals、Key Decisions、Constraints、Project-level AC、Risks、Open Questions、References
- 记录“上一版引用”：`Previous Design: <filename>`

### 3) Brainstorming（强制收敛）
**能力检查（Capabilities Check）：**
- 若执行需要具备命令运行/文件变更能力，先确认运行环境已启用所需能力（如：Superpowers）。
- 若未启用/不可用：必须停止执行变更，仍需生成 execute 文档：
  - Result=FAIL
  - Failure Reason：缺少执行能力（Superpowers 未安装/未启用）
  - Next Actions：提示用户安装/启用 Superpowers 后重试
  
```
请调用 /superpowers:brainstorm <需求描述>
```
等待 `/superpowers:brainstorm` 完成并获得需求摘要。

- 使用 brainstorming 进行发散，但输出必须**收敛到模板字段**：
  - `{{title}}`
  - `{{datetime}}`
  - `{{version}}`
  - `{{overview}}`
  - `{{architecture}}`
  - `{{references}}`
- brainstorming 产出必须回答：
  - 本次设计相对上一版的**变化点（Delta）**
  - 关键决策与权衡（Decision/Trade-offs）
  - 项目级验收标准（Project-level AC）：3–7 条，Outcome/Metric/Measurement 三要素

### 4) 汇总与成文（Template Fill）
- **只能使用** `templates/design.md` 生成文档
- 严格按模板章节输出，不新增/改名章节
- 语言：中文为主，关键术语括注英文（如 In scope / Non-goals / Acceptance Criteria / SLO / Rollback）

### 5) 写入新文档
- 生成新文件：`docs/YYYY-MM-DD-HH-MM-design.md`
- 在 References 中追加：
  - `Previous Design: docs/<last-design-filename>`（如存在）

---

## 输出（Output）
- 生成：`docs/YYYY-MM-DD-HH-MM-design.md`
- 内容必须完全由项目模板文件或全局模板目录中的 `templates/design.md` 渲染得到（字段填充），不得直接自由发挥成文。

---

## 失败处理（Failure Modes）
- 找不到项目模板、插件模板、全局模板目录中的`templates/design.md`：停止并报告错误（不得创建 design）
- 无法读取 docs：停止并报告错误（不得创建 design）
- 若信息不足以填充关键字段（title/overview/architecture）：允许在 overview/architecture 中写 “TBD”，并在 References 中记录“缺失信息清单”。
