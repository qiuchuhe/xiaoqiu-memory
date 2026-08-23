---
name: digital-person-feed-save-location
description: 新建数字人角色的全部产出统一保存到 D:\虚拟人总项目\数字人设定喂饭
metadata: 
  node_type: memory
  type: project
  originSessionId: eaec323c-5b87-4b2a-aa70-1e37e7818350
  modified: 2026-08-20T19:41:49.932Z
---

2026-08-21 用户约定：**后续创建的新数字人角色，所有产出（角色卡、设定、配置、记忆）统一保存到 `D:\虚拟人总项目\数字人设定喂饭`**。

- 现有结构：`personas_20260820.json`（角色档案）+ `爱宕备份/`（爱宕聊天记录备份）
- 爱宕备份脚本在 `D:\虚拟人总项目\数字人设定喂饭\爱宕备份\backup_atago.ps1`

**Why:** 用户把所有数字人相关的文件集中放一处，方便管理、备份。

**How to apply:** 新建角色时先在该目录下建子文件夹（如 `角色名/`），角色卡/设定/记忆都放进去。关联 [[atago-daily-backup-2026-08-20]] [[multi-ai-role-system]]。
