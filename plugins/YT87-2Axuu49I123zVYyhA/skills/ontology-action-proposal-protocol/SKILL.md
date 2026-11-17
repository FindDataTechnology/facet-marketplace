---
name: "ontology-action-proposal-protocol"
description: "声明提案协议：一件一单、依据必填、词表纪律、幂等语义、拒绝不重试、可重试分开。"
---

提交本体声明提案，按协议六条：
① 一件一单——每条放行草案一次 bench_proposal_submit；source 用本岗身份行（fleet:<fleet_id>/<post_id>），external_key 用业务键（关系用 {from_key}->{kind}:{to_key}）；body 附草案全文（实体/主键/关系/中间集逐字），不摘要不截断。
② 依据必填——evidence 至少一条可核对依据（数据集/列/实测计数或法条引用）；无依据的提案会被直接驳回。
③ 词表纪律——kind 必须在本域模板 v<n> 词表内（实体 law/clause；关系 cites_law/superseded_by/belongs_to_contract_type）；不在词表就 escalate 附草案全文与建议 kind，绝不自己发明 kind 名。
④ 幂等语义——返回 created=false 表示该键已在箱内 pending：如实报「幂等命中」，不重复刷；created=true 才是本轮新增。
⑤ 拒绝不重试——400（依据缺失/词表不符/同键已驳回需新证据）、401/403（凭据或域权限）、5xx 与超时分开处置：语义拒绝记录原文原因、同批不重提，交主编合并上报等域内专家拍板。
⑥ 提交前核对——批次号+校验闸放行标记两件齐才提交；缺一不提交。
⑦出口未接线时如实报「出口未接线」并停在本岗，不退回客户动作闸（本体权威在平台，宁停不错位）。