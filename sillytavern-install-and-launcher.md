---
name: sillytavern-install-and-launcher
description: 酒馆SillyTavern本体位置、启动命令、桌面启动器，以及bat中文编码坑
metadata:
  node_type: memory
  type: project
  originSessionId: ee218050-09cf-4b93-996a-c07ef2874ddf
  modified: 2026-10-08T11:23:17.005Z
---

酒馆本体在 `D:\虚拟人总项目\SillyTavern-1.18.0`（**不是** `D:\SillyTavern`，那儿只有 live2d 扩展包，容易找错）。端口 8000，config.yaml 里 `browserLaunch.enabled: true`，起来会自动开浏览器。node v24.16.0。

启动：`cd /d "D:\虚拟人总项目\SillyTavern-1.18.0"` 然后 `node server.js`。**别用自带的 Start.bat** —— 它每次跑 npm install，慢。

桌面启动器：`C:\Users\ASUS\Desktop\启动酒馆.bat`（爸爸自己双击用）。逻辑 = 先查 8000 端口有没有 LISTENING，有就直接开浏览器页面，没有才启动服务；目录不存在会提示报错。

**坑（写任何中文 bat 都适用）**：中文 Windows 上 bat 必须是 **GBK 编码 + CRLF 行尾**。UTF-8 无 BOM 的 bat 会被 cmd 按 GBK 逐字节解析，中文字符字节数对不上导致文件指针错位，整行被撕碎报 `'xx' 不是内部或外部命令`；只有 LF 行尾会让跨行 `( )` 块解析错乱，同样撕行。生成方式：写 UTF-8 → `iconv -f UTF-8 -t GBK | sed 's/$/\r/'` 输出。

相关：[[sillytavern-default-character-restore-2026-08-31]] [[gpt-sovits-tts-setup-2026-08-23]]
