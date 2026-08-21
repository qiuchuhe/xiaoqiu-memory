---
name: openwrite-expand-sentence-skill
description: OpenWrite 短句/对话扩写专用 Skill（expand_sentence.md）完整内容——爸爸2026-08-21提供，主动调用型扩写技能，与总控SKILL.md共存
metadata: 
  node_type: memory
  type: project
  originSessionId: eaec323c-5b87-4b2a-aa70-1e37e7818350
  modified: 2026-08-21T14:09:25.363Z
---

爸爸 2026-08-21 提供的 OpenWrite 第二个 Skill：短句/对话扩写专用。与总控 [[openwrite-novel-skill]] 配成一套，**共存互不冲突**。

**分工关系**
- 总控 SKILL.md（novel_long_project_init）：全局约束，全程自动生效——写作/改写/扩写/大纲全部任务先读 5 份资料库，管全书一致性
- expand_sentence.md（expand_sentence）：主动调用型——需要时在聊天框输入 `@expand_sentence` 再粘贴句子/细纲，管单点扩写
- 两个都会自动读取项目 src 下全部资料库，可同时生效

**存放方式（OpenWrite 侧）**：`小说项目/.openwrite/skills/expand_sentence.md`，软件内 `/reload` 重载全部技能。用法：`@expand_sentence` + 一句话回车。

---

# expand_sentence.md 全文

```
---
name: expand_sentence
description: 短句、台词、极简细纲扩写专用Skill。输入一句对话或者简短梗概，丰富神态、动作、环境、心理，保留原意不改剧情，输出高质量网文段落。依赖项目资料库约束人设与世界观。
tags:["扩写","润色","对话扩充"]
---

# 短句对话扩写专用技能
## 前置约束
执行扩写前自动读取项目src全部资料库：world.md、characters.md、foreshadowing.md、rule_satisfaction.md、style.md。
所有扩写内容必须符合人物性格、世界观、本书行文风格，严禁OOC。

## 输入类型
用户输入可以是：单句对话、简短台词、一两行细纲、极简剧情碎片。

## 扩写执行规则
1. **绝对保留核心**：原始对话、事件走向、人物立场、事实结果一点不能改动，只做内容增量，不修改原有剧情。
2. 多维填充，按需组合：
- 人物细微神态：眼神、眉头、嘴角、脸色变化
- 肢体动作：手势、站姿、细微肢体反应
- 内心心理活动：潜藏想法、情绪波动、顾虑
- 环境氛围：周遭场景、光影、环境烘托情绪
- 潜台词，人物语气，停顿感
3. 拒绝单纯把原对话直接丢出来，不能只有光秃秃的台词。
4. 杜绝AI套话、空洞形容词堆砌，拒绝大段无效水文。扩充内容要服务情绪、人物塑造。
5. 文风跟随style.md设定，贴合当代网文阅读节奏，不要老旧晦涩。

## 禁止清单
❌篡改原本对话含义、更改事件结局
❌凭空新增重大剧情、关键伏笔，如需新增重要情节，等待用户确认
❌所有人神态动作模板化千篇一律
❌大段无关风景描写脱离人物

## 输出格式
只输出扩写完完整小说正文片段，**不要分析、不要思考过程、不要注释**。

## 使用示例
用户输入：
>“你别过来。”

输出示例：
>他身子下意识往后退了半步，脊背微微绷紧，眼底藏着一丝忌惮，声音微微发颤：“你别过来。”昏暗的光落在他紧绷的侧脸，掌心已经悄悄攥紧。
```

**搭配提醒（爸爸给的实操）**
- 总控 SKILL.md 保留不动，两 Skill 共存；总控管全书全局，expand_sentence 专门负责主动调用扩写
- 扩写若产生人物状态变化 → 记得更新 characters.md 资料库

关联 [[openwrite-netnovel-tool]] [[openwrite-novel-skill]]。
