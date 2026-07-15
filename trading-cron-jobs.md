---
name: trading-cron-jobs
description: 七个交易日时段扫描 v3.0——scanner全市场为主→朋友框架过滤
metadata: 
  node_type: memory
  type: project
  originSessionId: 98e619f2-8da7-4865-b783-6a2ba4bed555
---

# 交易时段自动扫描 v3.0

## 核心逻辑

```
scanner全市场技术筛选 → Claude朋友框架(热门+龙头+情报)过滤垃圾/冷门/杂毛 → 精选候选
```

**先全后精，不跳过任何股票。**

## 七个时段

| 时间 | 时段 | 扫描内容 | 说明 |
|------|------|----------|------|
| 8:00 | 早间外盘 | WebSearch隔夜美股+要闻 | Claude手动执行 |
| 9:25 | 竞价异动 | **scanner.py** + track_b | 全市场初筛 + 热门板块参考 |
| 9:36 | 低开回升 | tech_bounce_scan.py | 跌停翘起+低开回升 |
| 10:03 | 早盘主攻 | **scanner.py** + monitor.py | 全市场 + 持仓巡检 |
| 13:07 | 午盘开盘 | **scanner.py** | 全市场午盘扫描 |
| 14:30 | 尾盘决战 | **scanner.py** + monitor.py | 全市场 + 朋友框架 + 明日计划 |
| 21:57 | 晚间复盘 | **scanner.py** + monitor.py | 盘后全量 + 全天总结 + 明日预案 |

## v2→v3 变化

- 9:25: track_b → **scanner + track_b**（全市场为主，热门为参考）
- 10:03: track_b → **scanner**（全市场）
- 13:07: track_b → **scanner**（全市场）
- 14:33→14:30: 时间修正回整点
- 🆕 21:57: 新增晚间复盘窗口

## 每个时段执行流程

1. 到点触发 → Claude处理prompt → 执行扫描命令
2. scanner全市场初筛 → 技术条件过滤 → 候选池
3. Claude读取结果 → 过朋友框架(热门题材+龙头排名+情报点名) → 过滤垃圾冷门杂毛
4. 结论包含：信号汇总 + 板块判断 + 操作建议
5. 14:30 额外输出【明日交易计划】
6. 21:57 额外输出【明日早盘预案】

## 实现方式

使用 **Windows 任务计划器**（非 Claude cron），开机即运行，不依赖 Claude Code/VS Code。

```
注册脚本: D:\AI小秋\策略量化\策略1\setup_tasks.ps1
输出文件: D:\AI小秋\策略量化\策略1\catchup_result.json / catchup_report.md
```

Claude 打开时直接读取结果文件，无需等 cron 触发。

## 管理

```powershell
taskschd.msc                          # 图形化管理
schtasks /Query /TN "小秋量化_*"       # 查看所有任务
```

[[morning-routine]] [[friend-market-wisdom]]
