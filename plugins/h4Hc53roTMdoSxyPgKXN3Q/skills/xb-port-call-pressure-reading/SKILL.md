---
name: "xb-port-call-pressure-reading"
description: "口岸水位读数纪律：port.calls 当日快照不回填，缺港按 series_state 双态归因，拥堵与吞吐分列不混算。"
---

读 port.calls 观测按本纪律：
① 快照语义——portwatch 是当日快照源，历史不回填；昨日缺港今天补不上，缺位即缺位，禁止用相邻日外推。
② 双态归因——read 返回带 series_state：never_held=该口岸序列确认没有（覆盖问题，如实写），gap_in_series=当期未取得（上游冻结/延迟，写四分类原因）；两类分开列，不混报。
③ 分列不混算——拥堵指数与在港艘次分列报，不互相换算合并成单一「压力分」。
④ 同序对比——异动只比同港同概念同序列；跨港跨源对比标不可比。
⑤ 覆盖如实——ports_covered 只数本轮真读到观测的港，未读到不计入。