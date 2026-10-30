# 柏讯 · 政策传导

柏讯政策传导四件套：政策识别（部委条目 source_type=ministry，读正文抽发布机关/文号/政策工具/适用范围/生效时间/执行期限/量化目标七件套，每项要素附 item_id、as_of 与经 locate_evidence 核对的逐字片段）、行业映射（政策要素→受影响行业/区域→可检验变量三段映射，变量必须在年鉴/城市/公司面板真实检索到，两档置信度：政策明示/语义推断，无变量环节进未验证清单）、区域与企业传导（事件窗与政策强度序列两条路线逐环检验，相关系数、时滞扫描、样本量与窗口随行；结论三档——已验证/不显著/未验证环节，未验证环节必须显式标注，相关系数与样本量随每条结论输出；历史统计关系非因果证明）、政策跟踪（部委条目流计数与期间对比、健康度水位、已验证链路命中登记只转引不新增结论）。核心纪律：健康度前置（引用前先查 stream_health，lag 超阈值声明信息可能不完整）、区分已验证与未验证环节（无数据的一环明示未验证，绝不默认政策必然传导）、相关系数与样本量随结论输出、不因果表述不预测。不构成投资建议。

## 技能

- policy-detection — 柏讯政策传导 · 政策识别。当用户要求"最近有什么新政策""把某部委近一月的发布理一下""这条政策文件说了什么"时使用。先查来源健康度，再取部委条目（source_type=ministry），读正文抽取政策要素（发布机关/文号/政策工具/适用对象与范围/生效时间/执行期限/量化目标），每项要素附 item_id、as_of 与经 locate_evidence 核对的原文逐字片段。只做识别与要素抽取，不做行业映射（交 policy-mapping）与传导检验（交 policy-transmission）。
- policy-mapping — 柏讯政策传导 · 行业映射。当用户要求"这条政策影响哪些行业""政策落到哪些可检验的指标上""给我政策—行业—变量的映射表"时使用。把政策要素映射到受影响行业/区域维度，并逐一检索真实在册的检验变量（年鉴/城市面板/公司面板），两档置信度（政策明示 / 语义推断）逐条标注。只做映射：不做统计检验（交 policy-transmission），不出投资候选。
- policy-transmission — 柏讯政策传导 · 区域与企业传导检验。当用户要求"这条政策到底传没传到行业/区域数据""政策发布后地方指标变了吗""政策与公司科目有没有统计关系"时使用。对映射交来的每条"政策→变量"假设逐环检验：相关系数、时滞扫描、样本量与窗口；结论三档——已验证 / 不显著 / 未验证环节，未验证环节必须显式标注，相关系数与样本量随每条结论输出。历史统计关系，不是因果证明，不预测。
- policy-watch — 柏讯政策传导 · 政策跟踪。当用户要求"帮我盯着部委发布""每旬把新政策理一遍""跟踪表里标一下哪些撞上了已验证链路"时使用。按部委来源与政策工具类型筛条目流，输出条目清单、来源计数、期间对比与健康度水位；对映射/传导已登记的政策标"同一事项的新披露"，对撞上已验证链路要素的新政策只做"命中登记"不新增结论。不预测、不推荐；定时属宿主 cron。

## 智能体

- 柏讯 · 政策传导助手

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **fd-info** — 端点 `https://mcp.finddatatech.cloud/fd-info/mcp`（需要组：wire-customers）
- **fd-find-data-business-mcp** — 端点 `https://mcp.finddatatech.cloud/fd-find-data-business-mcp/mcp`（需要组：wire-customers）
- **fd-quant** — 端点 `https://mcp.finddatatech.cloud/fd-quant/mcp`（需要组：wire-customers）
- **wire-build-mcp** — 端点 `https://mcp.finddatatech.cloud/wire-build-mcp/mcp`（需要组：wire-customers）

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```
