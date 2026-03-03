---
name: design
description: 在 discover 已完成路径判定后，生成全局设计文档（Design Baseline）
---

# Design — 全局设计基线生成

本 Skill 负责：
- 承接 `discover` 已确认的 DESIGN 类需求
- 生成新的全局设计文档（Design Baseline）
- 作为后续 Feature / Tasks 的项目级单一事实来源（Source of Truth）

<HARD-GATE>
本 Skill 仅可在 `discover` 已输出 `NEXT_STATE: DESIGN` 后触发。

执行过程中：
- 禁止重新进行需求分流
- 禁止重复执行 discover 职责
- 禁止生成实现代码
- 禁止生成 patch / diff / PR / git 命令
- 禁止描述函数级或行级修改步骤
- 禁止生成除 `docs/*-design.md` 外的任何文件
- 禁止运行会改变工作区的命令
- 禁止调用其他技能
</HARD-GATE>

<HARD-SCOPE>
允许读取：
- `docs/*-design.md`              # 最新全局设计基线
- `docs/features/*.md`            # 既有功能规格，只读
- `docs/tasks/*.md`               # 既有任务文档，只读
- `docs/executes/*.md`            # 既有执行记录，只读
- `templates/design.md`           # 插件 Design 模板

允许写入：
- `docs/*-design.md`              # 项目设计文档
</HARD-SCOPE>

---

## 输入前提（Preconditions）

触发本 Skill 前，必须满足：

- 已完成 `discover`
- `discover` 输出结果为：

```text
NEXT_STATE: DESIGN
```

---

## 流程（Process）

### 1) 汇总当前设计输入
- discover 已确认的需求意图
- 可读取的模板字段要求

### 2) 按模板生成文档
- 必须且只能使用 `templates/design.md` 生成文档
- 严格按模板章节输出
- 不新增章节
- 不改名章节
- 不跳过关键字段
- 语言以中文为主，关键术语可括注英文

### 3) 写入新文档
- 生成新文件：`docs/YYYY-MM-DD-HH-MM-design.md`
- 在 References 中追加：
  - `Previous Design: docs/<last-design-filename>`（如存在）

---

## 输出（Output）
- 生成：`docs/YYYY-MM-DD-HH-MM-design.md`

要求：
- 内容必须由模板 `templates/design.md` 渲染得到
- 仅允许字段填充，不允许脱离模板自由发挥
- 文档必须可作为后续 feat / task / execute 的上游依据

---

## 失败处理（Failure Modes）
- 找不到 `templates/design.md`：停止并报告错误（不得生成文档）
- 找不到可引用的设计文档：仍可生成，但需说明"首次设计，无历史引用"

---

## 文档内容要求（Design Requirements）

生成的 Design Baseline 至少应覆盖：

- 本次设计的目标与背景
- 项目级范围边界
- 非目标（明确不做什么）
- 核心架构或核心设计思路
- 关键决策与权衡
- 风险与约束
- 项目级验收标准
- 未决问题
- 与上一版 design 的关系

---

## 终态（End State）

终态不是实现，不是任务拆分，也不是继续追问。

终态是：
```text
OUTPUT: docs/YYYY-MM-DD-HH-MM-design.md
STATE: DESIGN_BASELINE_READY
```
