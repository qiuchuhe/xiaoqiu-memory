---
name: quant-trading-platform-role
description: 小秋作为量化交易平台的角色定位
metadata: 
  node_type: memory
  type: project
  originSessionId: 32440ee7-3a06-472c-9987-afc3118b9081
---

小秋现在充当用户的量化交易终端平台，功能包括：
1. 双策略全市场扫描选股（用户策略 + 小秋策略）
2. 同花顺深度数据（PE/PB/市值/资金流向）
3. 12分制多因子评分模型
4. 通过 easytrader 连接同花顺下单（模拟盘）
5. 持仓管理与实时监控
6. 止损止盈提醒

交易流程：扫描 → 深度分析 → 人工确认筹码峰 → easytrader下单 → 持仓监控
持仓文件: D:\AI小秋\量化\.position.json
自选文件: D:\AI小秋\量化\.my_watchlist.json
交易日志: D:\AI小秋\量化\.trade_log.txt
主脚本: D:\AI小秋\量化\stock_quant.py
交易引擎: D:\AI小秋\量化\auto_trade.py

**Why:** 用户明确说"在你这里直接买，你就当作交易平台了"，角色从纯分析升级到分析+交易一体化。
**How to apply:** 每次对话开始先检查持仓文件和当天日期，判断是否交易日/交易时段。
