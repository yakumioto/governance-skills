# AI Governance Skills

通用 AI 治理技能库，通过结构化的 Skills 和 Templates 建立可追溯、可审计的开发流程。

---

## 功能特性

### 四层治理流程

```
design → feat → tasks → execute
   ↓       ↓       ↓        ↓
全局设计 → 功能规格 → 任务清单 → 执行记录

fix（修复）可独立创建修复任务
```

### 核心能力

- **Design Baseline** — 生成项目级设计文档，作为单一事实来源
- **Feature Specification** — 将需求拆解为功能规格和可执行任务
- **Fix Workflow** — Bug 修复闭环：复现 → 修复 → 回归
- **Controlled Execution** — Scope 受控的变更执行与审计记录

### 治理机制

- **编号与引用体系** — 跨文档的可追溯引用
- **冲突检测** — 自动检查历史执行记录，避免重复实现与回归
- **Scope 控制** — 明确的允许/禁止范围，防止范围蔓延
- **验收标准三要素** — Outcome / Metric-Threshold / Measurement

---

## 安装

### Claude Code 安装

在 Claude Code 中运行：

```bash
/plugin add https://github.com/yakumioto/governance-skills.git
```

### 验证安装

安装完成后，检查技能是否可用：

```bash
/ls skills
```

应能看到以下技能：
- `design` — 生成全局设计文档
- `feat` — 功能开发流程
- `fix` — 小变更/修复
- `execute` — 执行任务

---

## 使用方法

### 1. 创建设计文档

```bash
/design
```

生成项目级设计文档，定义 Goals、Constraints、Acceptance Criteria 等。

### 2. 功能开发

```bash
/feat <功能描述>
```

生成 Feature 文档和任务清单，自动进行冲突检查。

### 3. Bug 修复

```bash
/fix <问题描述>
```

生成修复任务，包含复现→修复→回归闭环。

### 4. 执行任务

```bash
/execute docs/task/YYYY-MM-DD-HH-MM-<name>.md:<task-id>
```

执行指定任务并记录可审计的执行证据。

---

## 文档结构

```
governance-skills/
├── skills/              # 技能定义
│   ├── design/         # 设计技能
│   ├── feat/           # 功能技能
│   ├── fix/            # 修复技能
│   └── execute/        # 执行技能
├── templates/          # 文档模板（只读）
│   ├── design.md
│   ├── feature.md
│   ├── tasks.md
│   └── execute.md
└── .claude-plugin/     # 插件元数据
    ├── plugin.json
    └── marketplace.json
```

---

## 跨平台兼容

此技能库设计为跨 AI 平台使用：

| 平台 | 状态 |
|------|------|
| Claude Code | 原生支持 |
| GitHub Copilot | 待适配 |
| Cursor | 待适配 |
| 通用 CLI | 待适配 |

平台适配层只需处理技能调用入口、文件权限、环境检测等，核心流程与数据结构保持不变。

---

## 许可证

MIT License

---

## 贡献

欢迎提交 Issue 和 Pull Request！
