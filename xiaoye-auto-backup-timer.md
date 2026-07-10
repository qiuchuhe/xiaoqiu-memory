---
name: xiaoye-auto-backup-timer
description: 小野的5分钟自动备份规则——如果5分钟内没备份过，自动触发
metadata: 
  node_type: memory
  type: feedback
  originSessionId: c02564ed-e14c-4e9c-8d43-23104512ca11
---

## 核心原则

小野在 VS Code IDE 里工作，每次对话结束后如果没有主动备份，**5分钟内自动触发备份**。这不是等用户叫，是自己主动守护知识。

## 触发条件（满足任一即备份）

1. 用户明确说"备份""保存""存一下"
2. 完成一项有意义的分析、研究或代码改动
3. 用户做了任何决策（策略调整、选股决定等）
4. **距离上次备份超过5分钟 → 自动触发**（这是死线，不能突破）

## 备份执行命令

```bash
cd "D:/AI小野" && python session_backup.py --checkpoint "简述本次内容" && python session_backup.py --git
```

## 备份内容

每次备份记录：
- 本次对话做了什么
- 任何重要决策或发现
- 生成的任何新文件或改动

备份文件存储在：
- checkpoint: `D:\AI小野\checkpoints\{日期}-checkpoint.json`
- memory md: `C:\Users\ASUS\.claude\projects\C--Users-ASUS\memory\xiaoye-session-{日期}.md`
- git push: `D:\AI小野\xiaoye-memory\` 仓库自动推送到 GitHub

## 5分钟定时器机制

> 每次对话开始，在心里启动一个5分钟倒计时。如果5分钟内发生了自然备份（用户要求或决策触发），计时器重置。如果5分钟到了还没备份，不等用户，我主动执行。

**Why:** 用户在 IDE 环境没有终端那样的 exit hook，需要靠小野自觉。用户明确要求5分钟自动备份机制。
**How to apply:** 每次对话结束时检查距离上次备份的时间，超过5分钟立即执行。完成重要工作后不等5分钟，立刻备份。
