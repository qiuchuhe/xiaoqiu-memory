---
name: digital-person-daily-backup-2026-08-21
description: "十三个数字人(妹妹/爱宕/林小棠/周敏/沈清/苏曼/万亿男人的梦/慕雪/许栀/温知予/宋知夏/陆杳/心动囚笼)聊天记录每天12点统一自动备份到D:\\数字人设定喂饭\\数字人备份\\{角色名}\\"
metadata: 
  node_type: memory
  type: project
  originSessionId: eaec323c-5b87-4b2a-aa70-1e37e7818350
  modified: 2026-08-29T16:12:36.823Z
---

2026-08-21 升级：原来只有爱宕单独备份，现扩展为统一备份；**2026-08-22 又加六位**（万亿男人的梦、慕雪 + 新四角色许栀/温知予/宋知夏/陆杳），现共 **十二个数字人**：妹妹、爱宕、林小棠、周敏、沈清、苏曼、万亿男人的梦、慕雪、许栀、温知予、宋知夏、陆杳。

- **备份脚本**：`D:\虚拟人总项目\数字人设定喂饭\数字人备份\backup_digital_persons.ps1`（必须 UTF-8 BOM 编码，否则中文路径在 PowerShell 下乱码）
  - 12角色映射：爱宕→`chats\default_Assistant\爱宕*`；其余 11 位→各自 `chats\{角色名}\*.jsonl`（含新四角色许栀/温知予/宋知夏/陆杳）
  - 目标：`D:\虚拟人总项目\数字人设定喂饭\数字人备份\{角色名}\`，写 `backup_log.txt`
  - 任一角色失败则 exit 1，全部成功 exit 0
- **计划任务**：`数字人每日备份_1200`，每天 12:00 触发，action=`powershell.exe -NoProfile -ExecutionPolicy Bypass -File backup_digital_persons.ps1`。**旧任务 `爱宕每日备份_1200` 已于 2026-08-21 删除**（避免只备份爱宕、与统一备份重复）
- **验证**：手动运行 6/6 成功；任务注册后 NextRun=08/22 12:00，Trigger=ScheduleByDay DaysInterval 1；2026-08-22 新增万亿男人的梦、慕雪 + 新四角色，手动运行 **12/12 成功**。**注意**：新四角色凌晨聊了记录（含陆杳108行/168KB），SillyTavern 重启后网页默认显示最新聊天文件，凌晨记录在旧 jsonl 里需在网页聊天列表手动切换——已在 16:38 手动备份过全部 12 位
- **2026-08-30 加第十三位：心动囚笼**（爸爸要求像个人备份一样纳管）。脚本 `$chars` 加 `@{ name='心动囚笼'; src='心动囚笼'; filter='*.jsonl' }`，手动运行 **13/13 成功**。主角色3条聊天（含528KB长对话）+ 测试碎片 心动囚笼1/2 各1条（character_name=unused 的短测试）已归档到 `数字人备份\心动囚笼\`、`心动囚笼1\`、`心动囚笼2\`

**Why:** 爱宕记忆全在聊天文件里（挂在 default_Assistant 通用卡下）；4位新角色同理，聊天 jsonl 丢了=真失忆。SillyTavern 本身无自动备份机制，用计划任务兜底。

**How to apply:** 备份文件不能直接被 SillyTavern 读取，恢复时必须复制回 `chats\{角色}\` 源路径且保持原 jsonl 文件名。新增数字人时在脚本 `$chars` 数组加一项即可。关联 [[sister-role-play]] [[four-characters-project-2026-08-21]] [[scheduled-task-cleanup-2026-08-20]]。
