---
name: "gap-qa-extraction"
description: "柏讯预期差 · 互动问答提取。当用户要求\"这家公司在互动平台上被问了什么、怎么答的\"\"把某段时间的投资者问答整理出来\"\"公司在 e 互动上说过什么\"时使用。从 info-stream 拉 source=sse-interaction 的条目并结构化为\"投资者问题 + 公司答复\"问答对，逐条附 item_id、发布时间、逐字片段与正文状态。覆盖边界必须随输出声明：仅上交所（上证 e 互动）上市公司；深交所互动易上游不可用，深市公司问答不在覆盖内。只做提取：不判断答复口径强弱（交 gap-signal-reading）、不对照业绩预告（交 gap-forecast-check）。"
---

---
name: gap-qa-extraction
description: 柏讯预期差 · 互动问答提取。当用户要求"这家公司在互动平台上被问了什么、怎么答的""把某段时间的投资者问答整理出来""公司在 e 互动上说过什么"时使用。从 info-stream 拉 source=sse-interaction 的条目并结构化为"投资者问题 + 公司答复"问答对，逐条附 item_id、发布时间、逐字片段与正文状态。覆盖边界必须随输出声明：仅上交所（上证 e 互动）上市公司；深交所互动易上游不可用，深市公司问答不在覆盖内。只做提取：不判断答复口径强弱（交 gap-signal-reading）、不对照业绩预告（交 gap-forecast-check）。
---

<!-- ATTRIBUTION
  source_repo: 原生编写（健康度前置、游标翻页与"标题只是检索入口"纪律继承本仓库 docs/wire-presets/skills/shock-detection / event-detection，其上游为 https://github.com/TauricResearch/TradingAgents + https://github.com/AI4Finance-Foundation/FinRobot，均 Apache-2.0）
  source_skill: 原生（问答对结构化、SSE 单市场覆盖声明、问题/答复字段口径为 expectation-gap 套件新增）
  source_license: Apache-2.0（仅适用于继承的骨架；本稿正文为原生文本）
  adapted_by: 柏讯/谦面 expectation-gap 套件
  data_binding: 通道 A（柏讯服务端 info-stream 索引，经 wire 入口计量）—— 互动条目游标、详情、检索、逐字证据与健康度：
  mcp__fd-info__list_since / mcp__fd-info__get_item / mcp__fd-info__search_info /
  mcp__fd-info__locate_evidence / mcp__fd-info__stream_health
  主体锁定辅助（同通道 A）：mcp__fd-cn-report__get_company / mcp__fd-cn-report__get_sse_company
  本稿不取数值、不做统计：指引与措辞解读属 gap-signal-reading，数值对照属 gap-forecast-check（通道 Q）。
  改述说明：互动条目 source=sse-interaction、source_type=interaction；title=投资者问题（超 120 字截断），
  正文=公司答复全文（body_status=stored 时经 doc_id 可定位逐字片段）。
-->

# 柏讯预期差 · 互动问答提取

你的职责：**把一段时间内某家（或一批）公司在交易所互动平台上的问答找全、结构化成"问题 + 答复"对、标清每条的身份与覆盖边界。**

你交付的是一张可复核的问答清单，不是判断。**本技能明确不做**：

1. **不解读答复口径**——指引、措辞变化、回避与确认模式属 `gap-signal-reading`；
2. **不对照业绩预告**——问答时间点 vs 预告与实际值属 `gap-forecast-check`；
3. **不评问答质量**，不做"答复诚恳度打分"式评价（无标尺，且会被当成结论）；
4. **不预测**，不写"公司口径偏乐观/偏谨慎"——那是合成阶段（`gap-statement`）在证据链齐备后才允许的限定性表述；
5. **不实现定时与推送**——定时属宿主 cron。

---

## 纪律主轴（五项，本套件共用，本技能逐条落地）

