# 文本引擎 · 剧本经典套件

公版经典剧本的检索与研究：结构分析（幕场/折出骨架与情节推进）、角色检索（台词聚合与形象分析）、场景检索（按描述定位并细读整场）、相似段落（语义向量找近邻，跨用词差异找情境）。语料覆盖莎士比亚、希腊悲剧、元杂剧、明清传奇、现代话剧（老舍）、京剧，共 18 部；每个结果携带 provenance 三件套（source/stable_id/as_of）。检索走 fd-text-core 引擎的混合通道（关键词 CJK bigram + 繁简归一 ⊕ 语义向量 Qwen3-Embedding-8B）。

## 技能

- screenplay-structure-analysis — 剧本结构分析（文本引擎·剧本包入口技能）。当用户想了解一部戏剧/戏曲的幕场骨架、情节推进、折出结构，或要求梳理某剧的场次脉络、剧情大纲时使用。驱动从结构骨架到情节脉络的分析流程，数据经 fd-text-core 引擎的剧本语料实时检索。
- screenplay-character-search — 剧本角色检索与分析（文本引擎·剧本包）。当用户想查某个人物在某部剧里说了什么、出场段落、角色形象分析，或要求"找出哈姆雷特的独白""窦娥的唱词""这个角色在全剧中的台词"时使用。按角色名驱动台词级检索，数据经 fd-text-core 引擎实时取。
- screenplay-scene-search — 剧本场景检索与细读（文本引擎·剧本包）。当用户想查某部剧的某个具体场景（"哈姆雷特的戏中戏""窦娥临刑那场""西厢记的送别"）、某个情境段落、或要求细读某场戏的完整内容与调度时使用。按场景描述驱动检索，数据经 fd-text-core 引擎实时取。
- screenplay-similar-passages — 剧本相似段落检索与比较（文本引擎·剧本包）。当用户给出一段剧本文字想找"类似的其他段落"、要求跨剧对比相似情境（如"还有哪些戏写了临刑发誓""找和这段相似的独白"）、或做母题/桥段溯源时使用。按参考文本的语义向量找近邻，数据经 fd-text-core 引擎实时取。

## 智能体

- 剧本研究助手

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **fd-text-core** — 端点 `https://mcp.finddatatech.cloud/fd-text-core/mcp`（需要组：analysts）

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```
