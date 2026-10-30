---
name: "facet-pack-builder"
description: "谦面功能集（pack）制作的内部视角补篇：批量套件编排（套件定义 schema）、registry 侧技能注册、权限与计费四道闸的全貌。单包/首次制作请用《谦面工坊》功能集（它带发现与发布工具、覆盖完整创作流程）；本技能只讲工坊不覆盖的编排层与平台内部细节。当用户要批量生产一整条产品线的套件、要把技能注册进 registry 供各处复用、或要排查\"装不上/用不了\"的四道闸时使用。"
---

---
name: facet-pack-builder
description: 谦面功能集（pack）制作的内部视角补篇：批量套件编排（套件定义 schema）、registry 侧技能注册、权限与计费四道闸的全貌。单包/首次制作请用《谦面工坊》功能集（它带发现与发布工具、覆盖完整创作流程）；本技能只讲工坊不覆盖的编排层与平台内部细节。当用户要批量生产一整条产品线的套件、要把技能注册进 registry 供各处复用、或要排查"装不上/用不了"的四道闸时使用。
---

# 谦面功能集制作：编排层与平台内部补篇（facet-pack-builder）

> **先分清用哪个**：
>
> - **做一个 pack（绝大多数情况）** → 装《**谦面工坊**》功能集。它带发现工具（查平台上有什么
>   工具/技能）、市场工具（查重、预校验、发布），覆盖 想法 → 组装 → 预校验 → 发布 → 验收 全流程。
> - **本技能**只补工坊不覆盖的三件事：① 批量套件的编排 schema ② registry 侧技能注册
>   ③ 权限/计费四道闸的全貌（工坊只写用户能自救的部分）。

一个 **pack** = 内联技能 + registry MCP 引用 + persona 角色，版本化发布后用户在壹座
「智能体 → 功能集市场」订阅安装。

## 1. 批量生产：套件定义（编排层）

做一整条产品线（多个套件共享技能与 server）时，**先写套件定义，再生成 manifest**。
套件 JSON 是编排层，不是发布格式——发布格式由它 + SKILL.md 生成。

```json
{
  "schema": "facet-preset-suite/v1",
  "id": "lex-civil-nine-step",
  "line": "lex",
  "brand_name": "识律 · 民事诉讼套件",
  "brand_en": "Lex · Civil Litigation Suite",
  "version": "1.0.0",
  "description": "一句话说清套件干什么 + 数据来自哪里",
  "entry_skill": "civil-nine-step-workflow",
  "skills": ["civil-nine-step-workflow"],
  "mcp_servers": ["fd-legal-search-mcp", "fd-find-data-business-mcp"],
  "persona": "民商事诉讼律师助手",
  "required_groups": ["legal"],
  "data_caliber": { "wenshu": "3,700 万篇" },
  "commercial_ready": true
}
```

| 字段 | 必填 | 说明 |
|---|---|---|
| `schema` | 是 | 固定 `facet-preset-suite/v1` |
| `id` | 是 | kebab-case，全平台唯一；同时用作 pack 的 agent id |
| `line` | 是 | 产品线（lex / wire / base / facet / wanxing） |
| `brand_name` / `brand_en` | 是 | 品牌名 |
| `version` | 是 | 语义化版本 |
| `description` | 是 | 进 manifest 的 description |
| `entry_skill` | 是 | 入口技能名（对应 `skills/` 子目录名） |
| `skills` | 是 | 套件内技能目录名（≤10） |
| `mcp_servers` | 是 | registry **在册 server 名**（不是工具名；≤5） |
| `persona` | 是 | 人设，进 `agents[0].persona` |
| `required_groups` | 是 | 平台组名 → `mcpServers[].requiredGroup` |

**组装规则**：技能正文内联进 manifest（含 frontmatter）；`mcp_servers` →
`[{registryName, requiredGroup: required_groups[0]}]`；`persona` → `agents[0]`。
超限即报错退出，**绝不静默截断**。

manifest 生成要点：

- 技能 `content` = SKILL.md 全文（UTF-8 ≤64KiB）；`description` = frontmatter 的
  description 截断到 300（市场展示与触发的双重来源）。
- persona 建议追加入口指引："处理请求时优先加载并遵循套件入口技能「X」的工作流程；
  数据一律经套件所连 MCP 工具实时检索，未连接时按技能内降级路径如实声明。"
- 校验器不读未知顶层键（如 `_suite_ref`），发布时可保留作旁载。
- **限额的权威来源**是平台源码 `lib/pack-manifest.js` 的 `PACK_LIMITS`；
  发布前用真校验器对拍，别凭记忆。

## 2. 把技能注册进 registry（供各处复用，非 pack 内联）

pack 是**内联**技能；若要让一个技能被 registry 各处引用（含单文档注册），才需要这一步。

registry 注册要求 `skill_md_url` 是 **registry 容器可达的公开 URL**（内网地址它抓不到）。
惯例：技能源推到公开仓，用 raw URL。

```bash
curl -s -X POST https://mcp.finddatatech.cloud/api/skills \
  -H "Authorization: Bearer <admin-token>" -H "Content-Type: application/json" \
  -d '{"name":"<skill>","description":"<≤1024>","skill_md_url":"<raw url>",
       "tags":["<line>","finddata"],"visibility":"public","status":"active",
       "allowed_groups":["<group>"]}'
```

- `allowed_groups` 控制技能对谁可见；组名与对应身份系统 ID 都可列（交集语义）。
- 批量注册写幂等脚本（先 `GET /api/skills` 读现有，只注册差集）。
- **注意**：单文档注册不能承载树技能（带辅助文件的技能）——那条面会 fail-loud 拒。

## 3. 四道闸（用户"装不上/用不了"的排查全貌）

工坊的自查只写用户能自救的部分；这里是全貌，含需要平台运维介入的两道。

| 闸 | 在哪体现 | 症状（在哪看到） | 谁能修 |
|---|---|---|---|
| ① 装包组门 | pack 引用的 server 若属某组，用户组须相交 | 装包报 `requires group: <g>` | 平台运维加组（或换用无组约束的 server） |
| ② registry scope | 网关按用户组匹配 scope | 调用 403 | 平台运维挂组 |
| ③ 计费身份 | 非管理员调用者的计费预检 | 调用 402 `CALLER_NOT_MAPPED` / 余额/额度类 | 平台运维开户 |
| ④ 市场凭据 | 用户本人的市场 app token | 装包报 `credential-required` | **只能用户本人**：设置 → MCP → 商店 → 连接 |

另：`not in the market catalog` = server 未注册 registry，或工具面快照未刷新（等几分钟）。

## 4. 通道

发布 `POST /api/packs` 有两条通道：

- **脚本/CI**：`x-facet-token`（部署内部令牌）+ `x-facet-user`（base64url 的 `{email, groups}`）。
  发布者须在创作者组（默认 `creators`），否则 403；每作者每小时最多 10 次成功发布。
- **浏览器（首次/人工）**：壹座 → 智能体 → 功能集市场 → 我的创作 → 新建/编辑 → 发布。

⚠️ **含代码块的 manifest 走公网 POST 会被边缘 WAF 拦**（实测 403/断连）。
从集群内 loopback 发：`kubectl -n fd-prod exec deploy/facet -- curl http://127.0.0.1:3200/api/packs ...`。

## 5. 事实冲突时

以**代码与生产实测**为准：平台源码 `lib/pack-manifest.js`、谦面服务、registry。
创作侧的完整流程与限额表以《谦面工坊》为准——两处冲突时以工坊（它带工具、随平台更新）。
