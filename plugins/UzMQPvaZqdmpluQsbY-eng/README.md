# 识律 · 刑事辩护套件

刑事辩护套件：以总控流程技能为入口，串联认罪认罚/不起诉论证（审查起诉阶段）与庭审发问提纲（一审阶段），法条与指导案例走识律法律检索、法规效力走数据业务检索，类案与量刑参照走裁判文书检索。

## 技能

- criminal-defense-workflow — 刑事辩护全流程总控（识律刑事辩护套件入口技能）。当用户提出刑事案件材料（起诉意见书/起诉书/卷宗/判决书）、要求刑事阅卷分析、辩点梳理、辩护策略路径选择、定性或量刑方向判断时使用。按审查起诉—一审的阶段推进（侦查阶段不在本技能主流程内，见阶段 1 说明），调度套件内「认罪认罚/不起诉论证」与「庭审发问提纲」两个原子技能，并统一承担法条锚定、类案检索与红线校验。
- criminal-plea-and-nonprosecution — 认罪认罚与不起诉论证（识律刑事辩护套件原子技能）。审查起诉阶段使用：当用户问"要不要认罪认罚""认罪认罚协商""量刑建议协商""能不能不起诉""法定/存疑/酌定不起诉"或由 criminal-defense-workflow 调度时使用。输出证据态势、利弊权衡、量刑建议预测与协商三层条款、三种不起诉路径论证，并做当事人知情告知。
- criminal-trial-examination-outline — 庭审发问与举证质证提纲（识律刑事辩护套件原子技能）。一审阶段使用：当用户说"庭前会议""排非申请""非法证据排除""庭审发问""发问提纲""质证提纲""举证质证""交叉询问""逐份质证""辩方举证"或由 criminal-defense-workflow 调度时使用。输出庭前会议议程应对、非法证据排除申请、公诉人发问预判与应对、辩护人对被告人/证人/鉴定人的发问提纲、辩方举证提纲与逐份质证意见（书面版 + 庭上口述版）。

## 智能体

- 识律刑事辩护助手

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **fd-legal-search-mcp** — 端点 `https://mcp.finddatatech.cloud/fd-legal-search-mcp/mcp`（需要组：legal）
- **fd-find-data-business-mcp** — 端点 `https://mcp.finddatatech.cloud/fd-find-data-business-mcp/mcp`（需要组：legal）

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```
