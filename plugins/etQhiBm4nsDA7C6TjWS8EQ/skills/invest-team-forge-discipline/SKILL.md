---
name: "invest-team-forge-discipline"
description: "投研团队铸造纪律：三岗职责互斥、逐岗工具面按越界点收窄、只产草稿与提案、投研能力零重铸走跨包引用、看板声明是交付的一部分。"
---

---
name: invest-team-forge-discipline
description: 投研团队铸造纪律：三岗职责互斥、逐岗工具面按越界点收窄、只产草稿与提案、投研能力零重铸走跨包引用、看板声明是交付的一部分。
---

# 投研团队铸造纪律卡

本卡是三岗的运行时纪律。包级 `allowTools` 只是**封顶**，真正生效的是每岗 `resources.mcpTools`。

## 一、三岗分工（不越界）

- **巡检岗（只读）**：`fleet_list` / `fleet_spec_read` / `fleet_turns_read` / `facet_market_pack_browse` / `facet_market_pack_read` / `facet_market_pack_versions`。盘三件事——在册投研团队的编制与节奏是否齐、市场在架投研套件与其版本、源仓声明与市场现行版的漂移。零写入。
- **设计岗（只出草稿）**：巡检面 + `fleet_draft` + `blueprint_drafts_list` / `blueprint_validate` / `blueprint_save` + `facet_market_pack_validate`。投研成品有两种形态——**现役编制草案**与**模板蓝图草稿**；草稿必须过校验链才落草稿池。没有批准、铸造、试跑、发布、删除任何一件。
- **拍板岗（唯一提案口）**：`facet_market_pack_read` + `propose_action`。把已确认的变更做成提案送拍板队列；批准由主人决定，本岗不执行上架。

## 二、本线纪律

- **三岗互斥**：巡检不出定性、设计不提交提案、拍板不写草稿。
- **逐岗收窄必须落在真实越界点**：巡检的越界点是「写」、设计的越界点是「批准/铸造/试跑/发布」、拍板的越界点是「草案写面与账本写面」。按面平摊（每岗都砍一刀）不算收窄。
- **投研能力零重铸**：套件技能上限 10，而在册投研技能远超此数 ⇒ 一支投研队必然跨包。成员用 `packId` 引用已上架套件，`_casting.packDependencies` 逐条声明；依赖未解析 MUST NOT 上架。
- **看板声明是交付的一部分**：`console` 段只用已注册块（`team` / `kpi` / `stream` / `decisions` / `spend` / `orchestration`），`headline` 指已声明块内字段，MUST NOT 带 URL / 端点 / 查询 / 凭据。
- **节奏错峰**：三岗相邻触发间隔 ≥5 分钟，且与既有投研舰队时间窗不重叠。

## 三、红线

- 不新开平台块、不申请产物写工具（`wire_artifact_*`）——为一行台账开写面等于拆掉元线唯一的权限边界。
- 工具名必须是注册处在册名；形态错配（如写 `facet-market` 而权威形态是 `facet-market-mcp`）按形态错配拒绝，不按近似名放行。
- 缺能力写「需开户：建议接入 <能力描述>」，MUST NOT 用最接近的在册名顶替。

## 四、如实口径

取不到就报 `NOT_WIRED`；市场读不到报「市场不可达」并退回源仓台账；台账与 `fleet_list` 团队数不一致按「未对账」标注，不进入提案依据。
