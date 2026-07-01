---
name: trading-cron-jobs
description: 四个交易日时段自动扫描任务——竞价/早盘/午盘/尾盘
metadata: 
  node_type: memory
  type: project
  originSessionId: 98e619f2-8da7-4865-b783-6a2ba4bed555
---

# 交易时段自动扫描

## 四个时段

| 时间 | 时段名 | 逻辑 | Cron Job ID |
|------|--------|------|-------------|
| 9:25 | 竞价扫描 | 集合竞价异动：高开放量、跌停打开、竞价爆量 | `214428c4` |
| 10:03 | 早盘主攻 | 第一波走势幅度最大：封板/炸板、量比突增、板块联动。+持仓监控 | `d24f3050` |
| 10:57 PM | 晚间掘金 | 盘后情报收集+掘金报告 | `1b2b17ee` |
| 13:07 | 午盘开盘 | 大单突袭、上午横盘午后突破、涨停是否炸板 | `ed525c25` |
| 14:33 | 尾盘偷袭 | 尾盘拉升/砸盘、明日信号预埋。+朋友框架过滤 | `d743bba1` |

## 执行内容

每个时段执行：
1. `cd D:\AI小秋\策略量化\策略1 && python scanner.py --once`
2. 10:03 额外执行: `cd D:\AI小秋\量化 && python monitor.py --once`
3. 14:33 额外执行: 用朋友框架过滤热门题材+龙头

## 限制

- 周一到周五执行 (`1-5`)
- 每 7 天自动过期，需续期
- 需 Claude Code 在运行中
- 电脑需保持唤醒

## 脚本

- `C:\Users\ASUS\setup_tasks.ps1` — 配置说明文档

[[morning-routine]] [[friend-market-wisdom]]
