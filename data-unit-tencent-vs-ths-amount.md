---
name: data-unit-tencent-vs-ths-amount
description: 腾讯行情成交额单位是万元、同花顺K线成交额单位是元，算比值前必须对齐否则差1万倍
metadata: 
  node_type: memory
  type: reference
  originSessionId: eaec323c-5b87-4b2a-aa70-1e37e7818350
  modified: 2026-08-19T18:00:15.993Z
---

腾讯行情 `amount`(f[37]) 单位是**万元**；同花顺K线 `parts[6]`(成交额) 单位是**元**，两者差 1 万倍。实测 600519：腾讯 amount=487677(万) ↔ 同花顺 4876774800(元)。

**Why:** 任何"今日成交额 ÷ 昨日成交额"这类比值（如竞价占比、量能环比）若直接拿两源原始值相除，会差 10000 倍，导致阈值全错。

**How to apply:** 同花顺K线成交量 `volume`(parts[5]) 单位是**股**、腾讯 volume(f[6]) 单位是**手**（差 100 倍），同样要对齐。跨源比额/比量前先统一单位（腾讯额×10000=元，或同花顺额÷10000=万元）。关联 [[scanner-intraday-bug-kline-close]]。
