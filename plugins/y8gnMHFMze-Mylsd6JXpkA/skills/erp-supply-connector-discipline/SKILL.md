---
name: "erp-supply-connector-discipline"
description: "连接器纪律卡（仓配分销）：载体唯一（萬星连接器）、BYOK 凭证不出库、编排层只读外部系统、推送只留痕、未接通如实 NOT_WIRED。"
---

---
name: erp-supply-connector-discipline
description: 连接器纪律卡（仓配分销）：全部外部对接经萬星连接器载体；模板层零外部写入；未接通如实报 NOT_WIRED 不伪造。
---

# 连接器纪律卡（仓配分销）

## 一、载体唯一
- 金蝶/WMS/支付/门户对接全部经萬星连接器（多租户、凭证不出库）；ERP 线不建连接器框架、不直连凭据。
- WMS 无目录 provider 时按连接器 SDK 新增，在 open-connector-mt 独立 openspec 根立项；本仓只消费契约。

## 二、零外部写入（编排层）
- 模板编排层对 WMS/门户零写入：推送动作只落「推送指令记录」留痕，实际推送由连接器执行；WMS 只有只读快照面。
- 作业执行在外部（WMS/PDA/门户/金蝶）；中台编排，执行器在外。

## 三、如实守界
- 未接通的 provider（WMS 品牌/支付渠道目录）如实 NOT_WIRED + 替代路径说明；不以人工补录冒充自动接入。
- BYOK：凭证只经环境变量注入（ERP_BYOK_*），零落库零日志。
