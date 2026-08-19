---
name: scheduled-task-cleanup-2026-08-20
description: 计划任务清理 16→8，乱码任务连路径也坏从没跑起来，最终架构8个干净任务
metadata: 
  node_type: memory
  type: project
  originSessionId: eaec323c-5b87-4b2a-aa70-1e37e7818350
  modified: 2026-08-19T16:27:25.103Z
---

2026-08-20 清理 Windows 计划任务，从 16 个 python 任务精简到 8 个。

**最终干净架构（8 个任务，唯一调度器 = scheduled_scan.py）：**
- 08:00 XiaoQiu_MorningReport → morning_report.py（早间报告，唯一保留的 XiaoQiu_* 任务）
- 09:25 秋策_竞价扫描_0925 / 09:36 低开回升_0936 / 10:03 早盘主攻_1003 / 10:30 洗盘确认_1030 / 13:07 午盘开盘_1307 / 14:33 尾盘决战_1433 / 21:57 晚间复盘_2157 → 全部 scheduled_scan.py

**Why 重要发现：** 有一整套「乱码任务」（名字是 `绉嬬瓥_*`，即 `秋策_*` 的 GBK/UTF-8 编码错误）不只是名字坏——它们的**命令行路径也乱码**（`D:\AI灏忕\绛栫暐...` 而非 `D:\AI小秋\策略量化...`），指向不存在的路径，所以这些任务**从没真正跑起来过**（静默失败）。

**How to apply：**
1. 以后看到乱码任务名，必须连 Args 路径一起查，别只改名——路径也坏了要重建。
2. 旧的 XiaoQiu_* 定时任务（catchup_market.py --time X）已删：catchup_market 是「补扫」逻辑（把当天已过窗口全重跑），当固定定时任务会重复扫描全天，应只在开机补扫时用。
3. 删除/重建的备份在 `D:\task_cleanup_backup_2026-08-20\`（10 个 XML）。
4. 重建方法：用干净任务 `秋策_尾盘决战_1433` 的 XML 做模板，`-replace '14:33','目标时间'` + 去 URI + `Register-ScheduledTask -TaskName 新名 -Xml -Force`，再删旧名。
5. 清理脚本保留在 `D:\AI小秋\task_cleanup.py`（可复用）。关联 [[trading-cron-jobs]]、[[auto-catchup-on-startup]]。
