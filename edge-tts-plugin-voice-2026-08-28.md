---
name: edge-tts-plugin-voice-2026-08-28
description: SillyTavern Edge TTS 插件方案（provider=plugin 免Extras）跑通+中文女声实测只有晓晓/晓伊两个（无晓梦无萝莉音），CSRF与网页覆盖两大坑
metadata: 
  node_type: memory
  type: project
  originSessionId: eaec323c-5b87-4b2a-aa70-1e37e7818350
  modified: 2026-08-28T11:10:12.900Z
---

2026-08-28 许知糯 TTS 第一阶段跑通：用官方 EdgeTTS-Plugin（本机 plugins/ 已装），`tts.Edge.provider='plugin'` 走 `/api/plugins/edge-tts/generate`，零下载出声。

**中文女声实测**（以插件 `/api/plugins/edge-tts/list` 为准，Edge 声线库动态会变，别信记忆里的老声线名）：
- 标准普通话女声**只有 2 个**：晓晓 `zh-CN-XiaoxiaoNeural`（Warm 温暖治愈）、晓伊 `zh-CN-XiaoyiNeural`（Lively 活泼元气甜妹）
- **不存在"晓梦"**（曾误记有 zh-CN-XiaomengNeural，实测返回 0 字节=服务端不认）
- 8/23 记忆里的"晓双 XiaoshuangNeural"今天列表里也没了
- 方言女声：`zh-CN-liaoning-XiaobeiNeural`（东北话）、`zh-CN-shaanxi-XiaoniNeural`（陕西话）；zh-HK/zh-TW 是粤语/台湾腔
- **真软萌萝莉音 Edge 给不了**，只能上 GPT-SoVITS 社区声线包（第二阶段）

**接入要点**：
- 前端 voiceMap 读 `extension_settings.tts.Edge.voiceMap`（顶层 `tts.voiceMap` 也要同步），键格式一组4条：`角色名` / `角色名 ("Quotes")` / `角色名 (*Text inside asterisks*)` / `角色名 (Other text)`
- **坑1 CSRF**：POST 改状态请求必须带 token——先 `GET /csrf-token`（存 cookie），再带 `X-CSRF-Token` 头；GET（如 /list）豁免。浏览器前端自动处理，curl 需复刻
- **坑2 网页覆盖**：网页开着时直接改 settings.json 会被前端旧设置写回，必须关网页再改文件
- 改配置后需重启服务（`python start_silent.py`，kill 8000 端口进程再拉起，约2-25秒就绪）

许知糯（含许知糯1，不含许知糯1）当前绑晓晓，爸爸 8/28 试听中；不满意再回晓伊。关联 [[gpt-sovits-tts-setup-2026-08-23]]。
