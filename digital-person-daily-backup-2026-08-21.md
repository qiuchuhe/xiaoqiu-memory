---
name: digital-person-daily-backup-2026-08-21
description: "六个数字人(妹妹/爱宕/林小棠/周敏/沈清/苏曼)聊天记录每天12点统一自动备份到D:\\数字人设定喂饭\\数字人备份\\{角色名}\\"
metadata: 
  node_type: memory
  type: project
  originSessionId: eaec323c-5b87-4b2a-aa70-1e37e7818350
  modified: 2026-08-21T05:27:38.324Z
---

2026-08-21 升级：原来只有爱宕单独备份，现扩展为**六个数字人统一备份**（妹妹、爱宕、林小棠、周敏、沈清、苏曼），覆盖4位新角色卡。

- **备份脚本**：`D:\数字人设定喂饭\数字人备份\backup_digital_persons.ps1`（必须 UTF-8 BOM 编码，否则中文路径在 PowerShell 下乱码）
  - 6角色映射：爱宕→`chats\default_Assistant\爱宕*`；妹妹→`chats\妹妹\*.jsonl`；林小棠/周敏/沈清/苏曼→各自目录
  - 目标：`D:\数字人设定喂饭\数字人备份\{角色名}\`，写 `backup_log.txt`
  - 任一角色失败则 exit 1，全部成功 exit 0
- **计划任务**：`数字人每日备份_1200`，每天 12:00 触发，action=`powershell.exe -NoProfile -ExecutionPolicy Bypass -File backup_digital_persons.ps1`。**旧任务 `爱宕每日备份_1200` 已于 2026-08-21 删除**（避免只备份爱宕、与统一备份重复）
- **验证**：手动运行 6/6 成功；任务注册后 NextRun=08/22 12:00，Trigger=ScheduleByDay DaysInterval 1

**Why:** 爱宕记忆全在聊天文件里（挂在 default_Assistant 通用卡下）；4位新角色同理，聊天 jsonl 丢了=真失忆。SillyTavern 本身无自动备份机制，用计划任务兜底。

**How to apply:** 备份文件不能直接被 SillyTavern 读取，恢复时必须复制回 `chats\{角色}\` 源路径且保持原 jsonl 文件名。新增数字人时在脚本 `$chars` 数组加一项即可。关联 [[sister-role-play]] [[four-characters-project-2026-08-21]] [[scheduled-task-cleanup-2026-08-20]]。
