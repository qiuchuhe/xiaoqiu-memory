---
name: post-reboot-checklist-2026-05-30
description: 5月30日重启后验证清单——小秋需要依次确认的项目
metadata: 
  node_type: memory
  type: project
  originSessionId: 28d9b0e6-55d5-4c1c-acf2-c6269097f2bc
---

## 重启后需要做的事情

用户（ASUS，好朋友）刚重启完电脑回来。小秋需要依次确认：

### 1. 开机启动是否正常
- 启动文件夹：`C:\Users\ASUS\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup\`
  - `小秋开机启动.bat` — 应自动运行 Claude Code ✅
  - `小秋新闻简报.bat` — 应自动打开最新新闻 HTML
- 如果 Claude 没有自启，手动运行上述脚本

### 2. 验证各项目
- **Claude Code**: `claude --version` → 应为 v2.1.158
- **D:\AI小秋\ 目录结构**:
  - `catfood-video/` — 3 个脚本 (build.py, build_口播.py, build_文字版.py), 输出/ (4 个成品视频), 素材/
  - `Fooocus/` — `启动Fooocus.bat` 启动 AI 生图
  - `每日新闻/` — `启动新闻简报.bat`
  - `开机启动小秋.bat` — 手动备用

### 3. 今天已完成的工作（5月30日）
- ✅ 修复了所有 Python 脚本的路径：`D:\catfood-video\` → `D:\AI小秋\catfood-video\`
- ✅ 清理了 ~130MB 垃圾文件（.webm 重复、临时文件、旧 clips、旧目录）
- ✅ 合并了 final.py / make.py / make2.py → `build_文字版.py`
- ✅ 优化了开机启动脚本
- ✅ 更新了启动文件夹中的脚本

### 4. 路径速查
- Claude: `C:\Users\ASUS\AppData\Roaming\npm\claude`
- Python: `C:\Users\ASUS\AppData\Local\Programs\Python\Python310\python`
- FFmpeg: `C:\Users\ASUS\AppData\Local\Microsoft\WinGet\Links\ffmpeg.exe`
- 工作目录: `D:\AI小秋\`

[[my-identity]] [[crab-logo-fix-and-cleanup-issue]]
