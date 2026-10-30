---
name: "wire-artifact-build"
description: "柏讯产物构建。当用户要用对话把数据变成柏讯里看得见的东西——建看板（dashboard）、建正文文档（doc）、建数据录入表单（form）、建单记录扫码页（record-view）、给已有产物改 spec、发布或归档产物——时使用。所有写入一律落草稿，发布必须是独立的第二次显式调用；spec 引用的列名必须先用工具取真实列清单核对，不得凭记忆编造；上游拒绝时原样转述错误，不伪造成功。"
---

---
name: wire-artifact-build
description: 柏讯产物构建。当用户要用对话把数据变成柏讯里看得见的东西——建看板（dashboard）、建正文文档（doc）、建数据录入表单（form）、建单记录扫码页（record-view）、给已有产物改 spec、发布或归档产物——时使用。所有写入一律落草稿，发布必须是独立的第二次显式调用；spec 引用的列名必须先用工具取真实列清单核对，不得凭记忆编造；上游拒绝时原样转述错误，不伪造成功。
---

<!-- ATTRIBUTION
  source_repo: 柏讯原生（本仓自研，无外部上游）
  source_skill: 柏讯 wire-build-mcp 工具面纪律（tool-surface.json 的 build 组）与 artifact-lifecycle / form-apps / record-view 三份 spec
  source_license: 内部
  data_binding: 通道 A（柏讯服务端，经 wire 入口计量）—— 产物与数据集事实全部经 mcp__wire-build-mcp__* 工具读写，上游为 fd-wire-api 客户面 /api/v1/artifacts*|datasets*，与柏讯控制台同源
-->

# 柏讯产物构建

你负责把用户口述的东西变成柏讯控制台里真实存在的产物。产物是客户域内的一等实体，会跨会话保存、带版本链、可分享——**所以每一次写入都要当正式交付对待**。

## 固定口径

- 只用本套件声明的工具：`mcp__wire-build-mcp__*`。平台工具面之外的名字不存在、不可调用。
- **写入落草稿，发布独立**：创建与更新工具永远只产生 draft。把产物变 published 是单独一次 `wire_artifact_publish` 调用，用户明确同意后才做。
- **列名先取事实**：任何 spec 里出现的列名，必须先调 `wire_dataset_schema` 取该数据集真实列清单。禁止凭记忆或推测写列名。
- **失败原样转述**：上游返回的错误文本原样引用，并给出四分类（凭据无效 / 网络不可达 / 上游错误 / 参数被拒）。禁止把失败说成成功，禁止用空结果冒充「没有数据」。

## 工具接线表

| 目的 | 工具 | 写 |
|---|---|---|
| 取数据集列名与类型 | `mcp__wire-build-mcp__wire_dataset_schema` | 只读 |
| 列已有产物 | `mcp__wire-build-mcp__wire_artifact_list` | 只读 |
| 读单个产物与其 spec | `mcp__wire-build-mcp__wire_artifact_get` | 只读 |
| 建数据集（CSV 导入 / 列声明） | `mcp__wire-build-mcp__wire_dataset_create` | 写 |
| 建看板 | `mcp__wire-build-mcp__wire_artifact_dashboard_create` | 写（草稿） |
| 建正文文档 | `mcp__wire-build-mcp__wire_artifact_doc_create` | 写（草稿） |
| 建数据录入表单 | `mcp__wire-build-mcp__wire_artifact_form_create` | 写（草稿） |
| 建单记录扫码页 | `mcp__wire-build-mcp__wire_artifact_record_view_create` | 写（草稿） |
| 改产物 spec | `mcp__wire-build-mcp__wire_artifact_update` | 写（追加版本） |
| 发布 | `mcp__wire-build-mcp__wire_artifact_publish` | 写（状态迁移） |
| 归档 / 恢复 | `mcp__wire-build-mcp__wire_artifact_archive` / `__wire_artifact_restore` | 写（状态迁移） |

## 工作流

### 1. 先把「要记什么」问清楚

建表单或台账前，必须先确定：这张表记什么、每一列是什么、哪些列必填、谁在什么时候填。**列没定清就开始写 spec 是错的**——用户看到的第一版表单会变成他们长期使用的结构。

### 2. 取真实列事实

```
wire_dataset_schema(dataset_id) -> 列名 + 类型清单
```

数据集不存在时先建：免费档走 CSV 导入（`wire_dataset_create` 传 CSV 内容），其余档位走列声明建空集。**绝不编造示例行**——空集就是空集，示例数据会污染客户的真实台账。

### 3. 生成 spec

- **dashboard**：`{title, spec: {title, panels: [{type: "kpi|line|bar|table", title, span?, source?, data: {columns, rows}, query?}]}}`。面板里的列名与数值必须来自真实查询结果，空数据就如实留空（`rows: []`），不编造。
- **doc**：`{title, spec: {format: "markdown", text: "<markdown 源文本>"}}`。`format` 恒为 `markdown`；正文上限 256 KB；写的是 markdown 源文本，不要塞预渲染 HTML。
- **form**：`{title, spec: {datasetId, fields: [...], constraints?: [...]}}`
- **record-view**：`{title, spec: {datasetId, keyColumn, headerFields: [{column, label?}], sections: [{title, datasetId, dateColumn, filters?, limit?}]}}`

字段类型、条件显隐、联动选项等结构规则以服务端 spec 校验为准；被拒时按错误文本修正，不要试错式反复提交同一个 spec。

### 4. 落草稿并复述

创建成功后向用户复述：产物 id、kind、当前状态（draft）、版本号，并说明「发布是下一步单独动作」。

### 5. 发布（需用户明确同意）

用户确认内容无误后，才调用 `wire_artifact_publish`。**发布端点只做状态迁移、不校验 spec 结构**——所以发布前的结构正确性由你负责。

## 数据口径与溯源

产物里出现的每一个数值，都必须能交代**来源与数据期数**（例如「数据截至 2026-10-20，来源：柏讯指标目录」）。看板与表单的标题或说明里应写明数据截至日期；**数据截至日期不得晚于数据本身的日期**——那是自相矛盾的标注。

## 降级与失败

- 工具未连接或凭据无效：如实说明「服务未连接 / 凭据无效」，给出去哪配置，不用别的方式凑一个产物。
- 网络不可达：说明当前连不上，不做本地替代。
- 上游参数被拒（422）：原样引用错误文本。若错误里带了数据集真实列名清单，按清单修正后再试。
- 补偿语义：创建失败时上游可能已留下一个被归档的空白产物，工具会在错误文本里给出它的 id——如实转述，不要隐瞒。

## 红线

- 禁止编造列名、字段、数据集或产物 id。
- 禁止跳过用户确认直接发布。
- 禁止一次调用里同时创建并发布。
- 不提供删除产物或删除数据集的动作；需要下线时用归档。
- 本技能由 AI 辅助生成与执行，不构成任何专业意见；产物内容的准确性由客户最终确认。

## 免责

本工具与技能为 AI 辅助的数据录入与产物构建能力，产物结构与数据口径由客户自行确认；因口径错误造成的业务影响不由平台承担。
