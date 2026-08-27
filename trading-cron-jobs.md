---
name: trading-cron-jobs
description: 交易时段自动扫描——盘中5窗口+盘后21:57+早间8:00，唯一调度器scheduled_scan.py
metadata: 
  node_type: memory
  type: project
  originSessionId: eaec323c-5b87-4b2a-aa70-1e37e7818350
  modified: 2026-08-27T04:12:23.169Z
---

# 交易时段自动扫描（2026-08-20 精简后）

2026-08-20 计划任务从 16 个精简到 7 个，盘中 6 窗口收敛到 5 窗口。

## 最终窗口（7 个计划任务）

| 时间 | 时段 | 脚本 | 峰/闲 |
|------|------|------|-------|
| 08:00 | 早间报告 | morning_report.py | 闲(半价) |
| 09:25 | 竞价扫描 | scheduled_scan.py → scanner auction | 峰 |
| 09:36 | 低开回升 | scheduled_scan.py → bounce | 峰 |
| 10:03 | 早盘主攻 | scheduled_scan.py → scanner+monitor+breakout+health | 峰 |
| 10:30 | 洗盘确认 | scheduled_scan.py → washout | 峰 |
| 14:33 | 尾盘决战 | scheduled_scan.py → scanner+monitor+health | 峰 |
| 21:57 | 晚间复盘 | scheduled_scan.py → scanner+monitor+nightly_dig | 闲(半价) |

## 关键设计

- **唯一调度器 = scheduled_scan.py**（每窗口精确跑对应脚本，无补扫逻辑）
- **洗盘(washout)十点后才成形** → 只放 10:30，不再 9:36/10:03 重复跑
- **启动确认(breakout)** 放 10:03
- **尾盘 14:33 最重要**（定明日操作）
- 已砍：13:07 午盘（信息增量小）、9:36/10:03 的重复 washout
- 旧 XiaoQiu_* catchup 定时任务已删（catchup_market 只用于开机补扫，不再挂定时）

## 报告规范

高峰时段（9-12/14-18）扫描总结要**精简省 token**，详见 [[scan-report-protocols]]、[[deepseek-pricing-offpeak]]。

[[morning-routine]] [[scheduled-task-cleanup-2026-08-20]]

## 2026-08-27 重设（删除旧任务+重新注册）

**原因**：0925/0936 任务电脑睡眠时错过触发，9:53 才补跑且返回 pending(0x800710E0)；旧 setup_tasks.ps1 只注册5个任务（缺1030/2157）、没设唤醒、ExecutionTimeLimit=72h 导致挂起。

**现状**：7 个任务已用 `setup_tasks_v2.ps1`（UTF-8 BOM）重新注册：
- 全任务 `WakeToRun=True`（睡眠唤醒运行）+ `StartWhenAvailable=True`（错过补跑）+ `ExecutionTimeLimit=30分钟`（防挂起）+ 失败重试1次
- 窗口：0925竞价/0936低开/1003早盘/1030洗盘/1307午后/1433尾盘/2157晚间
- 已实测触发 1307 成功（结果0）
- 注意：WakeToRun 只对睡眠生效，电脑彻底关机则唤不醒；早间 08:00 morning_report 任务当前不在计划内
