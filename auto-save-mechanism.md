---
name: auto-save-mechanism
description: 退出自动保存机制——防崩溃丢内容，含Stop hook+checkpoint+session_backup脚本
metadata: 
  node_type: memory
  type: project
  originSessionId: 0291993f-1e4c-4768-8ed4-06784ba264ac
---

## 退出自动保存机制

### 架构（三层防护）

| 层 | 触发 | 位置 | 作用 |
|---|------|------|------|
| Checkpoint | 会话中途关键决策后 | `D:\AI小秋\checkpoints\{date}-checkpoint.json` | 崩溃后恢复最近状态 |
| Stop hook | 正常退出时 | `.claude/settings.local.json` | 自动跑session_backup.py + git push |
| Memory文件 | Stop hook触发后 | `memory/session-backup-{date}.md` | 下次启动时读取回忆 |

### 脚本
- `C:\Users\ASUS\AI小秋量化\session_backup.py`
- 用法:
  - `python session_backup.py --checkpoint "决策1" "决策2"` — 会话中途写检查点
  - `python session_backup.py --auto` — 自动收集+保存（hook用）

### Stop hook 配置
在 `settings.local.json` 中，退出时依次执行:
1. `python session_backup.py --auto` → 收集信息+写memory
2. git pull → cp memory → git commit → git push → 同步到GitHub

### 恢复流程
1. 重启后读取 `memory/session-backup-{date}.md`
2. 如果当天的不存在，读 `D:\AI小秋\checkpoints\{date}-checkpoint.json`
3. 如果都没有，检查 `D:\AI小秋\每日信息\{date}\会话备份.md`

### 小秋操作清单
- 每次重要决策后: 运行 `python session_backup.py --checkpoint "决策描述"`
- 退出前: Stop hook 自动处理
- 重启后: 先读 memory/MEMORY.md → 找最新的 session-backup
