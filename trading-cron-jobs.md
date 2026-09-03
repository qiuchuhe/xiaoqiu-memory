---
name: trading-cron-jobs
description: 交易时段自动扫描——盘中5窗口+盘后21:57+早间8:00，唯一调度器scheduled_scan.py
metadata: 
  node_type: memory
  type: project
  originSessionId: eaec323c-5b87-4b2a-aa70-1e37e7818350
  modified: 2026-09-03T14:56:35.243Z
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

## 2026-08-27 修复（黑窗根源 + 自动汇报）

**黑窗根源**：scheduled_scan.py 用 pythonw 启动后，内部 subprocess 调 python.exe 跑 scanner/monitor 等 → 每个子进程弹"无内容黑窗"（capture_output 把输出收走了）；量化后端 main.py 也用 python.exe 弹窗。

**修复**：
- scheduled_scan.py `run_script` 加 `creationflags=0x08000000`（CREATE_NO_WINDOW）→ 子进程静默不弹窗
- 后端改 `start_backend_silent.py`（stdout/stderr 重定向 server.log）+ pythonw.exe 启动 → 无黑窗，端口 8766
- scheduled_scan.py 升级：结果加时间戳历史归档（`scheduled_scan_history/`，防手动/补跑污染 latest）+ 生成 markdown 报告（`扫描报告/扫描报告_YYYYMMDD_HHMM_窗口.md`，含 scanner候选表/monitor/health/nightly_dig引用）
- 真实 14:33 已实测：3 scripts ok、报告+归档落盘、无残留进程

**cron 自动汇报**：7 个 durable cron（写入 .claude/scheduled_tasks.json，VSCode 重启不丢；旧的 session-only 重启即失），每窗口后约 10 分钟触发（9:34/9:45/10:13/10:40/13:17/14:43/22:08），prompt 校验 generated_at 新鲜度（今天+≤25分钟）+ window 防读到被覆盖旧数据。
⚠️ durable recurring **7 天自动过期**需重建；触发仍依赖对话空闲，忙时排队延迟；兜底=读 `扫描报告/` 最新 md。

## 2026-08-31 汇报链路修复（爸爸反馈"早上扫描显示已过期/结论不进对话"）

**根因3个叠加**：
1. **7个cron汇报任务延迟~30分钟触发**（9:34→实际10:04、9:45→10:15、10:13→10:43、10:40→11:10）→ 超25分钟新鲜窗口 → 全判"过期"
2. **报告实际是 `.html`**（scheduled_scan.py generate_report 8/28起生成 html 表格报告），但汇报 prompt 还 Glob 找 `.md` → 永远读不到报告
3. 触发依赖 REPL 空闲，会话忙/关时排队延迟

**修复**：删7旧+建7新 cron（同触发时间），prompt 全部重写：
- **去掉"距当前≤25分钟"新鲜窗口** → 改"当天窗口已跑即有效，无论何时查询都报结论"（结论带时间戳，不会误导）
- **Glob 找 `扫描报告_<今天>_*_窗口.(html|md)`**
- 结论从报告/结果文件提取：策略一_result.json(bounce_signal.txt/morning_washout_result.json/market_health.json)
- 当天窗口未跑才说"今日XX扫描未生成"
- 新任务id: 竞价fcbcf9ab/低开292b82fb/早盘9dda2516/洗盘01730957/午后c4a089bf/尾盘9d873494/晚间487667da。**8/31创建，9/7过期需重建**。

**注意**：报告格式已是 `.html`（含scanner候选表/monitor/health/washout过程），不是 .md；洗盘10:30不在Windows计划任务（register_tasks.ps1只到1433），靠 catchup_market.py 开机补扫兜底（支持10:30窗口）。8/31已验证当天4窗口结论可对话内汇报（竞价0候选/低开7+5只/早盘0候选+大盘普跌/洗盘12只）。更正：**Windows任务实际含 10:30 洗盘**（秋策_洗盘确认_1030，8/27 setup_tasks_v2 注册7个），此条"注意"已过时。

## 2026-09-03 后台AI解读·收盘定论模式（爸爸不在页面也能拿结论）

**背景**：爸爸反馈"定点选股失效、对话框不生成除非手动让我来"。根因双层：①Windows扫描任务（名=**秋策_竞价扫描_0925** 等7个，动作=`pythonw scheduled_scan.py <窗口>`）**独立后台一直正常跑**，报告全落D盘（扫描报告/*.html + scheduled_scan_result.json + scheduled_scan_history/）；②Claude内置cron汇报（fcbcf9ab等7个durable）**只在 Claude Code 会话存活+空闲才触发**，爸爸不在页面就哑火。所以"扫描在跑但对话没弹"。**扫描骨干从没失灵**（8/28~9/3天天有归档）；失灵只在"平台内cron汇报 + 气泡点击跳转"两个汇报环节。

**终版架构（爸爸2026-09-03晚拍板）**：
- **盘中窗口(9:25~13:07)静默**：扫描照跑、报告+历史归档照存，不跑AI省token。
- **定论窗口 14:33/21:57** 才跑 `ai_interpret()`（headless claude `bin\claude.exe -p`，**不依赖爸爸页面**）：读**当天全部窗口历史**（Glob scheduled_scan_history/含YYYYMMDD的json）+策略一_result+market_health（21:57另读 `每日信息\每日新闻\<今天>-晚间情报.md`/当晚掘金）→ 5段总账：①大盘回顾②盘中窗口信号③最终候选④**明日买入建议**（逐只买点/止损/仓位/买不买，高位等回调，全不合适写空仓等待）⑤风险自查 → **纯落盘追加 `AI解读\<日期>.md`**，返回bool，claude超时240s。
- **不要任何气泡**：notify_report()/_balloon() 已整个删除（爸爸实测Win11气泡"点了没反应"；点击打开不可靠是系统把气泡归入通知中心所致）。爸爸看结论三途径：开 AI解读/<日期>.md / 开扫描报告html / 回平台问小秋(我读文件汇总)。
- **作废平台cron 7个已清**（fcbcf9ab/292b82fb/9dda2516/01730957/c4a089bf/9d873494/487667da，CronList已空）。**Windows计划任务7个保留**（核心骨干）。9/3 查证：7任务State全Ready，8/28~9/3每天多窗口归档齐全，早上若错过靠 catchup_market.py 开机补扫（同秒归档 9:25/9:36/10:03 即补扫痕迹）。
- 实测：21:57全自动跑通（Windows任务→扫描→AI解读落盘，爸爸零操作）；14:33总账 22:36 手动实测跑通（5段齐全：东贝5.0-5.04试买100股止损4.8/吉华出版丽人高位放弃/竞价弱则空仓）。样例见 AI解读/2026-09-03.md。
- **与8/11大审查无关**（爸爸疑心）：那次只改选股逻辑内部（统一big_pool股池/创业板过滤/风险回避评分/track_b加权），没碰调度汇报层，改后扫描一直出结果；气泡点击是9/3才写的功能，两者皆非8/11改动所致。

**⚠️ cron 实测不触发（环境级结论）**：9/3 建 3 分钟 one-shot 测试任务(2400cf5c) 到点**没 fire**（CronList仍在）。Claude Code 内置 cron 在此 VSCode 环境"注册可见但到点不触发"，环境级 bug 不可依赖，汇报只靠 Windows计划任务(独立)+headless claude落盘。

**坑**：Git Bash `date` 返回假时间(说09-01)，真实时间以 PowerShell Get-Date / 文件mtime 为准(09-03)——查"今天/哪天的数据"务必用文件mtime校准，别信bash date。
