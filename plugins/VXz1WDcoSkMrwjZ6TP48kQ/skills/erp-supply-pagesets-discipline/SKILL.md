---
name: "erp-supply-pagesets-discipline"
description: "页面集纪律卡（仓配分销）：壹座底座唯一载体；与 MCP 同源取数无平行路径；确认门交互模式；客户中性命名。"
---

---
name: erp-supply-pagesets-discipline
description: 页面集纪律卡（仓配分销）：载体唯一（壹座 miniapp 底座）、数据同源（REST/WS 与 MCP 同源）、确认门交互（提交→服务器确认→拒绝回显→超时兜底）。
---

# 页面集纪律卡（仓配分销）

## 一、载体唯一
- 四端页面集（UE/SE/SP/CP）全部建在壹座 miniapp 底座（Taro4 + React + Daylight 设计系统）；本包只声明页面契约，不另起前端框架。
- 底座抽取与页面集实施归 paas 仓独立 openspec 根；ERP 线只消费契约。

## 二、数据同源
- 页面取数走配套后端 REST/WS 面，与 MCP 工具面同源同库同权限；禁止页面直连库表或另建平行数据路径。
- 报表与 AI 助手（底座 chat 面）同源取指标口径（report preset metric_calibers 单一出处）。

## 三、确认门交互
- 关键动作（下单/对账单确认/派单拍板）走「提交→服务器确认→拒绝回显→超时兜底」模式；拒绝必回显理由。
- 页面集资产客户中性命名；锚点实例信息不进资产。