---
name: "yizuo-wanxing-guide"
description: "壹座与萬星使用手册：壹座（AI 助手工作台）的界面地图、功能集安装动线、自建预设、常见操作；萬星（Agent 服务部署与运行平台）的服务目录、A2A 调用、部署者控制台、访客对话入口；含两平台分工关系与五分钟客户演示动线。当用户询问如何操作这两个平台、如何部署或调用 agent 服务、如何向客户演示时使用。"
---

---
name: yizuo-wanxing-guide
description: 壹座与萬星使用手册：壹座（AI 助手工作台）的界面地图、功能集安装动线、自建预设、常见操作；萬星（Agent 服务部署与运行平台）的服务目录、A2A 调用、部署者控制台、访客对话入口；含两平台分工关系与五分钟客户演示动线。当用户询问如何操作这两个平台、如何部署或调用 agent 服务、如何向客户演示时使用。
---

# 壹座与萬星使用手册

这两个平台是寻数 AI 能力的**使用面**：壹座负责"人用 AI"，萬星负责"把 AI 变成可被程序调用的服务"。
同一套底座（dsh 运行时 + 谦面 registry + 计费面）的两个方向。

- **壹座（Platform / 壹座线）**：AI 助手工作台。`https://platform.finddatatech.cloud`（也可本地部署：`npm start` → `localhost:3000`）。
  人在这里聊天、喂文档、装功能集、配 MCP/技能、跑定时任务、接 bot、看 trace。
- **萬星（Wanxing / 萬星线）**：Agent 服务的对外运行与服务平台。`https://wanxing.finddatatech.cloud`。
  把 pack 里的 agent **部署**成服务，对外提供 **A2A 协议**调用、门面（目录/对话页）、计费与机群观测。

**一句话分工**：在**壹座**里把 agent 装配好、自己用；在**萬星**里把它发布成服务、给别人（或程序）用。

## 先搞清楚你在哪一侧

| 你想做的事 | 去哪 | 读哪节 |
|---|---|---|
| 日常用 AI 助手（聊天/文档/装技能） | 壹座 | §1 |
| 装功能集（pack）、连 MCP 市场 | 壹座 → 设置 | §1.3 |
| 把 pack 里的 agent 部署成服务 | 壹座 pack 详情 → 部署（或萬星控制台） | §2.2 |
| 让程序调 agent（A2A） | 萬星 → 目录/文档 | §2.3 |
| 看 agent 跑得怎么样、改配置、暂停 | 萬星 → 控制台 | §2.4 |
| 给人一个网页对话入口 | 萬星 → `#/chat/<slug>` | §2.5 |

---

## §1 壹座（Platform）怎么用

### 1.1 界面地图（网页/桌面同一套）

| 路由 | 是什么 |
|---|---|
| `/chat`（默认） | 对话主页：流式思维链/工具调用/技能执行、模型与 agent 选择器、图表渲染 |
| `/knowledge` | 文档库：导入 PDF/Markdown/文本/网页 URL，做 RAG 检索 |
| `/agents` | 智能体与应用目录（含 pack 带来的 agent） |
| `/bots` | bot 通道：企业微信/飞书/Telegram/公众号 |
| `/tasks` | 定时任务（cron），带通知 |
| `/trace`、`/trace/:turnId` | 每回合的调用追踪——**验证 MCP 真实调用的地方** |
| `/resources` | 资源库（图表等产出自动入库） |
| `/settings/<section>` | 设置弹窗（见下） |
| `/share/:token` | 公开只读会话分享 |

设置分节（`/settings/<slug>`）：`general`、`account`、`models`（模型）、`mcp`（MCP 扩展）、
`skills`（技能）、`packs`（功能集市场）、`presets`（自建预设）、`wechat-app`、`devices`（设备配对）、
`branding`（管理员）、`status`（运行状态）。

### 1.2 最小上手（新用户）

1. 登录 `https://platform.finddatatech.cloud`（Logto SSO）。
2. `/chat` 直接开聊；右上可切模型与 agent。
3. 想让回答基于自己的资料 → `/knowledge` 导入文档。
4. 想扩展能力 → §1.3 装功能集。

