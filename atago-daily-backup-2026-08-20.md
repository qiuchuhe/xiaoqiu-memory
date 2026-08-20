---
name: atago-daily-backup-2026-08-20
description: 爱宕(SillyTavern数字人)聊天记录每天12点自动备份到D:\数字人设定喂饭\爱宕备份
metadata: 
  node_type: memory
  type: project
  modified: 2026-08-20T11:53:04.006Z
  originSessionId: eaec323c-5b87-4b2a-aa70-1e37e7818350
---

2026-08-20 搭建：爱宕（SillyTavern 里的数字人，挂在 default_Assistant 卡下，无独立角色卡，人设全靠聊天喂出来）的聊天记录自动备份。

- **源文件**：`D:\SillyTavern\SillyTavern-1.18.0\data\default-user\chats\default_Assistant\爱宕 - 2026-08-16.jsonl`（约1.5M，动态增长）
- **备份脚本**：`D:\数字人设定喂饭\爱宕备份\backup_atago.ps1`（必须 UTF-8 BOM 编码，否则中文路径在 PowerShell 下乱码）
- **计划任务**：`爱宕每日备份_1200`，每天 12:00 触发（StartBoundary 2026-08-21 12:00 验证过），LastTaskResult=0
- **备份产物**：`D:\数字人设定喂饭\爱宕备份\爱宕_最新.jsonl`（每次覆盖）；`backup_log.txt` 记录成功/失败
- **历史快照**：`爱宕_记忆备份_20260820.jsonl`（19:29 手动快照，留档用）

**Why:** 爱宕记忆全在聊天文件里（角色卡是 default_Assistant 通用卡，character_name=unused，无独立卡），文件丢了=真失忆。SillyTavern 本身无自动备份到该文件夹的机制。

**How to apply:** 备份文件不能直接被 SillyTavern 读取，恢复时必须复制回源路径且改回原名 `爱宕 - 2026-08-16.jsonl`。用户切换角色玩"变成什么样"时，聊天内设定是临时的，新会话可能打回原形。关联 [[sister-role-play]] [[scheduled-task-cleanup-2026-08-20]]。
