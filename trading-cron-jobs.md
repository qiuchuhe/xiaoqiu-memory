---
name: trading-cron-jobs
description: 六个交易日时段自动扫描——竞价/低开/早盘/午盘/尾盘/晚间
metadata: 
  node_type: memory
  type: project
  originSessionId: 98e619f2-8da7-4865-b783-6a2ba4bed555
---

# 交易时段自动扫描 v2.0

## 六个时段

| 时间 | 时段 | 扫描内容 | Cron ID |
|------|------|----------|---------|
| 9:25 | 竞价异动 | scanner.py 全市场 + 判断今日方向 | `3b932ca9` |
| 9:36 | 低开回升 | tech_bounce_scan.py 跌停板翘起+板块联动 | `60592a3b` |
| 10:03 | 早盘主攻 | scanner.py + monitor.py + 综合三轮对比 | `a6d99294` |
| 13:07 | 午盘开盘 | scanner.py + 横盘突破/炸板检测 | `d08e8695` |
| 14:30 | 尾盘决战 | scanner.py + monitor.py + 朋友框架过筛 + 明日交易计划 | `de6ff3bf` |
| 21:57 | 晚间掘金 | nightly_dig.py + 全天总结 + 明日预案 | `f9d87bd3` |

## 每个时段执行流程

1. 到点触发 → Claude处理prompt → 执行扫描命令
2. 读取扫描结果 → 分析数据 → 输出结论
3. 结论包含：信号汇总 + 板块判断 + 操作建议
4. 14:30 额外输出【明日交易计划】
5. 21:57 额外输出【明日早盘预案】

## 核心改进（v1→v2）

- 新增 9:36 低开回升专项扫描
- 所有时段都要求输出"结论"，不只是跑脚本
- 14:30 强制走朋友框架过滤
- 21:57 要求"一式两份"预案

## 限制

- 周一到周五执行 (1-5)
- 每 7 天自动过期，需续期
- 需 Claude Code 在运行中 + 电脑保持唤醒
- 如果电脑休眠/Claude退出，任务会丢失

[[morning-routine]] [[friend-market-wisdom]]