### 1.3 装功能集（pack）—— 完整动线

这是用户拿到"识律套件"等能力的方式：

1. 壹座 → **设置 → 功能集**（`/settings/packs`）。
2. 先**连接 MCP 市场**：点"连接 MCP 市场" → 跳 registry 登录页 → `Continue with Logto`（共享会话，**全程不输密码**）→ 铸一枚 7 天个人令牌。
   - 装 registry 来源的 MCP 需要这一步（无凭据会报 `credential-required`）。
   - 没有此凭据时只能装本地/内置 MCP。
3. 浏览市场 → 打开套件详情 → **订阅** → **安装到运行时**（"我的功能集"）。
4. 安装后：
   - **技能** → 设置 → 技能（热加载，即装即用）
   - **MCP** → 设置 → MCP
   - **agent/角色** → 对话页的 agent 选择器（聚焦模式会按 persona 收窄可用工具）
5. 验证：发一条真实业务问题，去 `/trace` 看是不是真的调了 `mcp__<server>__<tool>`。

**装不上时**：常见原因是权限/凭据未开通。四层权限闸（组/scope/计费/凭据）的排查表见
《谦面功能集制作指南》技能（同市场官方功能集）——那是同一个问题域。

### 1.4 自建预设与"发布并部署"

设置 → 自建预设（`/settings/presets`）：用户自组 persona（选技能+MCP 子集）。
行内「发布并部署」= 一键把预设发成 pack（需创作者组）并部署为 agent 服务——这是从"自己用"到"给服务"的捷径。

### 1.5 常见操作问答

- **换模型**：设置 → 模型；对话页顶部也可临时切。
- **加 MCP**：设置 → MCP → 添加（手填或从市场选）；市场来源的需要先连市场（§1.3 第 2 步）。
- **定时跑**：`/tasks` 新建 cron，可配通知。
- **接企业微信/飞书**：`/bots`，每个 bot 独立 webhook；机器调用走 relay 接口（Bearer 令牌，管理员预绑频道）。
- **分享对话**：会话菜单 → 分享 → 得 `/share/:token` 只读链接（匿名可看）。
- **本地起一套**：`npm install && npm start` → `localhost:3000`；桌面端 `npm run start:electron`。
  本地默认无鉴权（AUTH_MODE 未设 = 开放访问），适合个人用。

---

## §2 萬星（Wanxing）怎么用

萬星把"部署好的 agent"变成服务。三个概念先记住：

- **部署（deploy）**：把 pack 里带 `serving` 契约的 agent 拉起来，成为可调用服务。入口在**壹座 pack 详情**（"部署"按钮）。
- **调用（A2A）**：外部程序用 **调用键（sk-…）** 走 A2A 协议调它。计费按时长。
- **门面（web）**：`wanxing.finddatatech.cloud` —— 目录（看有哪些服务）、文档（对接说明）、控制台（部署者管理）、chat（访客直接对话）。

### 2.1 站点地图

| 页 | hash | 用途 |
|---|---|---|
| 首页 | `#/` | 介绍与快速事实 |
| 目录 | `#/catalog` | 公开 agent 服务列表（名称/版本/标签），每个有「对话」入口 |
| 文档 | `#/docs` | A2A 对接文档（含调用示例） |
| 控制台 | `#/console` | 部署者管理：机群板、每 agent 时间线、部署管理、调用者白名单、服务配置、用量与允许量 |
| 对话 | `#/chat/<slug>` | 访客对话页（登录后可聊；免费额度用完可贴自己的调用键继续） |

### 2.2 部署一个 agent 为服务

**入口 A（推荐，在壹座）**：壹座 → 设置 → 功能集 → 打开 pack 详情 → 有 serving 契约的 agent 会显示
**「部署为服务」**按钮 → 填（可选）节奏/预算覆盖、粘贴计费键 → 部署。生效约 5 分钟内。
部署后同页出现部署行：版本、在线/暂停状态、暂停/恢复按钮、「去配置」（跳到萬星控制台该 agent）。

