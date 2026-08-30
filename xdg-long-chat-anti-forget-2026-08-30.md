---
name: xdg-long-chat-anti-forget-2026-08-30
description: 心动囚笼长对话忘设定/缩单线/数值不变的根因与修复——角色卡铁则4/6/7/8禁主动插入是元凶，需改PNG+世界书+浏览器刷新
metadata: 
  node_type: memory
  type: project
  originSessionId: eaec323c-5b87-4b2a-aa70-1e37e7818350
  modified: 2026-08-30T15:59:13.506Z
---

2026-08-30 修复心动囚笼（都市病娇模拟器，主控姬野，A国修罗场）长对话遗忘问题。爸爸症状：聊长了忘设定/忘规则、退化成1对1、其他女主不主动插入、沈千雪永不出场、数值不增减、忘已发生事（吃过饭又问要不要吃饭）。

**根因三层**：
1. **角色卡 description 铁则4/6/7/8 明令禁止主动插入**——"严禁自主推进剧情：AI不能自己让角色出场""女主只被动反应""非在场角色待机不闯入""AI无权私自改数值"。description 每回合注入、优先级最高、永不截断，直接把世界书里的群像铁律压死。这是沈千雪永远不出场、数值永远不动的元凶。
2. **世界书 uid11(AI规则) 同步带同样的旧铁则**，uid3 核心原则写"数值唯一触发源是男主"，uid8②"未出场女主保持待机"，uid12-18 六位女主档案尾巴全是"待机/不私自跨界/不抢戏"——全压主动插入。
3. **SillyTavern 前端缓存**：世界书+角色卡都是前端加载，改完文件不刷新页面不生效（爸爸"当前玩并没感觉哪里变化"的主因）。

**修复（文件层，未动配置）**：
- `characters/心动囚笼.png`：chara+ccv3 两个内嵌 chunk 同步改（`data.description` 6728→7299字）。开篇插【每回合三铁律】（①数值必动②必给选项③群像勿单线每3~5回合让非焦点女主介入一次+沈千雪按节点登场）；铁则4改"玩家主控大剧情＋女主主动小互动"、6改"女主有主动行为自由"、7改"非当前场景角色可自然插入（来电/到场/插话，潮汐市角色高张力可赴A国）"、8改"数值必须动"；核心原则改双触发源（玩家言行+女主主动反应）。
- `worlds/心动囚笼_世界书_可导入酒馆.json`：uid3/8/11 同步改，uid12/13/15/16/17/18 六位女主尾巴改为可主动插入（沈千雪=暗中调查推进、触发节点自然登场）。
- 验证：PNG 签名+chunk 结构完好、chara/ccv3 内容一致、世界书 JSON 合法无残留矛盾、7位女主 scan_depth:100 保留、3条常驻constant(群像铁律190/状态锚点185/剧情锚定175)保留。备份 `.bak_fix_group_20260830_235648`。

**How to apply**: 改角色卡必改 PNG 内嵌 chunk（用 struct/zlib 重写 tEXt 的 chara+ccv3，保 IDAT 不动）；改完世界书/卡片必须让爸爸**刷新浏览器页面**才生效（当前聊天刷新不丢进度，存 chats/*.jsonl）。character-card-parser 读卡优先取 ccv3，chara 也要同步否则降级不一致。SillyTavern 世界书挂载配置在 `settings.json` 的 `world_info_settings`（不是 extension_settings.world_lore），active_character=心动囚笼.png、globalSelect=心动囚笼_世界书_可导入酒馆。关联 [[redeemer-project-2026-07-17]] [[new-four-characters-project-2026-08-22]]。