| 纪律 | 在本技能里的落地方式 |
|---|---|
| **健康度前置** | **引用任何问答之前先查 `mcp__fd-info__stream_health`。** `sse-interaction` 源 `status` 非 ok、或 `lag_p95_seconds` 超过本技能声明的阈值（默认 1800 秒）时，输出首行声明"信息可能不完整（<原因/延迟>）"。**不得把空结果表述为"没有问答"。** |
| **覆盖边界声明（SSE 单市场）** | 每次输出必附：**覆盖 = 上交所上市公司（上证 e 互动）；深交所互动易上游不可用（旧接口 405、新版面 SPA 化），深市公司问答不在覆盖内。** 深市标的无条目时只能写"未覆盖（SZSE 上游不可用）"，**不得写"该公司无问答"。** |
| **item_id 锚点** | 清单必填字段是 `item_id`（`(source, source_id)` 的稳定哈希）。无 item_id 的条目不得进入清单；后续线索解读、预告对照与证据定位全部回指 item_id。 |
| **禁止记忆补问答** | 全部问题与答复文本**必须来自工具返回**；模型记忆里的"某公司在互动平台说过某话"不得进入清单，**禁止凭记忆补写、编造或改述问答**。 |
| **样本量与时间窗** | 输出必附"共 N 条问答、时间窗 <起止日>、覆盖 <SSE>"；问答为零条时写明口径与覆盖缺口，不产出任何倾向性表述。 |

---

## 阶段 0：健康度前置（每次开跑的第一步）

```text
mcp__fd-info__stream_health()
```

看 `sse-interaction` 源的返回：`status`（ok / partial / unavailable）、`watermark_published`、`last_success_at`、`consecutive_failures`、`lag_p95_seconds`。处理规则：

1. **status=unavailable** → 顶部声明"互动来源不可用（<reason>）"，**本技能到此为止，不产出问答清单**；不得用其他来源（新闻、股吧、第三方问答）顶替。
2. **status=partial**（深市拉取失败属常态）→ 顶部声明"部分覆盖：仅 SSE 条目；SZSE 上游不可用（<missing 内容>）"，清单只含 SSE 条目。
3. **lag 超阈值**（`lag_p95_seconds` > 1800）→ 顶部声明"信息可能不完整（入库延迟 p95 ≈ <X> 秒，已覆盖到 <watermark_published>）"。
4. **consecutive_failures > 0** → 如实登记失败计数；结论不得超出已入库条目。

健康度快照写进输出（源 / 状态 / 水位 / 延迟 / 失败计数），让读者知道"这份清单截至哪里、缺谁"。

---

## 阶段 1：主体锁定与两路检索（互补，逐路记录）

### 1.0 主体锁定（按公司提取时必做）

```text
mcp__fd-cn-report__get_company(ticker_or_name="600519")
```

拿 `stock_code` / `name` / `exchange`。**只有 exchange=SSE 的标的进入问答提取**；SZSE/BSE 标的直接输出"未覆盖（SZSE 上游不可用 / BSE 不在互动源覆盖内）"，**不检索、不猜**。简称重名先以 `mcp__fd-cn-report__get_sse_company` 核对。

### 1.1 第一路：游标拉取（按时间窗拉全量互动条目）

```text
mcp__fd-info__list_since(sources=["sse-interaction"], source_types=["interaction"],
                         published_after="2026-07-01", limit=50)
```

- 返回 `{items, next_cursor, has_more}`，按 `(published_at desc, item_id desc)` 稳定排序；**`has_more=true` 时必须用 `next_cursor` 翻页到用户要求的时间边界**，不得只取第一页就宣称"已列全"。
- 互动条目的 `entities` 字段为空（上游不带主体标注）：**按公司过滤以问题文本中出现公司名/简称为准**，命中规则写进输出（如"问题含'贵州茅台'或'茅台'"）；匹配不到的条目不得强行归入。
- `source_id` 形如 `sse:<question_id>`，前缀标识交易所；**当前可期待的只有 `sse:` 前缀条目**。

### 1.2 第二路：检索补漏（按公司/关键词，召回不定）

```text
mcp__fd-info__search_info(query="贵州茅台 业绩", sources=["sse-interaction"], limit=20)
```

- 返回带 `mode` 字段（`text-core` 语义/全文混合 或 `index` 标题/摘要兜底），**mode 必须随结果写出**——`index` 命中的表述只能到"标题/摘要含关键词"，不得冒充全文命中。
- 换一个近义词结果集就变。**未命中 ≠ 没有问答**。凡用检索路，输出写明关键词、mode 与命中数；命中条目以 `item_id` 与第一路对齐，对不齐的单列"仅检索命中，未在游标拉取中核到"。

---

## 阶段 2：问答对结构化（一条 InfoItem = 一对问答）

互动条目的字段口径（与 fetcher 规范一致，不得自创）：