**入口 B（萬星控制台）**：`#/console` → 部署管理区查看/暂停/恢复。

**计费键**：部署时粘贴的 `sk-` 键 = 该 agent 跑回合时消耗模型资源的计费身份（部署者承担）。
不填则沿用既有绑定。

### 2.3 用程序调用 agent（A2A）

这是给集成方的核心接口（完整契约见附录 B）：

```
GET  {BASE}/api/wanxing/v1/agents                                 # 公开目录
GET  {BASE}/api/wanxing/v1/a2a/{slug}/.well-known/agent-card.json # AgentCard
POST {BASE}/api/wanxing/v1/a2a/{slug}                             # 回合（需 Bearer 调用键）
```

- **BASE** = `https://wanxing.finddatatech.cloud`。
- **slug** 形如 `packs-<packId>-<agentId>`（全小写），从目录拿。
- **鉴权**：`Authorization: Bearer sk-…`（调用键由平台提供）。
- **幂等**：`Idempotency-Key` 头（仅 `message/send`，24h 重放窗，同键不双跑不双计）。
- **计费**：按时长（分钟向上取整 × 档位单价），回合结束即时结算；失败回合不计费。
- **并发**：每（调用者, agent）同时只允许 1 个回合，超发 409 + `Retry-After`。

```bash
curl -X POST "https://wanxing.finddatatech.cloud/api/wanxing/v1/a2a/<slug>" \
  -H "Authorization: Bearer sk-xxxx" \
  -H "Idempotency-Key: my-$(date +%s)" \
  -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","id":1,"method":"message/send",
       "params":{"message":{"role":"user","parts":[{"kind":"text","text":"你好"}]}}}'
```

`message/stream` 同形，响应 SSE。同步返回是**回执**（agent 的即时答复），长活发生在回合里。

### 2.4 部署者控制台（`#/console`，需登录）

| 面板 | 能做什么 |
|---|---|
| 机群板（board） | 全局 agent 状态总览（30s 自动刷新） |
| 每 agent 时间线 | 该 agent 的最近事件（含延迟） |
| 部署管理 | 列出你的部署；暂停/恢复 |
| 调用者白名单 | 私有 agent 加/删调用者邮箱（公开 agent 无需） |
| 服务配置 | 每部署的节奏（rhythm）/回合预算（budget）/模型（model）读写 |
| 用量视图 | "谁调了我的 agent"（调用记录） |
| 允许量 | chat 门面的免费额度上限（host allowance） |

### 2.5 给人一个对话入口（chat 门面）

- 公开 agent：任何人打开 `https://wanxing.finddatatech.cloud/#/chat/<slug>` 登录即可聊。
- **免费额度**：由部署者设的 allowance 承担；用完不关闭对话页，而是提示"可粘贴自己的调用键继续"——
  贴了键后续回合就变成普通 API 调用（走他自己的余额）。
- 私有 agent：需在调用者白名单里。

### 2.6 运维侧（管理员/部署者）

- 用量视图 API：`GET /api/wanxing/v1/ops/usage`（管理员或运行时凭据），按调用者/按 agent 两维。
- 白名单 API：`GET/POST/DELETE /api/packs/{packId}/deployments/{agentId}/callers`。
- 部署/升级/回滚：`POST /api/packs/:id/versions/:version/deploy`（幂等；升级=同接口重部署；回滚=部署旧版本号）。
- 暂停/恢复：`POST .../deployments/:agentId/pause|resume`。

---

## §3 两个平台的关系（讲给客户/新同事）

```
谦面（facet）—— 功能集市场：把技能+MCP+persona 组装成 pack，发布
   │
   ├── 壹座（Platform）—— 装 pack，人用：聊天 / 文档 / 技能 / 定时 / bot
   │       └─ 自建预设「发布并部署」────┐
   │                                    ▼
   └── 萬星（Wanxing）—— 把 agent 部署成服务：A2A 调用 / 计费 / 观测 / 门面
           └── 识律/柏讯等线的内容 = pack 里的技能与 MCP（registry 供）
```

