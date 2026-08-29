---
name: gpt-sovits-tts-setup-2026-08-23
description: GPT-SoVITS 语音克隆项目（8/23 首装放弃→8/28 重建→8/29 星瞳声线接入成功，许知糯=星瞳甜妹音），provider 必须用 GPT-SoVITS-V2 (Unofficial)，参考音频放 voice 目录按角色名
metadata: 
  node_type: memory
  type: project
  originSessionId: eaec323c-5b87-4b2a-aa70-1e37e7818350
  modified: 2026-08-29T04:52:16.366Z
---

爸爸为「真实感声音」用 GPT-SoVITS 接入 SillyTavern。**8/23 首装后爸爸放弃删除**（参考音频带背景音→合成沙哑，高频占比>0.2 易沙哑，理想<0.1），**8/28 爸爸主动要求重建**（换社区声线包方案=真人训练音色），**8/29 星瞳声线接入成功**。

**链路**：SillyTavern(`GPT-SoVITS-V2 (Unofficial)` provider) → 适配器 `http://127.0.0.1:9881` → GPT-SoVITS API `api_v2.py 9880`。

**⚠️ provider 兼容性坑（最重要）**：SillyTavern 里有两个 GPT-SoVITS provider——
- `GPT-SoVITS-V2 (Unofficial)`：发 `ref_audio_path` → **与 guoql666 adapter 完全兼容**（adapter 按 ref_audio_path 的 basename 去 `voice\{角色名}.wav` 找参考音频），**必须用它**
- `GPT-SoVITS-Adapter`：发 `target_voice`+`card_name` → 本 adapter 版本 pydantic 不认，**422 失败**，别用

**目录**（8/28 重建，新目录普通连字符 `-`）：
- 本体：`D:\虚拟人总项目\GPT-SoVITS\GPT-SoVITS`（venv=Python310）
- 适配器：`D:\虚拟人总项目\GPT-SoVITS\adapter`（voice\ 放参考音频 + models.json 角色→权重）
- 声线包：`D:\虚拟人总项目\GPT-SoVITS\voicepacks\WutheringWaves_CN`（鸣潮，GPT+SV ckpt/pth）
- **一键启动**：`D:\虚拟人总项目\GPT-SoVITS\start_tts.py`（pythonw 无黑窗，先杀旧进程再拉起，端口探测就绪，日志 start_tts.log；api_v2 9880 + adapter 9881）
- 参考音频：adapter `voice\` 目录，`角色名.wav` + 同名 `角色名.txt`(参考文本) + models.json 配 `prompt_lang`

**SillyTavern 配置**：settings.json 里 `tts.currentProvider = "GPT-SoVITS-V2 (Unofficial)"`，`GPT-SoVITS-V2 (Unofficial)` 块 `provider_endpoint=http://127.0.0.1:9881`，voiceMap 按角色名→voice 名（=wav 文件名）。当前许知糯→许知糯声（星瞳），其余角色→测试声 `测试`（鸣潮权重）。**改 settings.json 后 F5 刷新页面即生效，不用重启**（server 端 `/settings/get` 每次读盘，非启动缓存）。改前先备份。Edge TTS 备用保留（provider 可随时切回）。**⚠️ 8/29 坑**：① GPT-SoVITS 块内 voiceMap 曾被清空成 `{}`（顶层 voiceMap 是 Edge 遗留 zh-CN-XiaoxiaoNeural 会串音）→ 用 `settings.json.bak_gptsovits` 恢复 61 键 + 许知糯 4 键覆盖 → F5 生效。② **无声根因（已修，双保险）**：前端 `public/scripts/extensions/tts/gpt-sovits-v2.js:187` 硬编码 `streaming_mode: 'true'` → api_v2 返回**流式 WAV**（RIFF size=36 + data size=0 + 大量 `\x01\x00\x00\x00`/`\x00\x00\x00\x00` 标记块），浏览器 `<audio>` 无法解码 → 请求 200 但静默无声。修复一：JS 改成 `streaming_mode: this.settings.streaming ? 'true' : 'false'`（settings.json 里 streaming=false）→ 返回标准 WAV。修复二（**兜底最重要**）：adapter.py 的 tts_stream_endpoint 转发前强制 `request_data["streaming_mode"]=False` + `media_type="wav"`，无论前端发什么都转标准 WAV。另加 emoji 清洗（adapter 顶部 EMOJI_RE，只删 emoji 区块+变体符+ZWJ 不碰汉字）避免 api_v2 split_lang 误判 en 走 nltk。备份 gpt-sovits-v2.js.bak_streaming_0829。改 JS 后需 **Ctrl+F5 硬刷新** 强制重载前端。③ **yaml version 不用管**：TTS.py `init_vits_weights` 从 sovits.pth 自动检测版本覆盖 yaml（`update_version`），当前 custom 块 v1 就是星瞳检测结果，合成正常。

**依赖/绕坑经验**（全 D 盘，D 盘余 89G 充足）：
- transformers 必须 ≤4.51.3（4.57.6 强制 torch>=2.6 CVE-2025-32434）；tokenizers 0.22.2；huggingface-hub 0.36.2
- torch 2.4.1+cu124 装完后所有 pip 必须 `--no-deps`（否则 resolver 误拉 CPU 版 torch 2.13.0）
- jieba_fast 需 MSVC 编译 → 建别名模块 jieba_fast.py 指向纯 Python jieba；fasttext → 装 `fasttext-wheel`；pyopenjtalk 跳过（日文才需要）
- requirements 文件必须纯 ASCII（中文注释→GBK UnicodeDecodeError）；stdout 禁用 ✓/✗（GBK 崩溃），用 OK/FAIL
- split_lang detector.py 硬编码 `model="full"` → 改 `model="lite"`（省 130MB 下载）；langsegmenter.py 加 cache_dir 指向本地 pretrained_models/fast_langdetect

**星瞳声线包**（8/29 接入成功）：`D:\虚拟人总项目\GPT-SoVITS\voicepacks\XingTong`（gpt.ckpt 155MB + sovits.pth 85MB + ref.wav），来源 hf-mirror.com `shibing624/parrots-gpt-sovits-speaker`。许知糯 models.json 指向 XingTong，参考音频=voice/许知糯.wav（作者自带 ref.wav 干净真人音）+ 许知糯.txt（"等你，我想想，嗯。"）。**⚠️ 下载坑**：curl `-C -` 续传会致 zip 损坏（字节对但 EOCD 丢）→ 必须完整下载+zip校验（dl_sovits2.sh 完整重下才成功）；坏 sovits.pth 会让 api_v2 启动崩→adapter 反复 400→SillyTavern 报 "TypeError: Failed to fetch"，恢复=换完好权重+杀进程重启（start_tts.py）。

**8/23 教训**：参考音频质量决定听感；高频占比>0.2 的源易致合成沙哑，优先爸爸确认干净的源（理想<0.1）。AI 合成音做参考仍是 AI 味。

关联 [[edge-tts-plugin-voice-2026-08-28]] [[sister-role-play]] [[digital-person-feed-save-location]]。
