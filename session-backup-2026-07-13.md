---
name: session-backup-2026-07-13
description: 7/13全天——秋策双核战法v3.1、评分体系重构、AB轨道合并、策略统一
metadata:
  type: project
---

## 核心成果：策略输出质量升级 v3.1

### 取消的大方向
- 否决了桌面GUI自动交易方案。用户实际是手动模式：四个时段扫描→排序→建议→手动同花顺下单

### 五大改进

1. **综合评分系统（0-100分）**
   技术面75 + 朋友逻辑25：
   - 买点质量25 | 均线距离15 | 量比强度15 | 涨幅位置10 | 换手率10
   - 热门板块10 | 龙头排名10(#1=10,#2=8,#3=6) | 情报点名5

2. **买入时机四分类**
   - 今日可买 / 今日可买+朋友确认 / 明日开盘买 / 等回落XX / 等放量确认

3. **时段差异化** --window: auction/morning/midday/late

4. **策略可插拔** strategies.json注册表

5. **输出格式升级** 含评分、时机、止损、风险金额、盈亏比

### 策略统一
- 策略1+轨道B → 秋策·双核战法（一套评分体系统一输出）
- 删除 dual_track.py/json（旧AB轨道PK系统）

### 修改的文件
- config.py: +SCORING +TIMING +WINDOW_CONFIG
- scanner.py: +calc_score +classify_timing +_load_friend_data +_print_one_v3 +--window
- track_b.py: 导入评分函数 +_print_result_v3 +--window
- catchup_market.py: 从strategies.json读取 +传递--window

### 新建文件
- D:\AI小秋\策略量化\strategies.json

### 删除文件
- D:\AI小秋\量化\dual_track.py
- D:\AI小秋\量化\dual_track.json

### 盘前准备(7/14周一)
- 持仓: 算力AI(159819)2.202、5G通信(159994)1.559、科创50(588000)2.333
- 科创50最危险 2.209 vs 止损2.15
- 霍尔木兹无限期封锁，周一大概率低开
- 预案: 588000破2.15止损500股留100底仓

**Why:** 策略输出从二分法升级到评分排序+时机分类，人工决策效率大幅提升
**How to apply:** 交易时段跑catchup_market.py，按评分排序看#1即可
