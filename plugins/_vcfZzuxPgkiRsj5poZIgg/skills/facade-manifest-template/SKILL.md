---
name: "facade-manifest-template"
description: "分身 manifest 骨架：可直填的完整结构，含分身配置块与语料/接待的最小示例"
---

# 分身 manifest 骨架（替换 ⟨⟩ 占位后即可发布）

manifest 里的 `facade` 块就是「AI 分身」的配置字段（facade 是开发内部叫法，界面上都叫 AI 分身）。

```json
{
  "name": "⟨分身名称⟩",
  "description": "⟨一句话：和这个分身聊能了解什么；写清「回答出自随版本冻结的策展语料，答不了的如实说」⟩",
  "tags": ["分身"],
  "visibility": "public",
  "category": "company",
  "skills": [
    {
      "name": "corpus-identity",
      "description": "⟨这一类语料是什么，≤300 字⟩",
      "content": "# ⟨语料标题⟩（语料截至 ⟨YYYY-MM-DD⟩）\n\n## ⟨小节：一个事实主题⟩\n\n⟨整理后的素材⟩\n\n## ⟨小节⟩\n\n⟨……⟩"
    },
    {
      "name": "corpus-work",
      "description": "⟨第二类语料⟩",
      "content": "# ⟨……⟩（语料截至 ⟨YYYY-MM-DD⟩）\n\n⟨……⟩"
    }
  ],
  "agents": [
    {
      "id": "reception",
      "name": "⟨接待角色名⟩",
      "persona": "你是⟨分身主人⟩的接待角色——这个分身对外的那张嘴。\n\n你的知识全部来自本包语料技能。回答前先对一下语料截至时间：语料没覆盖的就说「这个我答不了」或「可能过期了（截至 X）」，并建议访客直接联系分身主人。宁可诚实说不知道，绝不编造。\n\n回答风格：像一位熟悉⟨分身主人⟩的同事，直接、具体、不客套堆砌。\n\n价格、合同条款、对外承诺：一律不答——用 matter_open 开一个事项转给分身主人（带摘要与访客线索），把事项号告诉访客；访客问进展用 matter_status / matter_read 原样转述。",
      "serving": {
        "protocol": "a2a",
        "toolFace": "chat",
        "card": {
          "name": "⟨分身名称⟩",
          "description": "⟨与包 description 呼应的一句话⟩",
          "examples": [
            "⟨访客最可能问的第一个问题⟩",
            "⟨第二个⟩",
            "⟨第三个⟩"
          ]
        }
      }
    }
  ],
  "facade": {
    "reception": "reception",
    "corpus": ["corpus-identity", "corpus-work"],
    "delegation": [],
    "stance": "hosted"
  },
  "notes": "v1 纯接待。语料由分身工坊协助整理。"
}
```

## 字段速查

- `facade.reception`：必须等于 agents 里某个 id，且该 agent 必须带 `serving`（这是部署为对话服务的开关）。
- `facade.corpus`：数组，每一项必须是 skills 里真实存在的 name——接待的知识边界就是这几份语料。
- `facade.delegation`：留空 `[]` = 纯接待（v1 推荐起步）；以后要加专家再发新版本。
- `facade.stance`：v1 只能写 `hosted`（访客免费、主人出钱）。
- `serving.card.examples`：1-3 条，会显示在目录和分身入口当「一键试问」。
- 语料正文里**不要**写密码、私钥、客户隐私——分身是公开可聊的。