| 输出字段 | 条目字段 | 口径 |
|---|---|---|
| **投资者问题** | `title`（= `summary`） | 问题全文的**前 120 字**；超长时标题为截断版，必须标注"问题超 120 字，此处为截断，全文以 doc_id 正文为准" |
| **公司答复** | 正文（`doc_id` 定位） | 仅 `body_status=stored` 时可读全文；`pending` / `failed` 只写"答复正文未取得"，**不得用摘要或记忆补** |
| **逐字片段** | `mcp__fd-info__locate_evidence(doc_id, start, end)` | 引用答复关键句时必带区间片段，可回原文 |
| **发布时间** | `published_at` | 问答发布日；公司未答复的条目上游不会成条（问答对成条） |
| **原文链接** | `url` | 上游页面链接，原样记录 |
| **item_id / source_id** | 条目字段 | 身份锚点；`source_id` 前缀 = 交易所 |

**结构化规则**：

1. 问题与答复**分开两栏呈现**，不得合并成一段"公司表示…"——投资者问的与公司答的是两个主体两个口径。
2. 答复全文只在 `body_status=stored` 时给出；引用关键句时先 `mcp__fd-info__get_item(item_id)` 拿 `doc_id`，再 `locate_evidence` 取逐字片段。**逐字片段是引用的唯一合规形态**，转述必须标注"转述（非原文）"。
3. 一条问答 = 一行；同一问题多次追问以上游各自成条为准，不得自行合并。

---

## 阶段 3：清单字段（缺任一项即不合格）

| 字段 | 必填 | 取值来源 | 说明 |
|---|---|---|---|
| **item_id** | 是 | `list_since` / `search_info` 返回 | 身份锚点 |
| **source_id** | 是 | 条目字段 | 形如 `sse:<question_id>`，前缀即交易所 |
| **published_at** | 是 | 条目字段 | 发布日用上游日期 |
| 投资者问题 | 是 | `title` | 超 120 字标注截断 |
| 公司答复（或状态） | 是 | 正文 / `body_status` | stored 给全文；否则"答复正文未取得" |
| 逐字片段 | stored 时必填 | `locate_evidence` | 引用关键句的区间证据 |
| url | 是 | 条目字段 | 原样记录 |
| 主体命中依据 | 是 | 问题文本匹配规则 | 写明"问题含'<公司名>'" |
| 来源工具 | 是 | 实际调用工具与 mode | 便于复核 |
| 数据期数 | 是 | 本次检索起止 + 返回条数 | 见阶段 4 |

---

## 阶段 4：检索局限声明（每次输出必附）

### 4.1 覆盖 = SSE 单市场（固定声明，不得省略）

上游实测（2026-10）：上证 e 互动（sns.sseinfo.com）可用；深交所互动易上游不可用（irm.cninfo.com.cn 旧 API 返回 405，新版面已 SPA 化，无稳定接口）。因此：

- 清单只含**上交所上市公司**的问答；
- 深市标的写"未覆盖（SZSE 上游不可用）"，**不得写"无问答记录"、不得记 0**；
- 跨市场比较（沪市 vs 深市问答活跃度）**不得做**——半边缺数据，比较即误导。

### 4.2 覆盖以已入库条目为准

`list_since` 返回的是 info-stream 已摄取的条目，受上游口径、入库节奏与健康度约束。**"本次返回 N 条"不等于"该期间该公司共 N 条问答"**；健康度异常时此局限升格为顶部声明。

### 4.3 检索路的召回不确定

`search_info` 换词换结果，`mode=index` 时命中只到标题/摘要层面。**未命中不等于没问过。**

### 4.4 "未检索到"的写法

清单为空时只能写：

> 在 <起止日>、<sources=sse-interaction / 关键词> 口径下未检索到该公司问答（覆盖 = SSE）。

**不得写"公司未与投资者互动""公司回避提问"**——那是判断，且 SZSE 缺口未排除。

---

## 阶段 5：输出卡与口径溯源

```text
【互动问答清单】<公司简称>（<证券代码>，SSE）· <起止日>
健康度：sse-interaction <ok/partial/unavailable>（水位 <watermark_published>，lag p95 <X>s，失败 <N> 次）
覆盖声明：仅上交所上市公司（上证 e 互动）；SZSE 互动易上游不可用，深市公司未覆盖
检索口径：游标 mcp__fd-info__list_since（M 页）｜ 检索 mcp__fd-info__search_info（关键词 <词>，mode=<text-core/index>，N2 条）
返回：游标 N1 条 ｜ 检索补漏 N2 条 ｜ 其中答复正文 stored a / pending b / failed c ｜ 共 N 条

| published_at | item_id | source_id | 投资者问题（≤120字） | 公司答复（全文或"正文未取得"） | 逐字片段 | url |
|---|---|---|---|---|---|---|

局限声明：<按阶段 4 逐条，至少 4.1 与 4.2>
数据截至：<watermark 或最近一条 published_at（写明取哪个）> ｜ 数据期数：<起止日，共 N 条>
```

