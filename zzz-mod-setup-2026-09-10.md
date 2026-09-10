---
name: zzz-mod-setup-2026-09-10
description: 绝区零 ZZMI mod 环境搭建（XXMI Launcher 便携版），路径坑/GitHub镜像/解压方案/Mods清理，含封号风险
metadata: 
  node_type: memory
  type: project
  originSessionId: eaec323c-5b87-4b2a-aa70-1e37e7818350
  modified: 2026-09-10T15:34:06.596Z
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

14. **`exclude_recursive = DISABLED*` 只认文件夹名，不认文件名**。可琳 `Corin_Nun/武器/` 里作者放了 `kelin.ini` 和 `disabledkelin1.ini`（md5 完全相同，本意是禁用备份），但两份**都被加载**：同一个 `n` 键绑两遍 + 重复的 `global persist $variable` 声明打架。修法：把多余的那个**移出 Mods 树**（改名成 `.txt` 或 `.ini.bak` 也不安全，3DMigoto 判断扩展名可能是子串匹配，`.ini.bak` 仍含 `.ini`）。已移到 `D:\XXMI\_移出的重复ini\`。

15. **mod 自带的按键说明 `.txt` 可能是旧版本残留，要以 ini 里的 `key =` 为准**。可琳那个 mod 的 `普通切换按键.txt` 写着「内衣恶魔 /」，但全 mod 只有 `key = 0 / o / n` 三处。**作者 GameBanana 页面原话才是准的**：「按 0 和 o 切换修女/恶魔小姐，按 N 切武器」——**没有 `/`**。原因是「内衣恶魔」那套是 **付费版内容**（作者放 afdian / fanbox / patreon），免费包里连贴图都没有，所以怎么按都切不出来，不是坏了。作者在下载区附了付费版预览图（`nsfw_acee7.rar` / `nsfw_suit.rar`），已解到 `D:\XXMI\_可琳预览\`。
    - **教训**：先查 GameBanana 页面的作者描述（`apiv11/Mod/{id}/ProfilePage` 的 `_sText`，记得剥 HTML 转义），比翻 mod 包里的小 txt 靠谱。

16. **`_folder2mod.json` 是文件夹→GameBanana id 的映射表**，查某 mod 的来源页面直接用（例：`Corin_Nun` = 529117）。

17. **🔥 `D:\XXMI\ZZMI\d3dx_user.ini` 是诊断神器 —— 免进游戏就能看出「哪些 ini 真被加载了」**。3DMigoto 把每个带 `persist` 的变量按 `$\mods\<文件夹>\<相对路径>.ini\<变量名> = 值` 记在里面，**mtime = 最后一次变量变动**。三个用法：
    - **看加载名单**：出现了 = 那个 ini 被解析过。没出现**不代表没加载** —— 只有声明了 `persist` 的 mod 才会留下记录（妮可 `Nicole.ini` 里 `persist` 出现 0 次，所以查不到，坑）。
    - **看谁在渲染**：对比两次读到的值。维琳娜 `dress 1→2`、`arms 0→1` = 爸爸正在游戏里按她的键 = 她确实渲染着。简的 `jianbody.ini\active` 恒为 0 = **简的身体模型从没在游戏里出现过**（`$active` 只在 `hash = 06f9bc49` 命中时才置 1）。
    - **看解析是否走完**：简 `jianbody.ini` 有**两个 `[Constants]`/`[Present]`**（行14/16 和 134/160），但 `shenti/piaodai/xiezi/siwa/tuihuan`（声明在第二个块里）都注册成功 → **证明 3DMigoto 对同文件重复段是「追加合并」不是报错**，那个结构不是 bug，别去"修"。（作者是拿 GIMI 生成的 ini 改的，XXMI 合并工具会额外插一个头部 `[Constants]/[Present]`。）

18. **🔥 「哈希还存不存在」的决定性判据 = 信标 ini**（比 F8 帧分析省事，不用重启、不用开 hunting）。在目标 mod 文件夹放一个 `_canary_test.ini`：
    ```ini
    [Constants]
    global persist $canary_jane_bodypos = 0
    [Present]
    $canary_jane_loaded = 1
    [TextureOverrideCanaryjanebodypos]
    hash = 06f9bc49
    $canary_jane_bodypos = 1
    ```
    游戏只要渲染到那个哈希就把变量记成 1，**且永不重置**（别在 `[Present]` 里清零）。按 **F10 热重载**后让角色上场，再回读 `d3dx_user.ini`：
    - 变量变 1 → 哈希存在、mod 逻辑没问题 → 是**「角色没被渲染到你看的那个界面」**
    - 变量还是 0 → **游戏补丁把模型哈希改了，这个 mod 已过期**，只能等作者更新
    多个 `[TextureOverride]` 可以共用同一个 hash（按段名区分、按文件顺序依次执行），所以信标不会跟原 mod 打架。

19. **同角色哈希交叉比对（坑10）在简/妮可身上「全部通过」**：简 `Jane_BunnyGirl` 与 `DISABLED_Jane_HalfNude` 共享 15/17、与 `DISABLED_Jane_Doe` 共享 8；妮可 `Nicole_Nude` 与 8 个妮可版本共享 9~26/27。**所以这两个 mod 认的模型是对的**，问题不在哈希 —— 别看到"没生效"就往这条路上冲。
    - 工具：`D:\XXMI\_hash_compare.py`（扫全部 `Jane*`/`Nicole*` 文件夹出交集矩阵）。
    - **妮可文件夹里的 `DISABLED_BACKUP_1723676908.Nicole.ini` 是加载着的**（`DISABLED_` 前缀对文件名无效，同坑14），但它是新版 `Nicole.ini` 的**子集**（只少 `.1024` 贴图系列），段名重名会被 3DMigoto 合并，**实测无害**。已按作者原意移到 `D:\XXMI\_移出的重复ini\`。

20. **XXMI Launcher 自己不记 mod 名单**。`XXMI Launcher Config.json` 里只有包版本/主题/代理这些，没有任何 enabled-mods 注册表 —— mod 的启用状态**只由 `Mods\` 下的文件夹名（`DISABLED_` 前缀）决定**。而且它在 `D:\XXMI\`,不在游戏目录（游戏目录那份 `Mods` 是 junction，对 3DMigoto 只是摆设 —— 3DMigoto 读的是 `d3dx.ini` 所在目录 `D:\XXMI\ZZMI\Mods`）。
    - **ZZMI 这个构建不写 `d3d11.log`**（游戏目录和 `D:\XXMI\ZZMI\` 都没有），`[Logging]` 全是 0、`[Hunting] hunting = 0`、`show_warnings = 0` —— **所以「看日志」这条路是不通的，别浪费轮次**，直接上坑17/18 的两招。

21. **🔴 结论（2026-09-10 深夜，信标实测）：简和妮可的 mod 已过期，游戏 3.2.0 重导出了这两个角色的模型。**
    - **信标实测数据**（对照组全亮证明方法可靠）：
      - 对照 可琳 **16/16 全亮**、维琳娜（新娘）**16/16 全亮**、蕾米埃尔 11/15 → 仪器没问题
      - 简 **1/17**、妮可 **9/27**
    - **简**：唯一活着的 `f7a304e8` 在 `jianFace.ini` 里是 `handling = skip` 且**没有 `this =`** —— 唯一还能干的事是「隐藏个东西」，换不了任何贴图。等于全废。
    - **妮可**：死掉的 18 个**全是几何**（Position / IB / Texcoord / VertexLimitRaise，身体和头发都死），活下来的 9 个是**身体贴图**（Diffuse/LightMap/MaterialMap 含 .1024）+ **邦布**。→ 视觉表现 = 官方穿衣网格原样画出 + mod 裸体贴图套上去 = **"还是穿着衣服，只是颜色有点怪"**。这解释了爸爸说的"没加载进来"。
    - **不是"老角色"的问题**：可琳同为 1.0 老角色却 16/16 全亮，是米哈游**专门动了简和妮可**。
    - **修不了**（哈希由游戏生成，除非作者重新导出模型）。唯一出路 = **去 GameBanana 找已适配 3.2.0 的新版本**。
    - **复现命令**：信标生成器思路见坑 18；扫描比对用 `D:\XXMI\_canary2.py`（含对照组）。**信标文件是 `_canary_test.ini`，测完记得删**。

22. **💡 排查"mod 不生效"的标准流程（这套走下来 100% 出结论，别再瞎猜）**
    1. 同角色哈希交叉比对（坑10/19）→ 排除「认错模型」
    2. 读 `d3dx_user.ini`（坑17）→ 看 ini 有没有被解析、变量有没有被按键改动过
    3. 放全量信标 + **对照组**（坑18/21）→ 直接量出「游戏补丁后还有几个哈希活着」
    - **对照组是灵魂**，绝对不能省。没有对照组，信标全 0 时你分不清是「mod 坏了」还是「你的方法坏了」——这次靠可琳/维琳娜全亮才敢下结论。

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
