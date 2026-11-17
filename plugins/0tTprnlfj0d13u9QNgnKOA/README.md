# ERP 账本内核能力包

ERP 复式账本内核能力包/供给包：以 MCP 工具面暴露账本内核，供集群岗位与 cell 复用。写路径只出 proposal（拟入账事件），确认门才按复式分录入账；借贷必平由数据库约束触发器强制，已入账分录 append-only（更正走冲正并关联原分录），余额由分录确定性重算、无独立余额字段；stable_id 幂等去重，同键重复提交返回首次结果。对账引擎以只读滩头接入两源流水，产出四类差异（缺失/多余/金额不符/状态不符）与双侧原始引用，报告落档可查，长任务立即返回 task_id；对客户外部系统零写入。多币种记原币与本位币折算（汇率 + as_of + 出处），无出处汇率拒绝；租户列隔离 + 查询闸（跨租户拒绝并审计留痕），BYOK 凭证经环境变量注入、零落库零日志；每次响应含 rows_returned 戳，封闭错误码集合不伪装空成功，结果统一体积上限 + 分页续读。二十一件工具逐字（proposal / ledger / recon / billing / credit 五组）：submit_proposal / list_proposals / confirm_proposal / reject_proposal / query_balance / query_entries / run_reconciliation / get_recon_report / submit_billing_rule / approve_billing_rule / list_billing_rules / bill_event / bill_period_snapshot / list_fees / get_fee_detail / reverse_fee / simulate_billing / reserve_credit / release_credit / query_credit / list_credit_reservations；无任何绕过确认车道与数据库约束的直写工具。合规红线如实守界：资金归集/代收代付/分账只做对账与核对指令层、资金走客户自选持牌通道（二清红线）；开票/税务申报不自研（挂账），账内可记录发票类事件并参与对账、票据本体由客户既有合规渠道处理。本包为 publish-ready 源清单（待上架/未发布，packId 未铸造），live 发布/网关路由/registry 推送属独立部署步骤，本包内不完成；fleet 段 v2 形状且免自指。

## 技能

- erp-ledger-discipline — ERP 复式账本内核纪律卡：写路径只出 proposal、确认门才入账、借贷必平由数据库约束、余额由分录重算、对账只读滩头、32 件商业工具面、合规红线与如实口径。

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **fd-erp-ledger-mcp** — 端点 `https://mcp.finddatatech.cloud/fd-erp-ledger-mcp/mcp`

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```

---

装好只是开始。想让这套能力 **7×24 常驻**（定时运行、多岗协作、产出直接交付），到萬星开一支智能体团队：<https://finddatatech.cloud>
