# 执行记录（Execute）：{{title}} - Task {{task-id}}

**创建时间（Created）：** {{datetime}}  
**任务来源（Task Source）：** {{task-file}}  
**状态（Status）：**  pass | fail（通过｜失败）

---

## 0. Traceability（可追溯引用）
- **关联条目（Refs）：** {{refs}}  
  - 示例：`Feature:xxx [FR1][AC2]`；`Design:yyy [D3][AC1]`
- **本次对应任务验收（Task Acceptance）：** {{task-acceptance-ids}}（如：Task1.AC / [TC1]）

---

## 1. References（参考资料）
{{references}}

---

## 2. Scope（范围）
{{scope}}

**Out of scope（不在范围）：**
- ...

---

## 3. Environment（环境信息）
- **Workspace/Repo：** {{repo}}
- **Branch：** {{branch}}
- **Base Commit：** {{base-commit}}
- **Working Tree：** clean | dirty（是否有未提交修改）
- **OS：** {{os}}
- **Runtime/Toolchain：** {{toolchain}}（如 Go/Node/Python 版本）
- **Key Config：** {{key-config}}（影响结果的关键配置，敏感信息打码）

---

## 4. Commands Run（执行命令）
> 按执行顺序记录；必要时标注耗时（Duration）。

1. `{{command1}}`  （Duration: {{dur1}}）
2. `{{command2}}`  （Duration: {{dur2}}）

---

## 5. Assertions（关键断言/检查点）
> 说明“这些命令要证明什么”，对应 Task 的 Acceptance。

- [A1] ...
- [A2] ...

---

## 6. Result（结果）
**PASS / FAIL：** {{result}}  
**Failure Reason（如 FAIL）：** {{failure-reason}}

---

## 7. Output Summary（输出摘要）
{{summary}}

---

## 8. Artifacts & Evidence（产物与证据）
- **PR：** {{pr-link}}
- **Commits：**
  - {{commit1}}
  - {{commit2}}
- **Files Changed（摘要）：** {{files-changed}}
- **Logs/Screenshots：**
  - {{evidence1}}
  - {{evidence2}}

---

## 9. Rollback Plan（回滚/恢复）
- **How to rollback：** ...
- **Data impact（如有）：** ...

---

## 10. Notes / Next Actions（备注 / 下一步）
{{notes}}

**Next Actions：**
- [N1] ...
- [N2] ...