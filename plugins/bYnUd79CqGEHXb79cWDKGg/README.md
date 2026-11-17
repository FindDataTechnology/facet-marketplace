# 识律操作导览

识律（lex 线）操作面导览单技能包：先 lex_surface_overview 拿全景（检索面/law-bench/六套件矩阵/本面说明），再走账户四读（me/usage/billing/quota）与凭据列表；凭据 mint/revoke 是 internal 工具，本包 allowTools 不含它们——凭据生命周期一律走壹座 lex 凭据轨（连接即注入、401 翻面自动重连），需要手工管理时去识律控制台或经壹座凭据设置操作。装包即得 lex-ops-mcp 六件商业只读工具。

## 技能

- lex-surface-orientation — 识律线操作导览：首次接入先调 lex_surface_overview 拿全景；账户四读（me/usage/billing/quota）日常对账；凭据 mint 明文只出现一次的处置纪律、401 翻面语义、何时 mint 何时走壹座凭据轨。

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **lex-ops-mcp** — 端点 `https://mcp.finddatatech.cloud/lex-ops-mcp/mcp`

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```

---

装好只是开始。想让这套能力 **7×24 常驻**（定时运行、多岗协作、产出直接交付），到萬星开一支智能体团队：<https://finddatatech.cloud>
