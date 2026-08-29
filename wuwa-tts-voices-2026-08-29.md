---
name: wuwa-tts-voices-2026-08-29
description: 鸣潮58个中文声线全量下载接入GPT-SoVITS（57成功，卡卡罗缺权重），SillyTavern心动囚笼已绑今汐可随时换声
metadata: 
  node_type: memory
  type: project
  originSessionId: eaec323c-5b87-4b2a-aa70-1e37e7818350
  modified: 2026-08-29T12:47:30.094Z
---

爸爸 8/28-29 要鸣潮角色声线，已全量下载并接入 GPT-SoVITS。

**成果**：
- 来源：ModelScope 仓库 `aihobbyist/GPT-SoVITS_Model_Collection` 的 `鸣潮/中文` 58 角色，每包约 203MB，缓存于 `D:\虚拟人总项目\GPT-SoVITS\voicepacks\downloads`（11.6GB，可清理）
- 57 角色接入成功；**卡卡罗**包只有 ckpt 无 pth（源包残缺，81MB），待找齐 SoVITS 权重补
- 批处理脚本 `D:\虚拟人总项目\GPT-SoVITS\batch_wuwa.py`（幂等，`--all`/`--chars "名 名"`/`--setup-only`）
- V4 LoRA 模型需 v4 底模 `s2Gv4.pth`(769M) + `vocoder.pth`(55M)，已放 `pretrained_models/gsv-v4-pretrained/`（分块下载避断流）
- 引擎 TTS.py 自动检测版本（b"02"=v3完整 b"03"=v3lora b"04"=v4lora），v4 走 peft merge_and_unload

**SillyTavern 接入方式**：`GPT-SoVITS-V2 (Unofficial)` provider 的 voiceMap，key=角色名及其变体，value=声线名（=adapter `/speakers` 返回的 name，即角色名）。心动囚笼当整体绑定「今汐」（4 变体统一），爸爸可在 Voice Map 下拉自由换声线，或说名字让我换。

**8/29 已删男声**：爸爸明确只要女声。用基频分析(F0<150Hz)+游戏常识+配音性别交叉确认，删除 20 个男声角色（1号演员/亚哈/伤痕/偃师/克里斯托弗/卡卡罗/巴多里奥/布兰特/忌炎/文叔/斯塔巴克斯/渊武/男漂泊者/相里要/秋水/科波拉/芬莱克/莫特斐/阿莱克斯司铎/爱蒂奇）。删除脚本 `delete_male_voices.py`，models.json 备份为 `.bak_before_del_male_0829`。**剩余 38 个鸣潮女声**（speakers 共 41=38女声+测试+许知糯+许知糯_Edge晓晓备份）。注意：爱蒂奇=黑胡子海盗指代(男)，坎特蕾拉虽F0偏低但确为女声。F0 分析脚本 `f0_check.py`。

**坑**：zip 内文件名 GBK 编码须 `cp437→gbk` 修复；Git Bash curl 中文路径 GBK 乱码须用 Python urllib；管道吞退出码，大文件须分块下载。

关联 [[gpt-sovits-tts-setup-2026-08-23]]