- **registry（mcp.finddatatech.cloud）**是技能与 MCP server 的注册处，壹座/萬星都从它取供给。
- **计费面**管理调用键/部署键/结算。
- 一条 agent 服务的完整链路：谦面发 pack → 壹座订阅安装 → 壹座部署（或萬星控制台）→ 萬星门面暴露 A2A → 调用者用 sk- 键调用 → 结算。

## §4 演示动线（约 5 分钟，给客户看）

1. **壹座**登录 → `/chat` 发一条真实问题 → 打开 `/trace` 指出 `mcp__*` 真实调用（不是幻觉）。
2. **装功能集**：设置 → 功能集 → 连 MCP 市场（演示免密 SSO）→ 订阅一个行业套件 → 装 → 新会话用套件 agent 提问。
3. **萬星**：打开 `#/catalog` 看服务目录 → 打开一个 agent 的 `#/chat/<slug>` 直接对话（演示访客入口）。
4. **A2A**：`#/docs` 展示 curl 调用示例（或现场跑一条）→ 指出幂等键与按时长计费。
5. **控制台**：`#/console` 看机群板与某 agent 时间线 → 展示"谁调了我的 agent"。

---

## 附录 A：壹座界面与环境速查

### 形态

生产：`https://platform.finddatatech.cloud`（托管部署）。
本地：`npm start` → `http://localhost:3000`；Vite 开发 `npm run web:dev` → `:5173`（后端需同时跑 :3000）；
桌面：`npm run start:electron`（macOS/Windows，`userData/settings.json` 配置）。

### 小程序（微信）

`miniapp/`（Taro + React）。页面：chat（主页，历史在抽屉）、login（账号绑定）、share、cron。
后台切走会断 socket，回前台恢复；无 DOM 降级渲染。

### 关键环境变量（本地 `.env` / 托管配置）

| 变量 | 作用 |
|---|---|
| `LLM_API_KEY` / `LLM_BASE_URL` / `DEFAULT_MODEL` | 模型供应商（OpenAI 兼容）；未设 key 时服务照常启动、仅聊天不可用 |
| `ASSISTANT_NAME` | 内置助手显示名 |
| `AUTH_MODE` | 未设=开放访问；`forward_auth`=信任反代头；`logto`=OIDC 登录 |
| `PAAS_BASE_URL` | Logto 回调公开地址 |
| `PACK_MARKETPLACE` | `1` 显示功能集 UI |
| `FACET_BASE_URL` | `/api/packs` 全前缀代理到谦面；摘除即回滚市场到本地冻结库 |
| `PACK_CREATOR_GROUPS` | 可发布 pack 的组（默认 `creators`） |
| `PACK_BASELINE_MCP` | 聚焦模式仍加载的基线 MCP（如 `websearch`） |
| `MARKET_REGISTRY_URL` / `MARKET_REGISTRY_TOKEN` | 接 registry：MCP 服务器/技能/Agent 进市场 |
| `MARKET_REGISTRY_TTL_SECS` | registry 快照刷新周期（默认 300） |
| `AGENTS_CONFIG_URL` / `CATALOG_REFRESH_SECS` | Agent/应用目录云端 JSON（默认 60s 刷新） |
| `BOTS_RELAY_TOKEN` | bot 机器调用通道（未设时该接口关闭） |
| `PLATFORM_DATA_DIR` | 数据根（SQLite：会话/历史/文档索引/cron/扩展配置） |

托管形态：网关前置，每登录用户一个 **cell**（独立进程 + 私有数据目录）。

### 数据与安全模型（讲给客户时用）

- 数据落本地 SQLite（托管时在各自 cell 目录内）——**一个 cell 的数据对另一个 cell 不可见**。
- API key、外部服务令牌**只留服务端**，从不下发浏览器，从不入库明文。
- 可选组件缺失时优雅降级（服务始终能启动）——"本地优先，永远能启动"。

### 排查速记

