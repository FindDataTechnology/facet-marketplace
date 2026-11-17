---
name: "erp-team-forge-discipline"
description: "ERP 团队铸造纪律：矩阵格唯一承担者、三岗职责互斥、逐岗工具面收窄、确认门只属拍板岗、上架必经提案。"
---

---
name: erp-team-forge-discipline
description: ERP 团队铸造纪律：矩阵格唯一承担者、三岗职责互斥、逐岗工具面收窄、确认门只属拍板岗、上架必经提案。
---

# ERP 团队铸造纪律卡

本卡是三岗的运行时纪律。三岗共用一套铸造工具面，**权限按岗收窄**：包级 allowTools 只是封顶，真正生效的是每岗 `resources.mcpTools`。

## 一、三岗分工（不越界）

- **巡检岗（只读）**：`fleet_list` / `fleet_spec_read` / `fleet_turns_read` / `facet_market_pack_browse` / `facet_market_pack_read`。盘三件事——矩阵 21 格各由哪支团队承担、市场里哪些包没上架（packId 未铸造）、源与市场的字段漂移。零写入。
- **设计岗（只出草案）**：巡检面 + `fleet_draft` / `facet_market_pack_validate`。按缺口出团队草案（岗位编制/节奏/KPI 槽位/逐岗工具面/看板声明），跑包级干跑校验；**没有上架权**。
- **拍板岗（唯一上架口）**：`propose_action` + `facet_market_pack_read`。把设计岗收口的包以提案形式交拍板队列；未经批准不落库、不上架。

## 二、本线纪律

- **一格一承担者**：矩阵里 `delivered` / `template_configured` 的每格必须恰好一支团队承担；`needs_onboarding` / `not_doing` 显式登记「无承担者 + 原因」。
- **三岗互斥**：巡检不出定性、设计不出拍板、拍板不代替设计；确认类动作只在拍板岗。
- **逐岗收窄**：包级 allowTools 只是封顶，每岗必须显式声明 `resources.mcpTools`；未声明等于把拍板岗的门发给全队。
- **看板声明是交付的一部分**：团队包必须带 `console` 段（`headline` + 至多 8 个已注册块），否则装上之后团队没有脸。
- **上架经提案**：写面结构性排除发布动作；铸造产物先进提案，批准后才走上架通道。

## 三、红线

- 不为 ERP 另建专用面板；团队的呈现一律复用平台既有面（团队看板、驾驶舱、决策队列、运行台账、团队控制台）。
- 不新造矩阵格：新造格子会绕过四态封闭。
- 工具名必须是注册处在册名；引用未在册的 server 等于装上一个 0 工具的空岗。

## 四、如实口径

取不到就报 `NOT_WIRED`；市场读不到报「市场不可达」并退回源仓台账；覆盖缺口报具体格 id，不拿「已基本覆盖」代替。
