---
name: desktop-claude-archive-rule
description: 所有产出文件额外归档到桌面 Claude 文件夹，按 5 类子目录分类
metadata: 
  node_type: memory
  type: feedback
  originSessionId: eaec323c-5b87-4b2a-aa70-1e37e7818350
  modified: 2026-08-17T16:59:14.995Z
---

后续所有产出的报告、优化工单、代码修改摘要、方案文档，**除了原有输出路径外**，额外在桌面 `C:\Users\ASUS\Desktop\Claude\` 文件夹内同步留存一份副本。按目录规则分类：

1. **量化相关、早报、晚间情报、选股复盘文档** → `Claude\早报\`
2. **代码修复、系统改造工单、BUG整改清单** → `Claude\ai代码修理\`
3. **数字人、SillyTavern、图像视频相关方案** → `Claude\数字人说明\`
4. **各类备忘、记录清单** → `Claude\记录本\`
5. **整套系统整合说明、汇总文档** → `Claude\分享ai整体内容\`

**Why:** 用户要一个集中的桌面归档结构，所有成品按类落位，避免文档丢失、方便后续审查/分享。这取代了旧的「桌面根目录 ai代码修理」单文件夹方案（[[repair-summary-dual-save]]）。

**How to apply:** 每完成一轮工作产出成品文件后，主动 `cp` 到对应的 `Claude` 子目录（`mkdir -p` 先确保目录存在，中文路径用引号）。分类判断：量化分析/情报/选股→早报；代码/工单/BUG→ai代码修理；数字人/ST/图像→数字人说明；备忘→记录本；系统汇总/流程说明→分享ai整体内容。
