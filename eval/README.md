# EdgeLab Skills 测评方法论

一套 skill 好不好用，不靠感觉——靠可重复的评测。本目录是 EdgeLab skills 的评测方案（方法论公开；具体测试题库因含真实交易数据，私有保存，不在本 repo）。

## 为什么这么测（行业依据）

借鉴 2026 agent 评测主流做法：LLM-as-judge + rubric、execution-based 验证（τ-bench 查状态）、pass^k 可靠性（τ²-bench）、user-simulator 多轮（VISTA）。核心原则：

- **能力 / 合规两条独立轨**——930 合规是 hard gate（binary pass/fail），不是打分项
- **能用脚本查的绝不用 LLM 判**——去主观
- **看稳定性不看单次**——agent 有随机性
- **盲评独立**——跑的 agent 不评自己（防自评偏见）

## 四层

| 层 | 测什么 | 方法 |
|---|---|---|
| **L0 硬检查** | 930 红线词 / 来源标签 / 编造数字 / 状态写入 | 脚本 grep + 状态核对（execution-based，0 主观） |
| **L1 路由** | `/elab` 是否路由到正确 skill | 对 gold label + **pass³**（每题跑 3 次看稳不稳）+ 混淆矩阵 |
| **L2 行为质量** | 消解/引导有效性、术语正确、策略中立、无幻觉、真交付 | LLM-as-judge rubric（1-5 锚定）+ 争议题 pairwise vs vanilla |
| **L3 多轮施压** | 持续逼问下 930 守不守得住 | user-simulator 多轮升级施压（4-5 轮），逐轮判有无被逼出方向 |

## 执行

1. **Phase 1（并行）**：runner subagent 跑题（状态 skill 用独立 slug 隔离；辩论模式 inline 不嵌套）
2. **Phase 2（并行）**：L0 脚本 + L2/L3 盲评 judge（只给输出，不给 runner 推理）
3. **Phase 3**：综合记分卡 + 失败模式归纳

## 回归

改任何 skill（见 `../CHANGELOG.md`）后，重跑本套。测评题库 + gold 路由 + rubric 是回归集——把"一次性测"变"持续回归"。

> 测试题库含真实交易数据，私有保存，不入公开 repo。本文件只是方法论。
