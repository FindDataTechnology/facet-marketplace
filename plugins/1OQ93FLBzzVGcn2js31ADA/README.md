# 文本引擎 · 论文研究套件

学术论文的检索与研究：文献综述（方向定位与脉络梳理）、方法追踪（提出/变体/应用谱系）、相关工作（语义相似文献与对比矩阵）。语料覆盖 arXiv q-fin 与 cs.CL 等八个类目的千级样本（元数据 + 摘要，CC0 元数据）；外部开放检索（arXiv/OpenAlex/Semantic Scholar）编排补充。**版权红线**：只返回元数据、摘要、切块引用与指回 arXiv.org 的原文链接，本服务不提供也不外发论文全文。检索走 fd-text-core 引擎的混合通道（关键词 ⊕ 语义向量 Qwen3-Embedding-8B）。

## 技能

- paper-literature-search — 论文文献检索（文本引擎·论文包入口技能）。当用户想查某个研究方向的论文、做文献综述、了解某领域有哪些工作、或要求"找找关于 XX 的论文""这个方向的研究现状"时使用。驱动从方向定位到文献梳理的流程，数据经 fd-text-core 引擎的论文语料与外部开放检索（arXiv/OpenAlex/S2）实时获取。
- paper-method-tracking — 论文方法追踪（文本引擎·论文包）。当用户想查某个方法/技术（如 transformer、对比学习、GARCH）在学术论文里怎么用、有哪些变体、谁提出的，或要求"追一下 XX 方法的发展"时使用。按方法名驱动检索与脉络梳理，数据经 fd-text-core 引擎实时取。
- paper-related-work — 论文相关工作与相似文献（文本引擎·论文包）。当用户给出一段摘要/题目/研究想法，要求找相似论文、写相关工作段落、或做查重式比对（"有没有做过类似的"）时使用。按参考文本的语义向量找近邻，数据经 fd-text-core 引擎实时取。

## 智能体

- 论文研究助手

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **fd-text-core** — 端点 `https://mcp.finddatatech.cloud/fd-text-core/mcp`（需要组：analysts）

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```
