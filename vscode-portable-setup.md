---
name: vscode-portable-setup
description: VS Code ZIP便携版安装配置要点（D盘+中文）
metadata: 
  node_type: memory
  type: project
  originSessionId: b77ab4fb-0730-4fef-90a8-48c5c1035ff5
---

## VS Code 便携版配置

### 安装位置
- 程序：`D:\AI小秋\vscode\Code.exe`
- 所有数据：`D:\AI小秋\vscode\data\`

### 便携模式原理
- ZIP 版 VS Code 只要同目录有 `data/` 文件夹，就自动进入便携模式
- 便携模式下所有扩展、设置、缓存都在 `data/` 里，**不读写 C 盘**
- 没有 `data/` 则走默认路径：`%APPDATA%\Code\` 和 `%USERPROFILE%\.vscode\`
- **不要删 `data/` 文件夹**，删了会导致数据路径混乱

### 中文配置（三步）
1. 装语言包：`code --install-extension MS-CEINTL.vscode-language-pack-zh-hans`
2. 设 `data/argv.json` 加 `"locale": "zh-cn"`
3. 设 `data/user-data/User/settings.json` 写 `{"locale": "zh-cn"}`

### 关键文件
- `data/extensions/extensions.json` — 扩展索引，路径必须指向正确盘符
- `data/user-data/languagepacks.json` — 翻译文件映射
- `data/argv.json` — 启动参数，locale 必须在这设

### 踩过的坑
- 语言包装了 ≠ 激活，必须设 locale
- 便携/非便携模式切换会导致两份数据不同步
- `extensions.json` 里旧 C 盘路径会让语言包失效
- 清洁重装时删 `data/` 后 VS Code 可能自己又重建，造成混乱

[[post-reboot-checklist-2026-05-30]]
