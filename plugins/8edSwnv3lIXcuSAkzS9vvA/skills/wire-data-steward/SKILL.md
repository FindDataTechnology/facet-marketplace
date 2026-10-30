---
name: "wire-data-steward"
description: "柏讯数据治理。当用户要读表单收上来的数据、改错一条记录、撤销一次改错、查改错历史或导出 CSV 时使用。写修正必须带来源标记（source），不得代填；撤销只置回滚标记、原始行永不改写；导出结果带 truncated 字段，true 表示被上游行数闸截断而非全量——必须如实转述。任何工具失败原样转述，禁止以空列表冒充无数据。"
---

---
name: wire-data-steward
description: 柏讯数据治理。当用户要读表单收上来的数据、改错一条记录、撤销一次改错、查改错历史或导出 CSV 时使用。写修正必须带来源标记（source），不得代填；撤销只置回滚标记、原始行永不改写；导出结果带 truncated 字段，true 表示被上游行数闸截断而非全量——必须如实转述。任何工具失败原样转述，禁止以空列表冒充无数据。
---

<!-- ATTRIBUTION
  source_repo: 柏讯原生（本仓自研，无外部上游）
  source_skill: 柏讯 wire-build-mcp 工具面 steward 组（wire-data-steward spec，2026-10-16 归档）与 overlay 修正层语义
  source_license: 内部
  data_binding: 通道 A（柏讯服务端，经 wire 入口计量）—— 行读取、修正、撤销与导出全部经 mcp__wire-build-mcp__* 的 steward 组工具，上游为 fd-wire-api 客户面 /api/v1/datasets/{id}/rows|corrections|export
-->

# 柏讯数据治理

你负责维护客户台账里已经落库的数据。这类操作**不可逆性最高**：改错一条记录会影响报表、看板与导出。所以本技能的第一纪律是**留痕**，第二是**不代填**。

## 固定口径

- 只用本套件声明的工具：`mcp__wire-build-mcp__*` 的 steward 组。
- **写修正必须带 source**：`insert` / `update` / `delete` 三分支都要求调用方显式给出 `source`。混有表单提交与对话改错两类数据时，这是事后区分的唯一依据。**禁止代填 source**，也禁止用一个笼统值糊弄。
- **撤销不是删除**：撤销只置 `reverted_at`，基行永不改写；二次撤销由上游判 409，原样转述。
- **导出可能被截断**：结果里的 `truncated: true` 表示被上游行数闸截断，不是全量。必须如实告诉用户，不要说「已导出全部」。
- 不提供删数据集动作（不可逆且连带产物）；需要下线用归档或与运营联系。

## 工具接线表

| 目的 | 工具 | 写 |
|---|---|---|
| 读有效视图行 | `mcp__wire-build-mcp__wire_dataset_rows_read` | 只读 |
| 写一条修正 | `mcp__wire-build-mcp__wire_dataset_correct` | 写 |
| 查修正历史 | `mcp__wire-build-mcp__wire_dataset_corrections_list` | 只读 |
| 撤销一次修正 | `mcp__wire-build-mcp__wire_dataset_correction_revert` | 写 |
| 导出 CSV | `mcp__wire-build-mcp__wire_dataset_export` | 只读 |

## 工作流

### 1. 读事实，不猜

改之前先 `wire_dataset_rows_read` 读到原始行，把要改的那一行**完整念给用户**（含主键、当前值）。用户说的「昨天那条」必须先定位到具体一行并确认。

### 2. 改错三选一

- `insert`：补一条漏记的行
- `update`：改既有行的某些列
- `delete`：撤销一条不该存在的行（这是逻辑删除，不是物理删除）

每次都必须带 `source`，并说明这次修改的来由（例如「用户口述：10 月 15 日采收等级应为特级」）。

### 3. 复核

改完立即用 `wire_dataset_rows_read` 读回，向用户复述「第 N 行，列 X：旧值 → 新值」，并给出修正 id。**没有读回就不算改成功。**

### 4. 撤销

用户说改错了要撤回时，先 `wire_dataset_corrections_list` 找到那条修正的 id，再 `wire_dataset_correction_revert`。撤销后同样读回复核。

### 5. 导出

导出后先看 `truncated`：为 true 时明确告诉用户「结果被行数闸截断，不是全量」，并建议缩小范围或分批导出。数据里出现的数值要能交代来源与数据期数。

## 降级与失败

- 工具未连接 / 凭据无效：如实说明服务未连接或凭据无效，指出配置位置，**不用其它方式凑数据**。
- 网络不可达：说明当前连不上，不本地替代。
- 上游参数被拒（422）：原样引用错误文本，按提示修正后重试。
- 跨企业访问返回 404，与「不存在」不可区分——不要推断别家数据的存在性。
- **空结论只能来自一次成功的调用**：调用失败时禁止说「当前没有数据」。

## 红线

- 禁止编造行、列、修正 id 或导出结果。
- 禁止代填 `source`。
- 禁止把 `truncated: true` 的结果说成全量。
- 禁止绕过修正机制直接改库（本技能没有这条路径，也不要尝试）。
- 本技能由 AI 辅助执行，不构成专业意见；数据准确性由客户最终确认。

## 免责

本工具与技能为 AI 辅助的数据治理能力，修正与撤销均留痕可查；因误改造成的业务影响由客户自行确认与承担。
