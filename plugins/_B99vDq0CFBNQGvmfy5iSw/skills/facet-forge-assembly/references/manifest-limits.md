# manifest 字段与限额（发布前必读）

> 来源：平台源码 `lib/pack-manifest.js` 的 `PACK_LIMITS` 与 `validatePackManifest`。
> 数字与本文不符时**以源码为准**。本文件与源码同步日期：2026-10-16。

## 顶层字段

| 字段 | 必填 | 限额 | 说明 |
|---|---|---|---|
| `name` | ✅ | ≤80 字符 | pack 显示名（市场里用户看到的） |
| `description` | | ≤2000 字符 | **决定用户能否搜到它**——写清给谁、解决什么、数据从哪来 |
| `tags` | | ≤10 个，每个 ≤24 字符 | 货架标签 |
| `skills` | 三者至少一个非空 | ≤10 个 | 见下 |
| `mcpServers` | 同上 | ≤5 个 | 见下 |
| `agents` | 同上 | ≤3 个 | 见下 |
| `notes` | | ≤500 字符 | 版本说明（随版本不可变） |
| `visibility` | | `public` \| `private` | 默认 public |
| `category` | | slug 1-32 位 `[a-z0-9-]` | 货架分类（运营可改） |
| `guide` | | `steps` 恰好 3 步 | 三步使用指南，见 `three-step-guide.md` |

## `skills[]` —— 内联技能

| 字段 | 必填 | 规则 |
|---|---|---|
| `name` | ✅ | 匹配 `[A-Za-z0-9._-]`，1-64 字符，包内不得重复 |
| `description` | ✅ | 非空，≤300 字符（市场展示 + 触发机制，写清"什么时候用"） |
| `content` | ✅ | 技能全文，≤64KiB（含 frontmatter） |
| `files` | | 辅助文件 `{相对路径: 文本}`，单文件 ≤512KiB、≤64 个、合计 ≤2MiB |
| `frontmatter` | | 随技能存储的 frontmatter 对象 |

`files` 路径规则：**禁**绝对路径、`..`、反斜杠、盘符；只收纯文本；
键不得是 `SKILL.md` 的任何大小写变体（那是主文档）。
带脚本扩展名（py/sh/js 等）的 `files` 会让装包弹一次确认框。

## `mcpServers[]` —— 只能引用在册服务器

| 字段 | 规则 |
|---|---|
| `registryName` | ✅ 平台在册的 server 名（用 `search_registry` / `list_services` 查） |
| `requiredGroup` | 可选，目标用户组（用户组不相交则装不上） |
| `allowTools` | 可选，工具叶子名数组，≤64 个；**只能收窄**，不能超出 server 实际工具 |

**禁止**任何 `url` / `command` / `args` / `env` / `headers`——v1 安全边界，pack 不能自带端点。
声明了 `allowTools` 的 manifest 会过**工具声明门**：每个名字都要能对上有权使用的 server 工具面。

## `agents[]` —— 角色（persona-only）

| 字段 | 规则 |
|---|---|
| `id` | ✅ 不为空；**不得**以 `user.` 开头（那是用户自建预设的保留前缀） |
| `name` | ✅ ≤60 字符 |
| `persona` | ✅ ≤16KiB |

**禁止** `baseUrl` / `model` / `apiKey`——角色只定人格，不定运行时。

## 上线前会过的门（`validate` 会一次报全）

1. **结构限额**——即上表。报错形如 `{error: "...", entry: "skills[2]"}`。
2. **files 门**——带辅助文件时，市场需已开门；否则 `code: "pack-files-disabled"`。
3. **工具声明门**——声明了工具名才触发。名字对不上时给
   `toolGate: true`，并说明是"快照未登记"还是"确实不存在"。
4. **续版归属**——带 `packId` 时校验存在性与作者身份。

## 常见拒绝与改法

| 判词关键词 | 改法 |
|---|---|
| `mcpServers[0]` + `registry servers` | MCP 条目带了 url/command 等禁用键——只留 `registryName` |
| `persona-only` | agent 条目带了 model/baseUrl/apiKey——删掉 |
| `name must be a non-empty string of at most 80` | 包名超 80 字符 |
| `skill N description` | 技能 description 空或超 300 |
| `content` 相关 | 单技能正文超 64KiB——拆成两个技能，或用 `files` |
| `tag N` | 标签超 24 字符或超 10 个 |
| `guide.steps must be an array of exactly 3 steps` | 使用指南必须恰好 3 步 |
| `does not expose declared tool(s)` | 工具名对不上——用发现工具核准确的叶子名 |
| `is surface-registered; tools outside its commercial set` | 该 server 只开放了部分工具给 pack，换名单内的 |
| `no registry snapshot entry for declared tool(s)` | 该 server 的工具面快照未登记——换已登记的 server，或联系平台 |
