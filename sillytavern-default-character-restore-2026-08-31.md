---
name: sillytavern-default-character-restore-2026-08-31
description: 爸爸误以为心动囚笼被换成幸福小区——实为active_character被前端切走。SillyTavern默认角色/世界书挂载机制与恢复方法
metadata: 
  node_type: memory
  type: project
  originSessionId: eaec323c-5b87-4b2a-aa70-1e37e7818350
  modified: 2026-08-31T15:10:45.414Z
---

2026-08-31 爸爸打开SillyTavern发现是幸福小区，慌称"你把心动囚笼变成幸福小区了"。真相：心动囚笼.png和聊天存档（952KB主档）原封不动，只是 settings.json 顶层 `active_character` 被前端切成了"幸福小区.png"（爸爸昨晚最后停在那）。

**机制（SillyTavern-1.18.0）**：
- `active_character` 存在 settings.json **顶层**（不是 world_info_settings），前端加载时 `active_character = settings.active_character` 恢复选中角色（script.js:7951）。
- **浏览器前端会话在任意操作时会把当前选中角色写回 settings.json**——所以我改文件没用，只要爸爸页面还开着，它就把我改的覆盖回去（曾被覆盖成幸福小区、又被覆盖成 None）。**改 settings.json 必须先让爸爸关掉所有SillyTavern标签页**，或让爸爸在界面点选角色。
- 世界书挂载三途径：①`world_info_settings.world_info.globalSelect`（存**文件名无扩展名**，后端`path.parse().name`，注入路径明确）②角色卡 `extensions.world`（角色级绑定，幸福小区用它）③聊天 `chat_metadata.world_info`（本存档为None）。心动囚笼靠①，被清空后恢复=写回 `["心动囚笼_世界书_可导入酒馆"]`。
- 恢复完整命令序列：备份settings.json → 改 `active_character='心动囚笼.png'` + `world_info_settings.world_info.globalSelect=["心动囚笼_世界书_可导入酒馆"]` → 爸爸重开即进。

**两个剧本区分**：心动囚笼=都市病娇7女全成年（19-22）；幸福小区=全员病娇15女。8/31 爸爸把林小棠改成19岁成人后两剧本全员成年，幸福小区也上了15条情趣线（见 [[xingfuxiaoqu-qingqu-2026-08-31]]）。关联 [[xdg-long-chat-anti-forget-2026-08-30]]。

**2026-08-31 补充·双重坑实测（噩梦模式同步大坑，详见 [[xingfuxiaoqu-qingqu-2026-08-31]] 六改）**：
1. **改卡前必查 active_character 指向哪个文件**——8/31 爸爸实际在用 `幸福小区2.png`（副本/改名版），不是主卡 `幸福小区.png`。只改主卡=爸爸体验不到，白改。查法：`settings.json` 顶层 `active_character`。
2. **前端会把加载的世界书写回磁盘覆盖**——爸爸在酒馆操作（22:54）后，`globalSelect` 指向的世界书被前端内存旧版覆盖，我加的条目全丢。**改世界书/角色卡后必须让爸爸 F5 刷新页面重新加载**；且世界书文件若有 `originalData` 顶层键（前端保存格式），也要同步内容否则前端重置/重导入时用旧 originalData 覆盖。
3. 挂载真凶在 `world_info_settings.world_info.globalSelect`（存文件名无扩展名），不是我记忆里的旧文件 `幸福小区_全员病娇模拟器`——**同步前先读 globalSelect 确认挂载谁**。
