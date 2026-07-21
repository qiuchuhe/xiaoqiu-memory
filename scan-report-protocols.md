---
name: scan-report-protocols
description: 三个时段扫描的报告规范——9:36精简低开、10:03精简早盘、13:07完整午盘
metadata: 
  node_type: memory
  type: feedback
  originSessionId: eaec323c-5b87-4b2a-aa70-1e37e7818350
  modified: 2026-07-21T14:42:52.724Z
---

## 三级报告规范

### 💸 低开 (9:36, peak-精简)
```
运行: python "D:\AI小秋\策略量化\策略1\catchup_market.py" --time 9:36
内容: 只报告跌停翘起/低开回升/洗盘吸筹/启动确认的触发名单
规则: 无触发则直接跳过，不做展开
```

### 💸 早盘 (10:03, peak-精简)
```
运行: python "D:\AI小秋\策略量化\策略1\catchup_market.py" --time 10:03
内容: 今日可买信号TOP3 + 持仓检查结果
规则: 不展开评分明细，精简输出
```

### 💰 午盘 (13:07, 便宜-重仓详细)
```
运行: python "D:\AI小秋\策略量化\策略1\catchup_market.py" --time 13:07
内容: 完整输出——
  - 全评分明细
  - 朋友逻辑展开（热门题材？龙头？低吸？）
  - 龙头排名
  - 时机建议
  - 止损止盈价位
  - 风险金额
  - 早盘强势延续判断 + 午后新起方向
规则: 全天最详细的一次分析
```

**Why:** 用户明确分三级——早盘两次精简快速出信号，午盘一次详细展开做决策。避免刷屏。
**How to apply:** 每个时段严格按对应协议输出，不应混用。早盘不过度展开，午盘不过度精简。[[trading-advice-rule]] [[morning-routine]]
