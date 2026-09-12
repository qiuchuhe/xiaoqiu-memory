---
name: c-drive-bloat-sources
description: C盘常年紧张(2026-09-13只剩8.5GB)，三大自动膨胀源与可安全清理清单
metadata: 
  node_type: memory
  type: project
  originSessionId: eaec323c-5b87-4b2a-aa70-1e37e7818350
  modified: 2026-09-12T19:01:52.925Z
---

C 盘 151.5 GB 常年吃紧，2026-09-13 清理前只剩 8.5 GB。**会自己涨的三个源**：

1. **NVIDIA 着色器缓存** — `AppData\Local\NVIDIA\DXCache`(3.1GB) + `AppData\Local\NRC`(2.4GB)。每次玩 ZZMI 绝区零，显卡编译着色器就攒一批。删了自动重建，代价只是进游戏首次慢几秒。
2. **Ubisoft Connect 补丁缓存** — `C:\ProgramData\Ubisoft\Ubisoft Game Launcher\patch`。启动器**每次自动更新就把整个新版本重下一份(约 577 MB)且从不删旧的**，2026-09-13 时堆了 13 个版本共 7.16 GB。爸爸不玩育碧游戏，已删（C 盘 8.48→15.64 GB）。以后再涨就是这个目录，同样可删。
3. **豆包/微信日志** — 豆包 `AppData\Local\Doubao\User Data\sdk_storage\log`(677MB)，微信 `AppData\Roaming\Tencent\xwechat\log`(489MB)，天天涨。

**看不见的大块（资源管理器默认隐藏）**：`C:\pagefile.sys` 16.7GB（虚拟内存，别动）、`C:\hiberfil.sys` 6.1GB（休眠文件，`powercfg /h off` 可释放但失去快速启动，爸爸未拍板）。

**关键：清理前必须让爸爸明确点名要删哪些路径。** Claude Code 的权限分类器会拦下「未被用户点名目标」的删除操作（2026-09-13 实测被拦一次），不能自己决定。侦察脚本可以随便跑，删除脚本必须等点名。

D 盘也在变紧（2026-09-13 剩 17.6 GB），装 ZZMI mod 前先看余量。参见 [[all-downloads-to-d-drive]]、[[zzz-mod-setup-2026-09-10]]。