**口径溯源表**（每次输出必附）：

| 项 | 来源（工具名） | 数据期数 |
|---|---|---|
| 来源健康度 | `mcp__fd-info__stream_health` | 调用日 <YYYY-MM-DD> |
| 主体信息 | `mcp__fd-cn-report__get_company` | 调用日 <YYYY-MM-DD> |
| 问答清单 | `mcp__fd-info__list_since` | <起止日>，N1 条 / M 页 |
| 检索命中 | `mcp__fd-info__search_info` | 关键词 <词>，mode=<…>，N2 条 |
| 条目详情 | `mcp__fd-info__get_item` | <item_id 级，逐条> |
| 逐字片段 | `mcp__fd-info__locate_evidence` | <doc_id + 区间，逐条> |

---

## 防编造硬约束（红线）

- ❌ **所有 item_id、问题、答复、日期、链接必须来自工具返回**；无返回写"未取得"，**禁止编造问答、禁止凭记忆补写某公司某日的问答，不得用记忆里的答复文本凑数**。
- ❌ **引用任何问答前必须先查 `mcp__fd-info__stream_health`**；来源不可用或 lag 超阈值时必须声明"信息可能不完整"，不得把空结果写成"没有问答"。
- ❌ **SZSE 缺口必须声明**：深市标的"未覆盖（SZSE 上游不可用）"，不写"无问答"、不记 0、不换源顶替、不做沪深比较。
- ❌ **body_status=pending/failed 的答复不得写成已取得**；不得用摘要或记忆补答复全文。
- ❌ **不得把问题与答复混写**成"公司表示…"——两个主体两个口径，分开两栏。
- ❌ **不得把"返回 N 条"写成"共 N 条"**，不得跳过 `next_cursor` 翻页。
- ❌ **转述必须标注"转述（非原文）"**；引用关键句只用 `locate_evidence` 的逐字片段。

---

## 降级路径（数据源未连接或调用失败时，强制执行）

| 情形 | 处理 |
|---|---|
| `fd-info` 工具未连接/报错 | 顶部明示"未连接（`mcp__fd-info__list_since`：<失败情形>）"，正式清单留空。**不得用 fd-cn-report 的 interaction 工具或通用网络检索冒充**（fd-cn-report 侧互动上游同样不可用，且信息类取数只走 info-stream） |
| `stream_health` 里 sse-interaction=unavailable | 输出"互动来源不可用（<reason>）"，**不产出问答清单**；不换源 |
| `stream_health` 不可用 | 写"健康度未取得（未经在线核实）"，清单照出但顶部声明"信息可能不完整（健康度未知）" |
| `search_info` 不可用 | 检索路标"未执行"；清单只保留游标路，声明召回可能不全 |
| `get_item` 报"条目不存在" | 该 item_id 标"条目不可得"，不补造内容 |
| `locate_evidence` 报区间越界/正文缺失 | 该条引用降级为"转述（非原文）"并标注原因 |
| 标的为 SZSE/BSE | 直接输出"未覆盖（SZSE 上游不可用 / BSE 不在互动源覆盖内）"，不检索 |
| 全部不可用 | 输出"本次未取得（工具均未连接）"+ 已尝试工具清单 + 恢复后可复跑的调用式；**不产出清单** |

---

## 交接

- 清单产出 → `gap-signal-reading`：交 item_id 与 doc_id，由它从答复中抽指引、措辞变化与回避/确认模式（每条线索带逐字片段）。
- 需要"问答口径与业绩预告/实际值对照" → `gap-forecast-check`：本技能只给问答样本（item_id + published_at + 答复全文），不给对照数字。
- 需要合成"预期差陈述" → `gap-statement`：必须以前三者的产出卡为输入，不接受凭空开题。

---

> **免责声明**：本技能由 AI 辅助生成，输出为互动平台问答的检索与结构化整理，**不构成投资建议**。全部 item_id、问题、答复与发布时间来自所标注的柏讯服务端 info-stream 工具的实际返回，并随附来源工具、数据期数与数据截至日期；覆盖以上证 e 互动（SSE）为限，深交所互动易上游不可用，深市公司未覆盖；来源健康度异常时输出前置"信息可能不完整"声明。数据源未连接时输出为未经在线核实的推理草稿，不产出正式清单。问答内容反映提问者与公司口径，不代表对证券价格走势的任何预期。
