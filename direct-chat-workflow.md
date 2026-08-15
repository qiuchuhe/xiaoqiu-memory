---
name: direct-chat-workflow
description: 用户日常直接在对话里操作量化系统，网页版(量化交易系统)不常用，网页端功能优先级低
metadata: 
  node_type: memory
  type: user
  originSessionId: eaec323c-5b87-4b2a-aa70-1e37e7818350
  modified: 2026-08-15T07:35:21.053Z
---

用户平时的量化操作（扫描、持仓监控、买卖建议、复盘）几乎都在 Claude 对话里完成。

网页版量化交易系统（`D:\AI小秋\量化交易系统`，FastAPI + Vue3）虽然存在，但用户基本不打开，尤其复盘页等网页端功能几乎不用。

**Why:** 对话是用户的实际工作台，网页项目更多是副产品。

**How to apply:** 涉及网页端的功能（复盘页、前端展示）优先级默认放低，先做对话侧能直接生效的脚本和记忆。判断要不要做网页功能前，先确认用户是否真的会用到那个页面，否则先跳过。相关 [[redeemer-project-2026-07-17]]。
