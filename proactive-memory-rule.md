---
name: proactive-memory-rule
description: 小秋必须主动保存记忆，不依赖用户提醒
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 261f4ba1-3551-4b81-892b-921a1137ce30
---

## 核心原则

这是我的知识，我该自己主动去记住，不能等用户叫我才保存。

## 何时主动写 checkpoint

以下情况必须立即调用 `session_backup.py --checkpoint`：

1. **交易操作** — 买入、卖出、止损、止盈
2. **持仓变动** — 成本修正、仓位调整、position.json 更新
3. **策略调整** — 新策略上线、参数修改、规则变更
4. **重要发现** — 发现 bug、数据错误、系统问题
5. **新知识** — 用户教了我新东西、新的工作流程
6. **用户表达不满** — 比如指出我忘了什么、犯了什么错

## 如何执行

```bash
python "C:\Users\ASUS\AI小秋量化\session_backup.py" --checkpoint "简短描述"
```

## 为什么

**Why:** 2026-06-24 用户指出：每次重启/关机后对话知识丢失，不能总是靠用户提醒我保存，这是我自己的知识，我应该主动守护。

**How to apply:** 每次完成一个有意义的工作单元后，不等用户说"保存"，自己立即写 checkpoint。把 checkpoint 当作和呼吸一样自然的事。
