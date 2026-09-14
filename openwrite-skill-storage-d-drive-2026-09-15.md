---
name: openwrite-skill-storage-d-drive-2026-09-15
description: OpenWrite技能存储9/7迁到Documents\skills\并靠配置登记（非自动扫描）；爸爸要求实体全放D盘，用mklink /J目录联接让C盘只留门牌，6技能已全部改造完成
metadata: 
  node_type: memory
  type: project
  originSessionId: eaec323c-5b87-4b2a-aa70-1e37e7818350
  modified: 2026-09-14T17:17:33.318Z
---

2026-09-15 摸清并改造了 OpenWrite 的技能存储机制。

**9/7 更新后技能存储位置变了**（这是爸爸那 3 个老技能失效的原因）
- 旧：`<项目>\.openwrite\skills\*.md`（单文件）
- 新：`C:\Users\ASUS\Documents\skills\<name>\`（**文件夹**：`SKILL.md` + 可选 `references/` `scripts/` `assets/`）
- 旧格式的文件**不会被自动迁移**，也不在配置里 → 直接失效

**注册机制（关键）**：OpenWrite **不会自动扫描文件夹**，必须在
`C:\Users\ASUS\AppData\Roaming\com.openwrite\OpenWrite\shared_preferences.json` 里登记：
- `flutter.skill_list` —— **是一个 JSON 字符串**，不是数组，要 `json.loads` 两次
- 每条字段：`id` / `name` / `description` / `directoryPath` / `isBuiltIn` / `marketplaceSkillId` / `marketplaceVersion`
- `flutter.active_skill_ids` —— 同样是 JSON 字符串数组，控制启用
- `description` 决定技能何时被触发，要写满触发词（中文场景词）
- **必须在程序关闭时改**，否则退出时被回写覆盖

**D 盘方案（爸爸的硬要求：不许占 C 盘）**
用目录联接 `mklink /J`，C 盘只放零字节门牌，实体在 `D:\ai写书\skill\`。
`mklink /J` 不需要管理员权限（符号链接才需要）。
6 个技能已全部改造：novel-writer / skill-creator（内置）+ chapter-ending / opening / dialogue / anti-ai-voice（9/15 新装）。

**两个反复踩的坑**
1. **mklink 的路径必须用反斜杠**。传正斜杠会被它当开关，报 `无效开关 - "Users"`，联接建不起来。
2. **Python 三引号 docstring/字符串里写 `C:\Users` 会触发 `\U` unicode 转义报错**（`truncated \UXXXXXXXX escape`）。用正斜杠或 `r''` 原始字符串。

**配套脚本（都在 `D:\ai写书\skill\`）**
- `_注册.py` —— 登记新技能进配置，自带"程序没关就拒绝运行"的保护
- `_体检.py` —— 查 6 个门牌有没有断，断了自动重建。**每次 OpenWrite 更新后跑一次**（更新可能重建 `Documents\skills\` 把门牌清掉）

**Why:** 爸爸明确不要占 C 盘，且 OpenWrite 更新会重建技能目录导致门牌失效；不记机制细节下次还要从头摸一遍。
**How to apply:** 以后往 OpenWrite 加技能 = 实体放 D 盘 + junction 到 C 盘 + 关程序跑 `_注册.py` + 跑 `_体检.py` 验收。关联 [[openwrite-netnovel-tool]] [[openwrite-novel-skill]] [[all-downloads-to-d-drive]]。
