---
name: gpt-sovits-tts-setup-2026-08-23
description: GPT-SoVITS 语音克隆已装好并接入 SillyTavern（适配器9881→原生API9880），参考音频放voice目录按角色名，待爸爸提供真实声线
metadata: 
  node_type: memory
  type: project
  originSessionId: eaec323c-5b87-4b2a-aa70-1e37e7818350
  modified: 2026-08-23T07:29:56.713Z
---

爸爸 2026-08-23 为「真实感声音」装了 **GPT-SoVITS** 并接入 SillyTavern，全链路已跑通（合成 HTTP 200）。

**链路**：SillyTavern(`GPT-SoVITS-V2 (Unofficial)` provider) → 适配器 `http://127.0.0.1:9881` → GPT-SoVITS API `api_v2.py 9880`。

**目录**（注意 `DAIGPT‑SoVITS` 的连字符是 U+2011，GBK 不支持，脚本必须 UTF-8）：
- 本体：`D:\虚拟人总项目\DAIGPT‑SoVITS\GPT-SoVITS`（venv=Python310，jieba_fast/torchcodec 已打兼容补丁）
- 适配器：`D:\虚拟人总项目\DAIGPT‑SoVITS\GPT-SoVITS_sillytavern_adapter`
- **一键启动**：`D:\虚拟人总项目\DAIGPT‑SoVITS\启动GPT-SoVITS.bat`（内部调 start-gpt-sovits.ps1，TCP 端口探测就绪判断）
- 参考音频：适配器 `voice\` 目录，`角色名.wav` + 同名 `角色名.txt`(参考文本)
- **视频提取工具**：`DAIGPT‑SoVITS\video2ref.py`（ffmpeg 提取32kHz单声道→voice，同名.srt字幕自动配文本，--list列时间轴）

**SillyTavern 配置**：settings.json 里 `tts.currentProvider = "GPT-SoVITS-V2 (Unofficial)"`，voiceMap 按角色名→voice 名（=wav 文件名）。当前全部角色暂映射到测试声 `测试`。Edge TTS 作为备用保留（voiceMap 已修复中文乱码）。

**8/23 卡死事故诊断**：SillyTavern 连续/长句触发合成时，某次在「合成音频」解码阶段卡死，堵住共享模型，此后所有请求秒回 200 空字节（GPU 利用率 20% 不动）。**解法：重启 API 即恢复**（重启后爸爸的两句长句 20 秒正常出 1.38MB）。已建 **`重启GPT-SoVITS.bat`**（杀旧进程+重新拉起）救急。根因疑为 6GB 显存紧张+串行推理被并发请求打乱。

**SillyTavern 配置**：settings.json 里 `tts.currentProvider = "GPT-SoVITS-V2 (Unofficial)"`，voiceMap 按角色名→voice 名（=wav 文件名）。当前全部角色暂映射到测试声 `测试`。Edge TTS 作为备用保留（voiceMap 已修复中文乱码）。

**待办**：等爸爸提供每个角色的**真实 3-10 秒人声样本+文本**放进 voice\ 替换测试声，真实感完全取决于参考音频质量（AI 声做参考仍是 AI 味）。

**8/23 林如雪 GPT-SoVITS 方案已放弃**：试了两条路都不满意——① 8月23日.mp4 切段做参考→合成沙哑(该视频带游戏背景音,高频占比0.275被学进音色)；② 换成爸爸确认干净的 lrx2 源(高频0.078)依然不满意。**爸爸最终决定：林如雪语音改用豆包播放，不用 GPT-SoVITS 了**。已删除：桌面测试音频、voice\林如雪.wav/txt、SillyTavern 林如雪映射(还原回测试声)。voice 目录只剩 测试.wav/txt。GPT-SoVITS 项目本体保持可用(其余角色未来可复用)。**教训**：8月23日.mp4 音频带游戏背景音,与桌面两个录屏(14.14.45/14.15.35)及 lrx1/lrx2(爸爸另给的干净源,内容同为"这是真人…兔子耳朵")是三个不同来源,做参考音频优先用爸爸确认干净的 lrx 系列。whisper 相关结论:高频占比>0.2 的参考源易致合成沙哑,理想<0.1。

**坑记录**：PowerShell 5.1 读 ps1 需 UTF-8 BOM；`$host` 是保留变量不能做函数参数；api_v2.py 无 `/` 路由就绪判断要用 TCP 探测。

关联 [[digital-person-feed-save-location]] [[sister-role-play]]。
