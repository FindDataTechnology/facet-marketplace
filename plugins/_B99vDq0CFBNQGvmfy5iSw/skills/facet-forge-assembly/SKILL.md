---
name: "facet-forge-assembly"
description: "在谦面把想法铸成功能集（pack）：先发现可用的 MCP 工具与技能，再组装 manifest（内联技能 + MCP 引用 + 角色），预校验通过后发布。用户要「做一个功能集/预设套件/上架」「把某能力打包给用户装」「我有个想法想做成 AI 助手」时使用。"
---

---
name: facet-forge-assembly
description: 在谦面把想法铸成功能集（pack）：先发现可用的 MCP 工具与技能，再组装 manifest（内联技能 + MCP 引用 + 角色），预校验通过后发布。用户要「做一个功能集/预设套件/上架」「把某能力打包给用户装」「我有个想法想做成 AI 助手」时使用。
---

# 铸一个功能集（facet-forge-assembly）

谦面（facet）是寻数的功能集市场。一个 **pack** 装进用户的壹座后带来三样东西：

| 装进去的 | 用户在哪看到 | 是什么 |
|---|---|---|
| 技能（skills） | 设置 → 技能 | 内联的方法论文本，模型按它工作 |
| MCP 服务器引用 | 设置 → MCP | 指向平台在册的 MCP server（**不能自带端点**） |
| 角色（agents） | 对话选择器 | persona，只定人格不定模型 |

你的工作不是写代码，是把**一件事该怎么做**写成技能，接上**已经存在的数据/能力**，摆好**谁来说**。

## 零、先想清楚三件事（别急着组 manifest）

1. **给谁用、解决什么问题**——一句话能说完吗？说不完就是两个 pack。
2. **数据/能力从哪来**——平台在册的 MCP 里有现成的吗？（→ 第一步）没有的话，这个 pack 做不成，
   v1 不允许自带端点。
3. **别人是不是已经做过了**——（→ 第二步查重）有近似的，做差异化或就别做。

## 一、发现可用的料

**找工具**（要什么能力）：

```
mcp__airegistry-tools__search_registry        # 语义搜索，主力：一句话描述你需要的
mcp__airegistry-tools__list_services          # 列全部 server（含各自工具清单）
mcp__airegistry-tools__get_skill_content      # 读某个技能的完整内容
```

- `search_registry` 是首选——用**自然语言描述你需要的能力**，它跨 server/tool/agent/skill 排序返回。
- 结果只包含**你自己有权用**的东西（按你的身份裁剪）。
- 注意：`intelligent_tool_finder` 已废弃，别用。

**判定标准**：能在结果里点名具体的 `server` + `tool`，才算找到料。找不到就如实告诉用户
"这个能力平台上还没有"，**不要**编一个工具名塞进 manifest。

## 二、查重（强制，不是建议）

发布前**必须**查一次：

```
mcp__facet-market-mcp__facet_market_pack_browse    # search="关键词" 语义/字面匹配名称与描述
```

**必须把结果呈现给用户**：已有几个近似的？你的差异在哪？如果已有 3 个以上高度重叠的，
先跟用户确认要不要继续。市场里重复供给是真实成本（用户要在货架上挑）。

## 三、组装 manifest

完整字段与限额见 `references/manifest-limits.md`（**发布前必读**，超限会被拒）。
最小可用骨架见 `references/manifest-skeleton.json`。

四个要点：

1. **技能 content 是全文**（含 frontmatter），单技能 ≤64KiB。技能名匹配 `[A-Za-z0-9._-]{1,64}`。
2. **技能里点名 MCP 工具必须用完整形态** `mcp__<server>__<tool>`——模型只认这个。用到哪个
   server 就必须在 `mcpServers` 里声明它。
3. **`allowTools` 收窄到真正要用的工具**（可选但强烈建议）：不写=给用户全部工具，
   写=只给这几个，权限面更干净，也更好解释。
4. **角色 persona 只写人格与工作方式**——不能带 model/baseUrl/apiKey。

给 pack 起名与写描述：**描述决定用户能不能在货架上找到它**，写清"给谁、解决什么、数据从哪来"。

## 四、预校验（发布前的硬门）

```
mcp__facet-market-mcp__facet_market_pack_validate   { manifest }
```

返回 `{valid, errors, caller}`：

- `errors` 为**全量**（一次给全部问题，不用反复试）。每项就是发布时会被拒的原始判词
  （含 `code`、`toolGate` 标记）——**原样转述给用户，不要改写**。
- `caller.publishEligible=false` 表示当前身份还不能发布（需要创作者身份）。
  **要和用户明说**，不要让 `valid:true` 造成"已经能发了"的错觉。
- 这个调用**不会**发布、不占配额，可以放心多跑几轮改到 `valid:true`。

**常见不过门的原因**：

| 判词关键词 | 意思 | 怎么办 |
|---|---|---|
| `Invalid manifest` + 具体字段 | 结构/限额问题 | 按 entry 与字段名改，见 references/manifest-limits.md |
| `pack-files-disabled` | 带辅助文件的包在当前市场未开门 | 去掉 files，或告知平台运营 |
| `toolGate` / `does not expose declared tool` | 声明的工具名对不上 registry 快照 | 用发现工具核对准确工具名；确认该 server 已上牌 |
| `Creator group required` | 身份不是创作者 | 申请创作者身份（见下） |

**发布还需要创作身份**：谦面目前是邀请制——请联系平台开通 `creators` 组。
（对话中发现的 `caller.publishEligible` 就是这个开关的读数。）

## 五、发布

```
mcp__facet-market-mcp__facet_market_pack_publish    { manifest }              # 新建
mcp__facet-market-mcp__facet_market_pack_publish    { manifest, packId }      # 发新版本
```

**发布前必须做的三件事**（缺一不可）：

1. `validate` 已返回 `valid:true`；
2. **把 manifest 摘要复述给用户**并得到明确同意——至少包含：包名、技能清单（名+一句话）、
   MCP 清单（server + 工具数）、角色名、是新建还是续版；
3. 用户明确说了「发布」（"好"/"可以"不构成确认）。

**续版前先确认归属**：`facet_market_pack_read { id }` 看是不是你自己发布的（不是作者会被拒）。

**发布后的两件事**：

- 默认 **public**（匿名可见）。要改成私享：告诉用户去壹座 → 设置 → 功能集 → 我的创作 → 该包 → 可见性。
- 提醒用户走一遍 `facet-forge-acceptance` 的自查（**发布 ≠ 可用**）。

## 六、跟用户怎么说话

- 用**用户的语言**说事，别暴露内部机制（registry 快照、组门控这些词对创作者是噪音）。
- 报问题时报**它怎么办**，不是它为什么发生：不说"tool_list 快照滞后"，说"这个工具名还没在平台上登记好，我们换个已登记的写法"。
- 用户的想法太空（"做个金融助手"）时，**先问三个问题**（给谁/解决什么/数据哪来），别直接开写。

## 参考

- `references/manifest-limits.md` — 全部字段、限额、常见拒绝原因（发布前必读）
- `references/manifest-skeleton.json` — 最小可用骨架（复制后改）
- `references/three-step-guide.md` — 三步使用指南模板（装了之后怎么用）
- 技能改编（用别人的技能做底子）→ 同包技能 `facet-forge-adaptation`
- 发布后自查 → 同包技能 `facet-forge-acceptance`
