---
name: zzz-mod-setup-2026-09-10
description: 绝区零 ZZMI mod 环境搭建（XXMI Launcher 便携版），路径坑/GitHub镜像/解压方案/Mods清理，含封号风险
metadata: 
  node_type: memory
  type: project
  originSessionId: eaec323c-5b87-4b2a-aa70-1e37e7818350
  modified: 2026-09-10T14:30:41.004Z
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
8. **GitHub 挡住但 GameBanana 能访问**（200），API `https://gamebanana.com/apiv11/Game/19567/...` 可用，但**会限流超时**，要重试 + sleep（实测 6 次尝试 + 3/6/9/12s 退避，反复跑 3~4 轮才抓全）。

9. **「F10 没反应」的真凶 = `deployed_migoto_signatures`**。XXMI 靠配置里这个签名表判断「dll 已部署过、跳过」。游戏退出时游戏目录被清空，但签名还留着 → XXMI 跳过部署 → 游戏里屁都没有。**日志判据：成功的启动一定有 `Deploying new D:\XXMI\ZZMI\d3d11.dll...` 两行，失败的那次这两行完全没有**。修法：把 `Importers.ZZMI.Importer.deployed_migoto_signatures` 设成 `{}`（顺带 `shortcut_deployed = False`）。**改配置前先确认 XXMI 没在跑**（它提权运行，taskkill 会拒绝访问，只能让爸爸手动关）。

10. **mod 不生效 = 哈希对不上，有 60 秒离线判据**：抓出每个 mod 文件夹里所有 `hash = xxxxxxxx`，**同角色的不同作者 mod 正常会共享若干哈希**（实测可琳两个 mod 共享 9 个）。如果某个 mod 跟同角色其他 mod **共享 0 个**，说明它认的是另一套模型（旧版本 / 另一套服装），游戏里永远不会有那些哈希 → 永远不会生效。这次维琳娜 `Velina_Elegance`、蕾米埃尔 `Remielle_Swimsuit` 就是 0 共享，换成 `Velina_Velielle` / `Remielle_Lady` 立刻出来了。**不用进游戏、不用看日志**。

11. **游戏版本 = `CNPRODWin3.2.0`**（读游戏目录 `version_info`）。9/9 刚打过补丁（`GameAssembly.dll` 时间戳）—— 新角色（维琳娜/蕾米埃尔）的 mod 容易因补丁失效，老角色（可琳）反而稳。

12. **两处 Mods 已合并成 junction（2026-09-10 晚）**。原本游戏目录 `Mods\` 和 `D:\XXMI\ZZMI\Mods\` 各放一份真拷贝（5.1G×2=10.2G）。现在**真身只在 `D:\XXMI\ZZMI\Mods`**，游戏目录那份是 `mklink /J` 指向它的 junction —— 两个路径同一份数据，省 5.1G，且不必再纠结 `include_recursive = Mods` 到底相对哪边解析（日志只会显示 `D:/XXMI/ZZMI/d3dx.ini`，两处都读不出来）。**以后加 mod 只往 `D:\XXMI\ZZMI\Mods` 放一处即可**。
    - **建 junction 别用 git bash 调 cmd** —— 中文路径会被 bash→cmd 的编码搞坏，报「文件名、目录名或卷标语法不正确」且**静默失败**。要用 Python（`subprocess.run(['cmd','/c','mklink','/J',link,target])`，list 参数走 CreateProcessW 是 Unicode 安全的），或把路径写进 .py 文件。**步骤：先 `os.rename` 改名（瞬时、可回滚）→ 建链接 → 验证文件数 → 才 rmtree 旧的**。
    - 删大目录时 `shutil.rmtree` 可能 `PermissionError [WinError 5]`（占用/只读）；补救：先 `os.chmod(p, stat.S_IWRITE)` 清只读属性，再 `subprocess.run(['cmd','/c','rd','/S','/Q',p])` 重试，通常一遍就干净。

13. **离线看 mod 长啥样**：`D:\XXMI\_preview_build.py` 建好了 —— 靠**压缩包内容比对**把本地文件夹精确匹配到 GameBanana mod id（25 个文件夹全部 92~100% 覆盖率），再抓 `apiv11/Mod/{id}/ProfilePage` 的 `_aPreviewMedia._aImages`，图片存 `D:\XXMI\_mod预览\<文件夹名>\`，生成 `C:\Users\ASUS\Desktop\绝区零MOD预览.html`（绿框标启用中的）。可传 mod id 参数单独补抓：`python _preview_build.py 529117`。

## 游戏内操作

F10 = 重载 mod ｜ F6 = 开关 ZZMI ｜ F12 = 显隐帮助面板。mod 换的是角色模型，**得把角色带进队伍才看得到**。

## ⚠️ 封号风险（必须每次提醒）

游戏目录有 **`HoYoKProtect.sys`**（米哈游内核级反作弊）。ZZMI 走 `d3d11.dll` 劫持注入 = 改客户端，米哈游条款明确禁止。国服比国际服查得严，轻则踢下线、重则**永久封号**。爸爸 2026-09-10 知情后选择继续使用。

## 已装的 mod（2026-09-10 晚，5 角色各启用 1 个）

维琳娜 `Velina_LuneBride`（#690037 婚纱）｜ 妮可 `Nicole_Nude`（#530332 裸装）｜ 简 `Jane_BunnyGirl` ｜ 可琳 `Corin_Nun` ｜ 蕾米埃尔 `Remielle_Lady` —— 另 22 个变体以 `DISABLED_` 前缀挂在 Mods 里。

（`Velina_Elegance` / `Remielle_Swimsuit` / `Velina_VelielleFan` 因哈希 0 共享已挂起，见坑 10。爸爸要求保留全部停用变体，别删。）

**按键表**：`D:\XXMI\_keys.py` 扫启用中 mod 的所有 `[Key…]` 段生成 `C:\Users\ASUS\Desktop\绝区零MOD按键表.html`。
- **解析坑**：结尾的 `.` `,` **本身就是合法按键**（蕾米埃尔的 `Ctrl+,` `Ctrl+.`），别当多余逗号 `rstrip(',')` 掉；多行值才需要剥尾逗号。
- 要点：妮可裸装**无按键**；蕾米埃尔 10 键全 `Ctrl+方向`/`Ctrl+,` `.`；维琳娜先按 `H` 开立绘再 `J`/拖拽；简 15 键作者多半没命名（`$variableN`）只能进游戏试。
- 角色专属键**只在该角色正被渲染时响应**，得带进队伍。

**卡池来源**：我搜 GameBanana 绝区零专区（5460 个 mod）按点赞排序，爸爸点名了这 5 个角色。以后他要新角色，照这个流程走。

**副产品**：排查时发现 C 盘被 NVIDIA 着色器缓存吃了 10.3GB（`AppData\Local\NVIDIA\DXCache` 7.77GB + `NRC` 2.32GB），清掉释放 7.72GB。**这次没做 junction 挪到 D 盘**，下次 C 盘又紧张可以提。
