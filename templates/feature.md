# 功能文档（Feature）：{{title}}

**创建时间（Created）：** {{datetime}}  
**关联设计（Design Links）：** {{design_refs}}（例如：Design:xxx，[G1]/[D2]/[AC1]）

---

## 写作约束（Writing Rules）
- **必须严格按本模板分节输出**，不要新增/合并/改名章节。
- 需求/验收/风险等条目必须编号：`[FR1] [NFR1] [AC1] [TC1] [R1]`。
- **实现计划只到“步骤与修改点”粒度**：不贴大段代码；代码细节在 PR/commit。
- **验收标准（AC）必须可验证**：Given/When/Then 或 指标/阈值/观察点。
- 每节最多 7 条；超出请拆分为多个 Feature。

---

## 1. 目标（Goal）
- **一句话目标：** {{goal}}
- **覆盖设计项（Traceability）：** 关联 [G?] / 受 [D?] 约束 / 支撑 [AC?]（项目级）

---

## 2. 背景与范围（Context & Scope）
### 2.1 背景（Context）
- ...

### 2.2 范围内（In scope）
- ...

### 2.3 范围外（Out of scope）
- ...

### 2.4 假设（Assumptions）
- ...

---

## 3. 需求（Requirements）
### 3.1 功能需求（Functional Requirements）
- [FR1] ...
- [FR2] ...

### 3.2 非功能需求（Non-functional Requirements, NFR）
- [NFR1] 性能：...
- [NFR2] 可靠性：...
- [NFR3] 安全/权限：...
- [NFR4] 兼容性：...

---

## 4. 设计（Design）
### 4.1 方案概述（Approach）
{{design}}

### 4.2 交互 / 流程（User Flow / Sequence，可选）
- ...

### 4.3 接口与契约（Interfaces / Contracts）
> 参数名、配置键、API path、schema 字段名保持英文原样。
- **API/CLI：** ...
- **配置（Config）：** ...
- **数据结构（Schema）：** ...
- **错误码/异常（Errors）：** ...

### 4.4 权限与审计（AuthZ & Audit，可选）
- ...

---

## 5. 验收标准（Acceptance Criteria）
> Feature 级验收：写可测的行为与边界；必要时引用项目级 [AC?]。

- [AC1] **Given** ... **When** ... **Then** ...
- [AC2] ...
- [AC3] ...

---

## 6. 测试计划（Test Plan）
- [TC1] 单元测试（Unit）：覆盖 FR1/边界条件...
- [TC2] 集成测试（Integration）：调用链/依赖交互...
- [TC3] 回归点（Regression）：可能受影响的旧行为...
- [TC4] 手工验证（Manual，可选）：步骤与预期结果...

---

## 7. 实现计划（Implementation Plan）
> 粒度建议：按“变更点/模块/文件/步骤”拆分；每步可独立提交（Conventional Commits）。

- [IP1] 修改点：...（模块/文件：...）  
  输出：... / 风险：...

- [IP2] ...
- [IP3] ...

### 7.1 迁移与发布（Migration & Rollout，可选）
- 灰度（Canary）：...
- 回滚（Rollback）：...
- 数据迁移（Migration）：...

---

## 8. 依赖与阻塞（Dependencies & Blockers）
- [DEP1] 依赖：...（版本/接口/负责人）
- [DEP2] ...
- [BLK1] 阻塞：...（需要谁确认/需要什么条件）

---

## 9. 风险与应对（Risks & Mitigations）
- [R1] 风险：... → 应对：...
- [R2] ...

---

## 10. 参考资料（References）
{{references}}