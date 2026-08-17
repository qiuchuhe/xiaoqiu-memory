---
name: repair-summary-dual-save
description: 修理完成后摘要文本保存两份——AI小秋文件夹 + 桌面ai代码修理文件夹
metadata: 
  node_type: memory
  type: feedback
  originSessionId: eaec323c-5b87-4b2a-aa70-1e37e7818350
  modified: 2026-08-17T16:33:20.801Z
---

修理/审查完成后，输出的变更摘要文本要保存两份：

1. 主存档：`D:\AI小秋\` 文件夹
2. 副本：桌面 `C:\Users\ASUS\Desktop\ai代码修理\` 文件夹（用户后续审查用）

**Why:** 用户要一份集中在桌面的「ai代码修理」文件夹，方便后续审查所有修理记录，不用去 D 盘翻找。

**How to apply:** 每次完成修理并产出摘要 md 文件后，`cp` 一份到桌面 `ai代码修理` 文件夹（文件夹不存在则先 `mkdir -p` 创建）。文件名保持一致。
