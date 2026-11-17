---
name: "fleet-artifact-draft-discipline"
description: "产物草稿纪律：schema 预检、数值来源标注、草稿与发布分离、source_ref 身份三元组。"
---

---
name: fleet-artifact-draft-discipline
description: 产物草稿纪律：schema 预检、数值来源标注、草稿与发布分离、source_ref 身份三元组。
---

写任何产物前按五条过：
① schema 预检——先 wire_dataset_schema 取列名与类型，列名不存在不落库；引用数据集/列名逐字 copy，不改写。
② 数值标注——每个数字带「来源数据集+数据期数/截至日期」；数据截至日期晚于数值本身的视为异常，先回报不落库。
③ 草稿优先——建/改用 wire_artifact_dashboard_create/doc_create/form_create/update，全部落草稿；发布只在用户明确同意后单独调用 wire_artifact_publish。
④ 身份三元组——见【舰队岗位身份】行时，把 fleet/post/title 原样拼成 source_ref 传给所有写入工具；无身份行不传空值。
⑤ 查重——同一委派重复落库前先 wire_artifact_list 查重，重复即更新不新建。
失败按四分类（凭据无效/网络不可达/上游错误/参数被拒）原文上报，不重试到假装成功。