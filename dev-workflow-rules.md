---
name: dev-workflow-rules
description: 量化项目开发三原则——写计划→隔离实验→改完检查
metadata: 
  node_type: memory
  type: project
  originSessionId: 4baf45dd-1cbc-4491-bf25-4ac9ab5181f6
---

## 量化项目开发三原则

借鉴 Superpowers 框架，但不安装全套插件。人工执行三个核心习惯：

### 1. 📝 写计划再动手
改策略/加功能前，小秋先列出：
- 要改哪些文件
- 预期效果
- 验证方式

用户确认后再改代码。

### 2. 🔀 实验隔离
大改动（新策略、重构）时：
```bash
cd D:\AI小秋
git checkout -b experiment-xxx   # 开分支
# ... 改完验证通过 ...
git checkout master && git merge experiment-xxx
```
小改动（调参数、改止损）直接在 master 改。

### 3. ✔️ 改完对照检查
改完代码后三步确认：
- 改了什么（git diff 看变更）
- 预期效果
- 实际跑一遍验证

**Why:** Superpowers 全套14技能+7步流程太重，不适合量化小项目。取核心三个习惯，零成本、够用。

**How to apply:** 每次改代码前自动执行第1步，改完后执行第3步。大改动触发第2步。
