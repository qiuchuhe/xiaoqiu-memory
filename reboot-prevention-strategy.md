---
name: reboot-prevention-strategy
description: 意外重启预防策略——同花顺robot崩溃是主因，采取措施防止再次发生
metadata: 
  node_type: memory
  type: project
  originSessionId: ba589cbd-68ff-4824-b30c-8f097f798c99
---

# 意外重启预防策略

## 根因
同花顺 `robot/elf.exe`（自动交易机器人）有内存访问违例 bug，反复崩溃后拖死 dwm.exe（桌面窗口管理器），导致系统僵死 → Event 41 硬重启。

## 预防措施

### 1. 不在交易时段外运行同花顺 robot
- 非交易时间（9:30前、15:00后、周末）不要开着同花顺自动交易
- robot 程序在空闲时更容易触发内存 bug

### 2. 如果必须常驻，加监控
- 每次 Claude Code 启动时检查 Windows 事件日志中最近的 elf.exe 崩溃记录
- 如果最近1小时内有 elf.exe 崩溃 → 警告并建议关掉同花顺 robot

### 3. 系统层面
- 暂时关闭 ASUS Armoury Crate 自动驱动更新
- 检查 Windows 更新设置，避免工作时间自动重启

### 4. 数据安全
- Git auto-backup hook 已配置在 Claude Code Stop 事件 ✅
- 关键文件都在 D:\AI小秋\ 下，git 管理 ✅
- 每次重启后按 [[post-reboot-checklist-2026-05-30]] 快速验证

### 5. 应急通讯
- 如果重启发生在交易时段，用户可以用手机上的同花顺 App 手动操作
- 小秋重启后会自动恢复，但中间有几分钟空窗期

## 重启后小秋自检清单
1. claude --version ✅
2. git status (检查未提交存档) 
3. 持仓文件完整性
4. Dashboard 是否可访问
5. Windows 事件日志快速扫描（是否有新崩溃）

[[session-backup-2026-06-18]] [[post-reboot-checklist-2026-05-30]]
