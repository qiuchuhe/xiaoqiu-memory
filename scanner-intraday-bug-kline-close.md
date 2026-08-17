---
name: scanner-intraday-bug-kline-close
description: 同花顺K线"是否含今日"随时间变化，取"昨日"数据必须动态判断不能写死索引
metadata: 
  node_type: memory
  type: project
  originSessionId: cec57627-3c55-4b03-a8c8-252a922993d6
  modified: 2026-08-17T15:49:20.328Z
---

## 核心规律（8/17 实测确认）

同花顺 K线接口 `http://d.10jqka.com.cn/v2/line/hs_{code}/01/last.js` 返回的"最后一根是否含今日"是**随时间的**：

| 场景 | kl[-1] | kl[-2] |
|---|---|---|
| 盘中（交易时段） | 昨日 | 前日 |
| 盘后（收盘后） | 今日 | 昨日 |

所以 **`get_kline()` 里没有固定的"昨日=kl[-1]"或"昨日=kl[-2]"**，取决于当前是盘中还是盘后。

## 正确做法：动态判断

```
has_today = str(kl[-1]["date"]).replace("-", "") == today
昨日K线 = kl[-2] if has_today else kl[-1]
```

已落地两处（口径一致）：
- `monitor.py` 的 `_kline_has_today(kl)`（主题4，2026-08-17）
- `scanner.py` 的 `prev_kline`，挂在已有的 `last_kline_date != today_str` 判断里（P3，2026-08-17）

## 踩坑历史（三次）

1. **scanner.py 盘中买点信号**：`close_today = closes[-1]` 盘中取成昨日收盘，13只假信号。已修（盘中修复 v2.1，用腾讯实时价填充）。
2. **monitor.py 止损线 prev_low**：先写 `kl[-2]`（盘中取成前日低），8/15 改成 `kl[-1]`——但那只对盘中成立，**盘后 kl[-1]=今日又会取成今低**。8/17 用 `_kline_has_today` 彻底修对。
3. **scanner.py prev_low/open_chg**：`prev_low=kl[-2]` 盘中错、`open_chg` 基准 `kl[-1]["close"]` 盘后错。8/17 统一改用 `prev_kline`。

## 教训

- 凡是用 `get_kline` 取「昨日」数据的代码，**一律动态判断是否含今日**，禁止写死 `kl[-1]` 或 `kl[-2]`。
- 早期记忆"一律用 kl[-1]"是错的（只对盘中成立），已在本文件纠正。
- 盘中扫描前先验证 K线是否含今日；输出报告标注数据时间戳，区分实时价 vs 昨日收盘。
