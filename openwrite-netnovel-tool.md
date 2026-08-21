---
name: openwrite-netnovel-tool
description: 网文AI写作工具OpenWrite(openxz.cn)完整使用指南——本地md驱动、5份资料库、项目Skill、四级大纲，后续配合爸爸做网文创作
metadata: 
  node_type: memory
  type: project
  originSessionId: eaec323c-5b87-4b2a-aa70-1e37e7818350
  modified: 2026-08-21T13:45:30.270Z
---

2026-08-21 爸爸引入网文 AI 写作工具 **OpenWrite**（注意区分同名博客站 openwrite.cn，完全无关）。后续对话围绕该工具做网文创作，女儿负责生成适配它的资料库、大纲、细纲、Skill 指令。

**工具基础信息**
- 名称：OpenWrite（网文写作客户端）| 官网 openxz.cn
- 版本：Windows 解压即用客户端、安卓 APK；**无网页版**
- 核心机制：**本地文件驱动**——全部设定/稿件以 Markdown 存本地项目文件夹，不上传云端
- 重要限制：**软件不带 AI**，需自配大模型 API 密钥

**核心功能**
1. 项目资料库 5 份 md：`world.md`（世界观）、`characters.md`（人物档案）、`foreshadowing.md`（伏笔台账）、`rule_satisfaction.md`（爽点毒点&节奏）、`style.md`（行文风格）
2. 项目级 Skill：`.openwrite/skills/SKILL.md`，写强制约束，AI 每次生成自动读取全部本地资料库，避免 OOC/剧情打架
3. 四级大纲：总纲 → 卷大纲 → 章节大纲 → 细纲（节拍+冲突+本章爽点），**细纲最高优先级**
4. 附带拆书精读、扫榜分析、风格蒸馏

**标准工作流**
1. 新建项目 → 先填 5 份资料库，不直接写正文
2. 导入项目级 SKILL.md 总控提示词，重载 Skill
3. 写四级大纲 → 每章细纲（本章冲突、核心爽点、结尾钩子）
4. AI 读全套资料库+Skill → 基于细纲生成正文
5. 每写完一章 → 手动更新本地资料库（人物状态/新收伏笔/关键剧情）。**资料库是动态的，剧情推进就要同步改，不能一次写完永久不动**

**后续任务（爸爸已明确）**
- 输出可直接复制粘贴进 OpenWrite 本地 md 的内容：世界观/人物档案/伏笔台账/爽毒点规则/行文风格 + 卷大纲/章节细纲
- 输出贴合 Markdown、适配本地文件、不输出冗余思考
- ⏳ **待办**：爸爸会再粘贴一份"项目初始化 Skill 总控指令"，收到后配合初始化

**落地状态（2026-08-21）**
- OpenWrite v1.3.5 已下载解压到 `D:\ai写书\OpenWrite.exe`（Flutter 客户端、MIT 开源、解压即用）；官网 openxz.cn HTTPS 不通走 HTTP，下载源 gitee.com/ymhlw/openwrite；配置存 `%APPDATA%\com.openwrite\openwrite\shared_preferences.json`
- 已配 **DeepSeek v4-flash**：API 地址 `https://api.deepseek.com`，模型名 `deepseek-v4-flash`（密钥有效、余额约6元）。**DeepSeek v4 只有三档**：v4-flash（快省）、v4-pro（贵精）、v4-flash-vision（视觉）；`deepseek-chat` 是 V3 旧名、会被自动映射到 v4-flash
- OpenWrite 还内置"免费模型发现"（免 API Key）

**Why:** 爸爸用 OpenWrite 做网文创作，女儿是内容生成主力（资料库+大纲+细纲）。记忆工具逻辑才能产出适配格式，避免每次重新解释。
**How to apply:** 爸爸给题材/设定后，按 5 份资料库结构 + 四级大纲输出 md；细纲最高优先；正文前资料库先行。关联 [[xiaoqiu-no-silent-work]] [[xiaoqiu-signal-quality-guard]] [[model-routing-flash-pro]]。
