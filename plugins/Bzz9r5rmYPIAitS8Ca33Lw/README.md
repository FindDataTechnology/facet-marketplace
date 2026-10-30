# 萬星 · 本地内容发布套件

云端生成 + 本地发布的完整闭环：从 VAAS 托管实例拉取租户名下的生成产物（图/视频/图文），在本机完成平台适配、人工确认后经自己的浏览器登录态发布到 10 视频平台 + 11 图文平台。登录态只存本机、平台零保管（见包内 SECURITY-NOTES.md）。发布前必须人工确认。

## 技能

- fd-vaas-pull-from-cloud — 从 VAAS 托管生成实例拉取租户名下的产物到本机发布目录。三种输入任选其一：谦面调用键（连网关按 owner 过滤）、签名下载 URL（生成响应里带的）、或直接给 asset id + 实例地址。产物自动落 downloads/ 标准目录并生成发布技能可直接消费的 task 目录（article.md + meta.json 骨架）。无凭据时输出获取指引。**本技能只拉内容，不碰任何登录态**（发布登录走 fd-vaas-login，在本机完成）。
- fd-vaas-login — 视频 + 图文平台登录管理页 — 查看各平台登录状态，触发扫码/窗口登录，轮询 QR 码。零依赖 Python stdlib HTTP Server。
- fd-vaas-publish-docs — 把一篇文章/图文一键分发到主流图文平台(知乎、微信公众号、小红书、抖音图文、快手图文、雪球、东方财富号、同花顺财经号、今日头条、百家号、微博)。**只做编排 -- 平台差异化文案/字数/标签/封面 + 内容适配 + 发布记录**。**上游优先原则**:vendored social-auto-upload(在 fd-vaas-publish-videos skill 里)有图文(Note)实现的平台(小红书、抖音图文、快手图文)走 `scripts/note_adapter.py` 复用上游,cookie 与视频发布共享;上游没有的平台才用自己的逻辑:**默认 ego-browser**(r…
- fd-vaas-publish-videos — >

## 智能体

- 萬星发布助手

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **vaas-wanxing** — 端点 `https://mcp.finddatatech.cloud/vaas-wanxing/mcp`（需要组：vaas-testers）

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```
