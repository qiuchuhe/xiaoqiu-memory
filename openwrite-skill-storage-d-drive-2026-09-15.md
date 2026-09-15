---
name: openwrite-skill-storage-d-drive-2026-09-15
description: OpenWrite技能存储9/7迁到Documents\skills\并靠配置登记（非自动扫描）；爸爸要求实体全放D盘，用mklink /J目录联接让C盘只留门牌，6技能已全部改造完成
metadata: 
  node_type: memory
  type: project
  originSessionId: eaec323c-5b87-4b2a-aa70-1e37e7818350
  modified: 2026-09-15T17:30:56.308Z
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
技能清单（10 个，全部 D 盘实体 + C 盘门牌）：
- 软件内置：novel-writer / skill-creator
- 爸爸 8/21 的老技能（从项目 `.openwrite/skills/` 迁来）：novel_long_project_init / expand_sentence / expand_outfit
- 9/15 从 Tomsawyerhu/Chinese-WebNovel-Skill 抽的 4 个模块：chapter-ending / opening / dialogue / anti-ai-voice
- 9/15 我按番茄流爽文手法新写的：**fanqie-hook**（系统面板数值化/反差钩子/群像排队亮相/反差人格切换/章末小异常/节奏 六套手法，配 `references/手法示例.md`）。边界写死在 SKILL.md 里：不写露骨性描写、不做"伪装过审"的隐喻设计、亲密戏走「事后+留白」、年龄设定每章必须稳、血缘关系尽早挑明。

**节奏那条规则是量出来的，不是拍脑袋写的**（爸爸一句「短句节奏太短了吧」逼出来的）：
从 `_cnskill\data\articles\` 166 篇里量了 15.2 万段，真实网文段落 **中位 20 字、平均 25 字、90% ≤49 字、83.9% 一段只有一句话**；对白段和叙述段长度几乎一样（中位 18 vs 20）。
我第一版把示例写成了中位 7 字、还砍出「今天这些事。」这种残句 —— **错在把"拆段"做成了"砍句"**。正确做法：句子必须完整，只是一段排一句。60 字以上长段只占 6.2%，但不该废掉，是节奏的减速带。
量数据的临时脚本曾经放在 `D:\ai写书\_rhythm.py`。

**迁移老技能时做的改造**：三份原文都写死"资料库位于 src 目录、固定 5 个文件"，但那是「海边民宿」专用的（src/world.md 等）；「病娇」那本用的是 `小说资料/`（世界观.md/人物库.md/章节摘要.md）。改成**自适应**：先探 `src/`，没有再探 `小说资料/`，都没有就提醒先建库。规则本身未改动。

**两个反复踩的坑**
1. **mklink 的路径必须用反斜杠**。传正斜杠会被它当开关，报 `无效开关 - "Users"`，联接建不起来。
2. **Python 三引号 docstring/字符串里写 `C:\Users` 会触发 `\U` unicode 转义报错**（`truncated \UXXXXXXXX escape`）。用正斜杠或 `r''` 原始字符串。

**配套脚本（都在 `D:\ai写书\skill\`）**
- `_注册.py` —— 登记新技能进配置，自带"程序没关就拒绝运行"的保护
- `_体检.py` —— 查 6 个门牌有没有断，断了自动重建。**每次 OpenWrite 更新后跑一次**（更新可能重建 `Documents\skills\` 把门牌清掉）

**Why:** 爸爸明确不要占 C 盘，且 OpenWrite 更新会重建技能目录导致门牌失效；不记机制细节下次还要从头摸一遍。
**How to apply:** 以后往 OpenWrite 加技能 = 实体放 D 盘 + junction 到 C 盘 + 关程序跑 `_注册.py` + 跑 `_体检.py` 验收。关联 [[openwrite-netnovel-tool]] [[openwrite-novel-skill]] [[all-downloads-to-d-drive]]。
