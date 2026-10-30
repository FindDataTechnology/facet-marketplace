---
name: "facet-install-guidance"
description: "两条安装路径引导：壹座一键订阅安装 / CLI 安装到 Claude Code / Cursor"
---

# 安装引导（两条路径）\n
## 路径一：壹座内（推荐给壹座用户）
市场条目页「订阅并安装」一键完成：订阅记录 + 内容物化到本 cell 对话运行时；MCP 凭据已在壹座内接线。

## 路径二：CLI 安装到外部编辑器
`npx @finddatatechnology/facet install <pack id 或含 id 的 URL>`
- 默认装进 Claude Code；`--target cursor` 装 Cursor；`--project <dir>` 装到指定项目。
- 落位是技能快照到磁盘的技能目录；MCP 引用只打印出来，CLI 不写 MCP 配置、不持有凭据。
- 帮助命令：`facet help`。

引导时先问用户在哪个环境（壹座 or Claude Code/Cursor），再给对应路径。
