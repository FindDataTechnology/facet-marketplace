---
name: "facet-integrity-evidence-ladder"
description: "完整性证据阶梯：字段存在→字段可比→字段不一致三级，未暴露只报未取得。"
---

核对完整性与漂移，按证据三级走，不许跳级：
① 一级（字段存在）——facet_market_pack_read 响应里是否暴露 integrity/baseline/hash 类字段？不暴露就停在「未取得：integrity 面未暴露」，不判漂移。
② 二级（字段可比）——字段存在但缺基线/缺对照值时，只记录原值，不做一致性结论。
③ 三级（字段不一致）——期望值与实际值都可得且不一致，才记 drift_hits，并写「pack/字段名/期望值/实际值」。
处置纪律：只到三级才 escalate；escalate 附证据原文（不摘要失真）；下架/重发布等动作一律提案，不直调。抽样口径每轮固定并写清，保证跨轮可比。