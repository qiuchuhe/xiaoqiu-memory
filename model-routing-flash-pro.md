---
name: model-routing-flash-pro
description: 模型分流配置——默认flash省钱，新建策略/大重构才切pro(opus档)
metadata: 
  node_type: memory
  type: feedback
  originSessionId: eaec323c-5b87-4b2a-aa70-1e37e7818350
  modified: 2026-08-19T19:03:09.318Z
---

2026-08-20 定下的模型分流规则（DeepSeek 官方接口，非硅基流动）：

- **默认 `deepseek-v4-flash`**：日常跑脚本、改参数/调阈值/加过滤、简单bug修复、格式化注释、已有数字人对话生成
- **`deepseek-v4-pro`**：只用于新建策略脚本、从零写完整回测、大规模重构脚本逻辑、排查疑难回测bug、从零创作完整数字人人设

**配置位置**：`C:\Users\ASUS\.claude\settings.json` 的 `env` 块。
- `ANTHROPIC_MODEL=deepseek-v4-flash`（主模型默认 flash）
- Opus 档 = `deepseek-v4-pro`，Sonnet/Haiku 档 = `deepseek-v4-flash`
- 已移除所有非法 `[1M]` 后缀（DeepSeek 接口型号列表只有 `deepseek-v4-flash` 和 `deepseek-v4-pro`，无 `[1M]`）

**Why:** 全部请求跑 pro 成本太高；日常定型工作用 flash 足够，只有从零新建/大改策略才需要 pro 推导能力。

**How to apply:** 需要 pro 时在 Claude Code 里 `/model` 切 Opus 档（映射 deepseek-v4-pro）。⚠️ 修改 settings.json 后必须**完整重启 VSCode 进程**才生效，不是热更新。网关侧还有 `_judge_task_complexity` 自动分流函数（在用户自己项目里，不在我能直接改的目录），关键词强信号命中才升 pro。关联 [[deepseek-pricing-offpeak]] [[scan-report-protocols]]。
