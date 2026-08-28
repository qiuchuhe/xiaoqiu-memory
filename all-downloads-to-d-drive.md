---
name: all-downloads-to-d-drive
description: 爸爸明确要求所有下载/安装/缓存都放 D 盘，不占 C 盘空间
metadata: 
  node_type: memory
  type: feedback
  originSessionId: eaec323c-5b87-4b2a-aa70-1e37e7818350
  modified: 2026-08-28T13:54:20.760Z
---

2026-08-28 爸爸明确说："是不是说不要给c盘，东西都下d盘"。所有大下载、软件、缓存一律放 D 盘，C 盘只留系统必要文件。

**已落地的配置：**
- pip 全局缓存已永久设到 `D:\pip_cache`（写 C:\Users\ASUS\AppData\Roaming\pip\pip.ini，用**无中文路径**避免 GBK/UTF-8 乱码坑）
- GPT-SoVITS 整套都在 `D:\虚拟人总项目\GPT-SoVITS\`：源码/venv/权重/adapter/torch_wheels/voicepacks
- 2026-08-28 曾清掉 C 盘遗留 pip cache 5.6G + Temp 3.8G

**为什么：** C 盘空间宝贵，装系统/软件，放不下大模型文件；且中文路径写 pip.ini 会乱码（pip 用 locale 编码读配置）。

**How to apply:** 以后任何下载/安装，默认 target 选 D 盘路径；新环境装包前先 `pip config set global.cache-dir D:\pip_cache`；涉及中文路径的配置文件（pip.ini 等）改用无中文路径或用 GBK 写。关联 [[gpt-sovits-tts-setup-2026-08-23]]。
