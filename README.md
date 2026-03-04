# AI Governance Skills

通用 AI 治理技能库，通过结构化的 Skills 和 Templates 建立可追溯、可审计的开发流程。

---

## 功能特性

### 四层治理流程

```
design → feat → tasks → execute
   ↓       ↓       ↓        ↓
全局设计 → 功能规格 → 任务清单 → 执行记录

discover（需求识别）← 入口
fix（修复）可独立创建修复任务
```

### 核心能力

- **Discover** — 需求识别与路径判定，支持 4 种场景：DIRECT（直接回答）/ DESIGN（新能力）/ FEAT（功能增强）/ FIX（错误修复）
- **Design Baseline** — 生成项目级设计文档，作为单一事实来源
- **Feature Specification** — 将需求拆解为功能规格和可执行任务
- **Fix Workflow** — Bug 修复闭环：复现 → 修复 → 回归
- **Controlled Execution** — Scope 受控的变更执行与审计记录

### 治理机制

- **编号与引用体系** — 跨文档的可追溯引用（G/NG/D/AC/R/Q；FR/NFR/TC/IP/DEP）
- **冲突检测** — 自动检查历史执行记录，避免重复实现与回归
- **Scope 控制** — 明确的允许/禁止范围，防止范围蔓延
- **验收标准三要素** — Outcome / Metric-Threshold / Measurement

---

## 安装

### Claude Code 安装

在 Claude Code 中注册 Marketplace

```bash
/plugin marketplace add yakumioto/governance-skills
```

在 Marketplace 中安装 governance-skills 插件

```bash
/plugin install governance-skills@yakumioto-marketplace
```

### 验证安装

应能看到以下 5 个技能：
- `discover` — 需求识别与路径判定
- `design` — 生成全局设计文档
- `feat` — 功能开发流程
- `fix` — 小变更/修复
- `execute` — 执行任务

---

## 使用方法

### 0. 需求识别

```bash
/discover <需求描述>
```

分析需求意图，判定进入 DIRECT / DESIGN / FEAT / FIX 流程：

| 场景 | 说明 |
|------|------|
| DIRECT | 用户意图是了解现状，直接回答问题 |
| DESIGN | 新增能力，需要先做全局设计 |
| FEAT | 现有能力增强 |
| FIX | 错误修复 |

### 1. 创建设计文档

```bash
/design <项目需求>
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
/execute docs/tasks/YYYY-MM-DD-HH-MM-<name>.md:<task-id>
```

执行指定任务并记录可审计的执行证据。

---

## 文档结构

```
governance-skills/
├── skills/              # 技能定义
│   ├── discover/       # 需求识别技能
│   ├── design/        # 设计技能
│   ├── feat/          # 功能技能
│   ├── fix/           # 修复技能
│   └── execute/       # 执行技能
├── templates/          # 文档模板（只读）
│   ├── design.md
│   ├── feature.md
│   ├── tasks.md
│   └── execute.md
├── docs/               # 治理文档
│   ├── <timestamp>-design.md      # 设计文档
│   ├── features/                    # 功能文档
│   ├── tasks/                       # 任务清单
│   └── executes/                     # 执行记录
├── CLAUDE.md           # 项目指令
├── README.md           # 本文件
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

---

## 更新日志

### v0.3.1

- **Discover 扩展**：支持 4 种场景（DIRECT / DESIGN / FEAT / FIX），新增"了解现状"场景用于直接回答代码问题
