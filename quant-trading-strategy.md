---
name: quant-trading-strategy
description: 小秋量化交易策略完整定义——双策略+风控+监控池
metadata: 
  node_type: memory
  type: project
  originSessionId: 1672ddc1-b864-477e-8846-71babb979d58
---

## 策略概述
基于同花顺模拟盘的小资金量化交易系统，项目目录：`D:\AI小秋\量化\`

## 核心约束
- **股价上限**: 20元以内（策略B代码里是25，但用户要求20元以内）
- **模拟本金**: 3000元
- **单笔股数**: 固定100股
- **最大持仓**: 1只股票
- **券商**: 国信证券 → 同花顺下单端 (xiadan.exe)

## 双策略信号

### 策略A：均线金叉/死叉
- MA5 上穿 MA20 → 买入信号
- MA5 下穿 MA20 → 卖出信号
- 适用于短线快进快出

### 策略B：多头排列+温和放量
- 条件1: MA5 > MA10 > MA15 > MA30（多头排列）
- 条件2: 当日涨幅 1%~5%
- 条件3: 成交量 = 5日均量的 1.2~3.0 倍（温和放量）
- 条件4: 股价 < 25元
- 全部满足 → 买入信号

## 风控参数
- **止损线**: -5%（持仓亏损超5% → 次日开盘卖出）
- **止盈线**: +10%（持仓盈利超10% → 次日开盘卖出）
- **扫描间隔**: 60秒

## 监控池（2026-06-01）
- 漫步者 (002351) ~11.48元 | 策略B | max_price: 11.60
- 百润股份 (002568) ~20.19元 | 策略A | max_price: 20.50
- 中原高速 (600020) ~4.20元 | 策略B | max_price: 4.20

## 自选股池
99只自选股保存在 `.my_watchlist.json`，全部为20元以内低价股。

## 关键文件
- `stock_quant.py` - 量化工具集（行情看板/选股/回测/均线分析）
- `xiaoqiu_monitor.py` - 双策略实时监控 + 模拟交易引擎
- `auto_trade.py` - 自动交易引擎（easytrader+同花顺）
- `stock_watch.py` - 自选股监控
- `.my_watchlist.json` - 99只自选股
- `.monitor_state.json` - 持仓/资金状态
- `.trade_log.txt` - 交易日志

## 运行方式
```bash
cd D:\AI小秋\量化
python xiaoqiu_monitor.py --dry          # 模拟盘模式(只看信号不下单)
python xiaoqiu_monitor.py --once         # 单次扫描
python xiaoqiu_monitor.py --interval 120 # 每120秒扫描
```

## Git备份
项目已用git管理，remote: GitHub，exit时自动commit+push。
详见 [[auto-backup-on-exit]]

**Why:** 昨天和小秋一起制定的完整策略，今天对话中我忘记了，必须永久记录。
**How to apply:** 每次涉及量化交易时，先读取此记忆，确认策略参数和约束。
