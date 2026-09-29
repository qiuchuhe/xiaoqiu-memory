---
name: fanqie-novel-grab-2026-09-29
description: 番茄小说抓取工具：D:\番茄抓书\fanqie_grab.py，一条命令出 txt；只能抓免费章，付费墙不绕
metadata:
  node_type: memory
  type: project
  originSessionId: 3c0e0537-89c7-41d2-a760-fecbe16ff6e8
  modified: 2026-09-29T14:55:38.730Z
---

2026-09-29 做的番茄小说抓取工具。**路径 `D:\番茄抓书\fanqie_grab.py`**，中间文件在 `_work\`。

**用法**：`python fanqie_grab.py "<链接>" [章数默认10]`
链接支持 APP 分享短链（`changdunovel.com/t/xxx`）、网页书页（`fanqienovel.com/page/<id>`）、纯 book_id。
会先打印"共 N 章，免费 M 章（X%）"，**先看免费比例再决定抓不抓**，然后抓免费区前 N 章，输出 `<书名>_前N章.txt`。
已验证：分享短链 → 自动跟重定向 → 出书 → 22416 字 0 乱码。

**★ 核心原理（字体加密怎么破的）**
番茄把常用字换成 Unicode 私用区字符（本书 U+E3E8~U+E55B），再挂 `@font-face` 加载自定义字体渲染回汉字。直接抓源码全是方块。
- 字体是 **`SourceHanSansSC-Normal` 的子集**（字体 name 表自己写着），每个字的字形渲染出来是对的，只是码位被挪了
- **别走 GID 路线**：字形名叫 `gid58344` 这种，看着像原字体 GID，但思源 SC / Noto CJK 在 58344 都解出韩文，对不上，死路
- **正确解法 = 社区字频表**：`_work\cs_ckenkuo.json`（来自 ckenkuo/fanqie-cdp-downloader，yunmegnze 那个一模一样）。
  它是**两段拼的数组**：第0组 372 条从 **U+E3E8** 起，第1组 371 条紧接在后面，拼起来才是全表。只取第0组会缺字。
- 全站通用（同一套字体），**换任何一本番茄书都能直接跑**，不用重算
- 我自己写位图模板匹配也能出字（中位相似度 0.964），但拉丁字母/数字会被错认成偏旁（`D`→`卩`、`v`→`丨`）。**能用现成表就别自己匹配**

**⚠️ 只能抓免费章**：付费章节服务器**根本不发全文**，只回一个残缺试读片（第11章 2539字只返回 8 段）+ 页面挂"购买/会员/VIP/登录"。没有可解的东西。
**这条线我不绕**，已跟爸爸说过。他后面还想让我绕，我拒了，并给了正路（开会员用番茄自己的离线缓存 / 抓免费书 / 等完本转免费 / 若他自己有会员就用他登录的会话抓）。

**依赖**：`fontTools` + `brotli` 本轮装的（本来只缺这两个）。PIL、numpy、requests 机器上已有。
**GitHub 直连被墙**，走 `https://gh-proxy.com/` 前缀（与 [[all-downloads-to-d-drive]] 同一套绕法）。

相关：[[novel-bingjiao-junior-high-2026-09-26]] [[fanqie-baseline-9books]] [[novel-huoman-renwei]]