- **聊天无响应/模型报错** → 检查模型配置；设置→状态看运行时。
- **装 pack 报缺凭据** → 设置→功能集→连接 MCP 市场（§1.3）。
- **装了技能但对话不调用** → 检查该会话是否聚焦模式（聚焦会收窄工具到 pack 声明的集合）；
  检查 trace 是否有 `mcp__*` 调用。
- **市场看不到某 pack** → 该 pack 私有，或你的账号不在可见范围；或市场快照未刷新（等几分钟）。
- **托管部署的配置调整** → 由平台运维统一管理，请联系平台运维。

## 附录 B：萬星 A2A 完整契约

权威来源：萬星 `#/docs` 页与平台对接文档；冲突时以生产实测为准。

### 速览

| 项 | 值 |
|---|---|
| 协议 | A2A 原生（JSON-RPC 2.0 over HTTP；`message/send` 同步 / `message/stream` SSE） |
| 鉴权 | `Authorization: Bearer <调用键>`（sk-…） |
| 幂等 | `Idempotency-Key` 请求头（仅 `message/send`；24h 重放窗） |
| 计费 | 按时长，分钟向上取整 × 档位单价；回合结束即时结算到调用键 |
| 限流 | 每（调用者, agent）并发回合=1（超发 409 + `Retry-After`）；另有调用者级 RPM |
| 会话 | 不带 `context_id` 时每请求独立；外部会话 24h 空闲回收 |

### 路由

```
GET  {BASE}/api/wanxing/v1/agents                                       # 公开目录（仅 public agent）
GET  {BASE}/api/wanxing/v1/a2a/{slug}/.well-known/agent-card.json      # 公开 AgentCard
POST {BASE}/api/wanxing/v1/a2a/{slug}                                   # A2A 回合（需鉴权）
GET  {BASE}/api/wanxing/v1/chat/{slug}/status                           # 门面 chat 状态（免费额度等）
POST {BASE}/api/wanxing/v1/chat/{slug}                                  # 门面 chat 回合（登录态，免费额度）
```

### 错误码

| HTTP | code | 含义 |
|---|---|---|
| 401 | `INVALID_KEY` / `UNRESOLVED_KEY` | 键形状错 / 键无法解析到账号 |
| 402 | `INSUFFICIENT_BALANCE` | 键余额/窗口不足 |
| 402 | `CALLER_SUSPENDED` | 结算连续失败被挂起；充值后自动解除 |
| 403 | `CALLER_NOT_AUTHORIZED` | 私有 agent 且不在允许清单 |
| 404 | `AGENT_NOT_FOUND` | 无此部署条目 |
| 409 | `TURN_IN_FLIGHT` | 该（调用者,agent）已有回合在跑；按 `Retry-After` 重试 |
| 423 | `AGENT_PAUSED` | agent 已暂停 |
| 429 | `RATE_LIMITED` | 超过调用者 RPM |
| 503 | `WANXING_BILLING_UNAVAILABLE` | 平台计费面未配置（fail-closed） |

JSON-RPC 层错误：`-32600`（非法请求）/`-32601`（方法不存在）/`-32604`（空消息）/`-32032`（上游回合失败）。

### 幂等与去重

- `Idempotency-Key` 作用域 =（调用者, agent, key），24h 重放窗：同键重放返回**首次完整应答**，
  不跑第二个回合、不产生第二条计费。并发同键只有一个回合执行。
- 工单型 agent 自己的去重（inbox 里已见路径只回执）是第二层，各管各的。

### 计费

- 平台按「时长 × 单价」向**调用键**结算，与 token 用量无关。
- 失败回合（平台/上游错误）不计费，但记账（可查）。
- 财务硬顶 = 键自身的消费窗与余额。

### chat 门面（访客对话）

- 状态：`GET /api/wanxing/v1/chat/{slug}/status` → `{chatAble, reason, subsidy, agent, subsidyRemainingToday, hostPoolRemaining}`。
- 免费额度由部署者 allowance 承担；用完不关页面，提示粘贴自己的调用键（之后走 API 计费）。
- 私有 agent 需白名单。
