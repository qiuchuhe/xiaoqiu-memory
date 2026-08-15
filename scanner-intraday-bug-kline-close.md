---
name: scanner-intraday-bug-kline-close
description: 策略1扫描器盘中买点信号计算用的是昨日K线收盘价，不是实时价
metadata: 
  node_type: memory
  type: project
  originSessionId: cec57627-3c55-4b03-a8c8-252a922993d6
  modified: 2026-08-15T07:35:31.559Z
---

## 问题

scanner.py 的 `deep_analyze()` 第107行用 `close_today = closes[-1]` 计算买点信号，但 `get_kline()` 从同花顺API取的日K线**盘中不包含当天K线**，最新一根是昨日收盘价。

## 后果

- 显示的 `price` 是腾讯实时行情（对的）
- 买点A/B的信号判断用的是昨日收盘价（错的）
- 导致盘中扫描的所有买入信号都是垃圾数据
- 典型：宝光股份实时已翻红+1%，但信号按昨日收盘算显示-2.5%

## 修复方向

- 盘中模式需用 `candidate["price"]`（腾讯实时价）替代 `closes[-1]` 做信号判断
- 或把实时价追加到 closes 数组末尾再算MA和信号
- K线API: `http://d.10jqka.com.cn/v2/line/hs_{code}/01/last.js`，盘中不含当日

## 教训

- 盘中扫描前必须先验证K线数据是否有今日
- 不能假设K线 `closes[-1]` 是实时价
- 输出报告时必须标注数据时间戳，区分实时价 vs 昨日收盘

## 同源坑：monitor.py 止损线（8/15 修复）

同一个「盘中 kl[-1]=昨日、kl[-2]=前日」的语义，在 monitor.py 里又踩了一次：

- `check_signals()` 和 `run_check()` 里 `prev_low = kl[-2]`，注释误写「kl[-2] 为昨日日K」
- 实际 kl[-2] 是**前日**低点，导致未盈利止损线一直用错（前日低×0.98 而非昨低×0.98）
- 已修复为 `kl[-1]`

教训：凡是用 get_kline 取「昨日」数据的代码，一律用 kl[-1]，kl[-2] 是前日。
