---
name: quant-trading-strategy
description: 小秋量化交易策略完整定义——策略一为主，旧代码已精简
metadata: 
  node_type: memory
  type: project
  originSessionId: 37d1cc60-30ad-4ee2-89ab-dcd6d19fa693
---

## 项目结构

```
D:\AI小秋\
├── 策略量化\              ← 🆕 策略代码（新建）
│   └── 策略1\             ← 均线多头+温和放量
│       ├── config.py      ← 参数配置
│       ├── scanner.py     ← 扫描引擎
│       └── README.md      ← 策略说明
│
├── 量化\                  ← 工具集 + 历史代码
│   ├── stock_quant.py     ← 行情看板/回测/自选股管理（已精简）
│   ├── xiaoqiu_monitor.py ← 实时监控（待更新对接策略1）
│   ├── xiaoqiu_launcher.py ← 统一启动器 v2.0
│   ├── alert_daemon.py    ← 止损止盈预警
│   ├── auto_trade.py      ← easytrader下单
│   ├── 闲鱼代写/          ← 3进4打板策略
│   └── .my_watchlist.json ← 自选股列表
│
├── xiaoqiu-memory/        ← 记忆备份（独立repo）
└── .gitignore
```

## 策略一：均线多头+温和放量

见 [[策略一最终参数]]（待创建），或直接看 `D:\AI小秋\策略量化\策略1\config.py`

**运行方式：**
```bash
cd D:\AI小秋\策略量化\策略1
python scanner.py              # 盘后全量扫描
python scanner.py --live 300   # 盘中监控
python scanner.py --once       # 单次快扫
```

## 旧策略清理记录（2026-06-07）

已删除的策略/文件：
- screen/screen2/signal 三个选股命令（与策略1重叠）
- paper_trader.py / daily_report.py / config_tomorrow.py
- update_watchlist.py / stock_watch.py / stock_watch.sh

保留的功能：
- stock_quant.py: 行情看板/回测/自选股管理/同花顺深度数据
- xiaoqiu_monitor.py: 待重写对接策略1
- alert_daemon.py: 预警守护
- auto_trade.py: 自动交易

## GitHub备份
- Remote: https://github.com/qiuchuhe/xiaoqiu-quant
- 目录: D:\AI小秋\
- exit时自动 commit+push

**How to apply:** 选股用策略1(scanner.py)，看行情/回测用stock_quant.py，监控用xiaoqiu_launcher.py。
