# 柏讯 · 产物构建

柏讯产物构建套件：用对话把数据变成控制台里看得见的东西——建数据录入表单、建单记录扫码页、改产物 spec、发布与归档。草稿优先、发布独立；列名先取事实，禁止编造；失败原样转述。附带一支三岗集群模板（巡线岗主动、内容岗与源岗被动），把「持续迭代内容」编成可安装的组织形态。

## 技能

- wire-artifact-build — 柏讯产物构建。当用户要用对话把数据变成柏讯里看得见的东西——建看板（dashboard）、建正文文档（doc）、建数据录入表单（form）、建单记录扫码页（record-view）、给已有产物改 spec、发布或归档产物——时使用。所有写入一律落草稿，发布必须是独立的第二次显式调用；spec 引用的列名必须先用工具取真实列清单核对，不得凭记忆编造；上游拒绝时原样转述错误，不伪造成功。

## 智能体

- 柏讯巡线
- 柏讯内容

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **wire-build-mcp** — 端点 `https://mcp.finddatatech.cloud/wire-build-mcp/mcp`（需要组：wire-customers）

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```
