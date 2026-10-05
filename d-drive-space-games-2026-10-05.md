---
name: d-drive-space-games-2026-10-05
description: D盘324GB只剩9.9GB，TapTap\PC Games三款游戏占234.67GB；pagefile在C/D双盘且都是系统自动管理
metadata:
  node_type: memory
  type: project
  originSessionId: eaec323c-5b87-4b2a-aa70-1e37e7818350
  modified: 2026-10-05T14:41:53.401Z
---

2026-10-05 排查「游戏进程一直吃我的内容，每次都得关游戏才能解决」。**爸爸原话里"内容"指内存还是硬盘，当时未确认。**

**D 盘**：324.2GB 只剩 9.9GB（97% 满）。元凶是 `D:\TapTap\PC Games\` 一个目录 **234.67GB**，三款游戏：

| 目录 | 游戏 | 占用 | 最后改动 |
|---|---|---|---|
| `663867-鸣潮` | 鸣潮 | 102.68GB | 09-30 |
| `713200-绝区零` | 绝区零 | 69.56GB | 09-07 |
| `188212` | 《洛克王国：世界》(腾讯/UE4) | 62.43GB | 09-26 |

其余大户：Program Files 16.76 / D:\下载 15.14 / 虚拟人总项目 13.8 / ps 10.24 / XXMI 5.53 / steam 3.23 / **回收站 2.46（可直接清空）**。

**内存**：物理 15.25GB，常驻已用 87%（仅开 Code 1.9G + Edge 1.7G + 豆包 1.4G 就吃掉 5G）；`Memory Compression` 涨到 1GB = 内存吃紧信号。
**虚拟内存**：`C:\pagefile.sys` 12.7GB + `D:\pagefile.sys` 4.5GB，**两个都是"系统自动管理"**（`Win32_PageFileSetting` 的 InitialSize/MaximumSize 均为 0）→ 内存一紧会自动膨胀，直接吃对应盘。
**没查到** `Microsoft-Windows-Resource-Exhaustion-Detector` 事件 → 尚未到硬性 OOM。

**待办**：确认"内容"=内存还是硬盘；确认后决定是否清回收站 / 卸游戏（卸载需爸爸点名）。

相关：[[c-drive-bloat-sources]] [[all-downloads-to-d-drive]]
