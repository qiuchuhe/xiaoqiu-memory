---
name: zzz-mod-setup-2026-09-10
description: 绝区零 ZZMI mod 环境搭建（XXMI Launcher 便携版），路径坑/GitHub镜像/解压方案/Mods清理，含封号风险
metadata: 
  node_type: memory
  type: project
  originSessionId: eaec323c-5b87-4b2a-aa70-1e37e7818350
  modified: 2026-09-10T11:39:53.633Z
---

2026-09-10 给爸爸搭好了**绝区零换装 mod 环境**。游戏是 TapTap 国服，装在 `D:\TapTap\PC Games\713200-绝区零\games\ZenlessZoneZero Game`。

## 环境位置（全在 D 盘，合 [[all-downloads-to-d-drive]]）

- **XXMI Launcher 便携版 v2.2.1** → `D:\XXMI\Resources\Bin\XXMI Launcher.exe`，配置写 `D:\XXMI\XXMI Launcher Config.json`
- **XXMI 核心 loader v1.0.5** → `D:\XXMI\Resources\Packages\XXMI\`（`d3d11.dll` + `3dmloader.dll` + `d3dcompiler_47.dll` + `Manifest.json`）
- **ZZMI 加载器 v1.4.5** → `D:\XXMI\ZZMI\`（`d3dx.ini` + `Core/` + `ShaderFixes/`）
- **mod 放这里** → `<游戏目录>\Mods\`（`d3dx.ini` 里 `include_recursive = Mods`，递归扫描）

## 踩过的坑（都验证过，别再犯）

1. **ZZMI 不能放 `Resources\Bin\` 里**。日志铁证 `VerifyFileAccess(path=D:/XXMI/ZZMI/Core/ZZMI/main.ini)` —— importer 目录要跟 `Resources` **平级**，放错就是 `ZZMI N/A`。
2. **XXMI 核心在 `Resources\Packages\<包名>\`**，不是根目录（从 exe 里挖到 `Resources/Packages/GI-FPS-Unlocker/...` 才知道）。
3. **GitHub 在这台机器完全不通**（`github.com` 000），XXMI 自己的「安装」按钮必失败。绕法：**`https://gh-proxy.com/https://github.com/OWNER/REPO/releases/download/TAG/FILE`**，API 也用同样前缀（`ghproxy.net` 对 API 路径返回 Invalid input，**认准 gh-proxy.com**）。
4. **`auto_update` 要设 False**，否则每次启动都弹「验证 GitHub SSL 证书失败」。
5. **截图问爸爸要信息最费轮次** —— 直接从 `D:\XXMI\XXMI Launcher Log.txt` 读 DEBUG 日志 + 用 python 在 `XXMI Launcher.exe` 里搜字符串，比问他快得多。
6. **解压不用装 7-Zip/WinRAR** —— Windows 自带的 `C:\Windows\System32\tar.exe`（bsdtar）**rar / 7z / zip 三种全支持**。
7. **同角色多个 mod 会打架**（花屏错位）。规则：**每个角色只启用一个**，其余文件夹名前加 `DISABLED_`，按 F10 热重载即时切换。
8. **GitHub 挡住但 GameBanana 能访问**（200），API `https://gamebanana.com/apiv11/Game/19567/...` 可用，但**会限流超时**，要重试 + sleep。

## 游戏内操作

F10 = 重载 mod ｜ F6 = 开关 ZZMI ｜ F12 = 显隐帮助面板。mod 换的是角色模型，**得把角色带进队伍才看得到**。

## ⚠️ 封号风险（必须每次提醒）

游戏目录有 **`HoYoKProtect.sys`**（米哈游内核级反作弊）。ZZMI 走 `d3d11.dll` 劫持注入 = 改客户端，米哈游条款明确禁止。国服比国际服查得严，轻则踢下线、重则**永久封号**。爸爸 2026-09-10 知情后选择继续使用。

## 已装的 mod（2026-09-10 首批，5 角色各启用 1 个）

妮可 Nicole_Casualwear ｜ 简 Jane_BunnyGirl（100MB）｜ 蕾米埃尔 Remielle_Swimsuit ｜ 维琳娜 Velina_Elegance ｜ 可琳 Corin_Nun —— 另 20 个变体以 `DISABLED_` 前缀挂在 Mods 里。

**卡池来源**：我搜 GameBanana 绝区零专区（5460 个 mod）按点赞排序，爸爸点名了这 5 个角色。以后他要新角色，照这个流程走。

**副产品**：排查时发现 C 盘被 NVIDIA 着色器缓存吃了 10.3GB（`AppData\Local\NVIDIA\DXCache` 7.77GB + `NRC` 2.32GB），清掉释放 7.72GB。**这次没做 junction 挪到 D 盘**，下次 C 盘又紧张可以提。
