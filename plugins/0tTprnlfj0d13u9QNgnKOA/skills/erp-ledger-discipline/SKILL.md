---
name: "erp-ledger-discipline"
description: "ERP 复式账本内核纪律卡：写路径只出 proposal、确认门才入账、借贷必平由数据库约束、余额由分录重算、对账只读滩头、32 件商业工具面、合规红线与如实口径。"
---

---
name: erp-ledger-discipline
description: ERP 复式账本内核纪律卡：写路径只出 proposal、确认门才入账、借贷必平由数据库约束、余额由分录重算、对账只读滩头、32 件商业工具面、合规红线与如实口径。
---

# ERP 复式账本内核纪律卡

本卡是 `fd-erp-ledger-mcp`（ERP 板块，本位币 CNY）的如实口径守界，供集群岗位与 cell 复用。以下每条都是真实能力边界，不得对外宣称越界能力。

## 一、写路径只有 proposal，确认门才入账
- agent 与外部调用方只能提交 proposal（拟入账事件），proposal 不直接改变账本状态；无任何绕过确认车道的直写落账工具。
- 确认门（confirm_proposal）是入账的单一关口：确认后按复式分录入账；借贷必平由数据库约束触发器强制，不平则整笔拒绝。
- 拒绝（reject_proposal）留审计痕迹且不入账；stable_id 幂等去重，同键重复提交返回首次结果（唯一索引落库）。

## 二、已入账分录只进不改
- 已入账分录 append-only：任何更正以冲正（反向/红字分录）表达并与原分录关联可溯；改/删路径不存在（数据库拒）。
- 余额按科目/币种查询，由分录确定性重算，无独立余额字段；每条分录带来源引用与时间戳。

## 三、对账引擎只读滩头
- 两源流水对齐，产出四类差异（缺失、多余、金额不符、状态不符），每条含双侧原始引用，报告落档可查。
- 对账阶段对客户外部系统保持只读、零数据改动，不要求客户更换既有经营系统；长任务立即返回 task_id，状态与进度可查。

## 四、多币种与凭证
- 原币 + 本位币折算（汇率 + as_of + 出处）；无出处汇率拒绝。
- 租户列隔离 + 查询闸：跨租户查询拒绝并审计留痕；BYOK 凭证经环境变量注入，零落库零日志。

## 五、商业工具面（32 件随包声明 / 内核注册 37 件）
本包声明 32 件（本包纪律覆盖的范围）：proposal 4（submit_proposal / list_proposals / confirm_proposal / reject_proposal）、ledger 2（query_balance / query_entries）、recon 2（run_reconciliation / get_recon_report）、billing 9、credit 4、event 4、object 2、posting 5。
内核另有 period 组 5 件（close_period / reopen_period / period_status / period_report / list_periods）不在本包声明内——需要期间关账能力的团队按岗另配，不默认发全队。
工具面不提供任何绕过确认车道与数据库约束的写路径；每次响应含 rows_returned 戳；错误为封闭错误码集合，不伪装空成功；结果统一体积上限，超限给截断标记与分页续读。

## 六、合规红线（如实守界，不得宣称越界能力）
- 资金归集/代收代付/分账：平台只做对账与核对指令层，资金走客户自选持牌通道（二清红线）。
- 开票/税务申报不自研（挂账）；账内可记录发票类事件并参与对账，票据本体由客户既有合规渠道处理。
- 不宣称：单体 ERP 应用/表单 UI、开票自研、资金执行、网关已上线、市场已 live 上架、registry 快照已 live 写入。

## 七、交付状态
本包为 publish-ready 源清单：待上架/未发布（packId 未铸造）。live 发布、网关路由、registry 推送需线上凭据，属独立部署步骤，不在本包内完成；fleet 段 v2 形状且免自指（不引用本包尚未铸造的自身 id）。
