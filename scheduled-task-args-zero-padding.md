---
name: scheduled-task-args-zero-padding
description: 计划任务参数前导零坑——时间参数必须与WINDOW_CONFIG key完全一致，不能补前导零
metadata: 
  node_type: memory
  type: project
  originSessionId: eaec323c-5b87-4b2a-aa70-1e37e7818350
  modified: 2026-08-20T01:45:22.223Z
---

2026-08-20 早盘发现：9:25 竞价、9:36 低开两个计划任务触发但 Result=1（脚本挂了）。

**根因**：任务 `Arguments` 里写的是 `scheduled_scan.py 09:25`（带前导零），但 `scheduled_scan.py` 的 `WINDOW_CONFIG` 的 key 是 `"9:25"`、`"9:36"`（**不带前导零**）→ 传 `09:25` 走"未知窗口"分支 → `sys.exit(1)` → LastTaskResult=1。14:33/10:03/13:07 本来就是两位数所以正常。

**修复**：用 14:33（运行成功、配置验证过）的 XML 当模板，`-replace '14:33'→目标时间` 重建任务，确保时间参数不带前导零。验证 LastTaskResult=0。

**Why:** 任务重建时用模板 replace 时间，若不小心补了前导零就会静默失败，且失败发生在脚本入口（exit 1），不写结果文件、不留痕迹，像"没触发"。

**How to apply:** 任何基于模板重建/新建 9 点前的计划任务，时间参数必须写成 `9:25` 而非 `09:25`；新建后手动 `schtasks /run` + 查 `LastTaskResult` 验证是否=0。关联 [[scheduled-task-cleanup-2026-08-20]] [[trading-cron-jobs]]。
