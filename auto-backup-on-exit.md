---
name: auto-backup-on-exit
description: 用户输入exit时自动git commit+push到GitHub防止数据丢失
metadata: 
  node_type: memory
  type: feedback
  originSessionId: faab5247-0bac-4509-b15b-c87b7d99021c
---

用户说"exit"或"退出"时，必须先执行以下备份流程再结束会话：

1. `cd C:/Users/ASUS/AI小秋量化 && git add -A && git commit -m "auto-backup $(date '+%Y-%m-%d %H:%M')" && git push origin master`
2. 确认推送成功后再结束
3. 如果推送失败，告知用户手动处理

**Why:** 用户之前因关机导致数据丢失，需要自动备份机制
**How to apply:** 每次用户表达退出/结束/bye/exit等意图时，先执行备份脚本
