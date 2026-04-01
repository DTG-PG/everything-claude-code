# QA Codex Baseline

这是一套面向测试负责人和小规模 QA 团队的 Codex 基线示例。

适用场景：

- 互联网工具型产品
- 产品覆盖 Android、iOS、Windows、macOS、Web
- 同一产品家族拆成多个隔离仓库，而不是单仓多端
- 前端仓库和后端仓库彼此独立，分别提测和发布
- 产品数量持续增加，但测试人数基本不变
- 测试团队拥有前后端源码读取权限
- 希望把 Codex 用在提测分诊、回归缩圈、自动化补点、发版判断

## 设计目标

这套 baseline 的重点不是替代人工测试，而是帮助测试团队把有限人力集中到高价值环节：

- 提测前先用源码和 diff 做变更影响分析
- 把回归范围从“全量测试”缩到“按风险分层”
- 优先建设可复用的冒烟自动化，而不是追求一次性大而全
- 在发版前给出结构化的“可发布 / 有条件发布 / 阻塞发布”判断

## 仓库边界原则

这套 baseline 默认假设：

- 当前打开的源码仓库，就是当前分析和回归的直接边界
- 如果当前仓库是 Android 仓库，就先只分析 Android
- 如果当前仓库是 Windows 或 macOS 仓库，就先只分析桌面端
- 如果当前仓库是后端仓库，就先只分析当前服务、当前接口和当前数据层
- 不要因为产品家族同时存在其他端，就默认把它们都列为必测

只有在以下情况，才扩展到“兄弟仓库 follow-up 检查”：

- 明确使用了共享内核、共享协议、共享后端接口
- 当前需求或任务单明确要求多端联测
- 本次改动涉及公共格式、公共文件结构、公共会员规则、公共接口契约
- 已有证据表明兄弟端会被联动影响

## 建议复制到业务项目的内容

把下面这些内容复制到你的业务项目根目录：

```text
AGENTS.md
.codex/
.agents/skills/test-impact-analysis/
.agents/skills/backend-contract-regression/
.agents/skills/release-readiness/
.agents/skills/cross-platform-regression/
PROMPTS.md
```

如果业务项目还没有测试相关 skill，也建议一并复制这些已有 skill：

- `e2e-testing`
- `verification-loop`
- `eval-harness`
- `security-review`
- `documentation-lookup`
- `backend-patterns`
- `api-design`

它们来自本仓库现有的 ECC 技能集，可以和这套 QA baseline 配合使用。

## 推荐落地方式

1. 保留当前 ECC 仓库作为上游参考仓库，不要直接改成你们业务项目的配置仓库。
2. 单独建立一个内部 `qa-codex-baseline` 目录或仓库，放你们确认过的版本。
3. 从内部 baseline 向各业务项目复制，不同项目只做少量本地化调整。
4. 每次提测都让 Codex 先读源码、读 diff、输出影响分析，再进入人工验证。

## 你们团队的推荐工作流

### 1. 提测入口

开发提测时，要求至少提供：

- 需求摘要
- 代码分支或 PR
- 影响平台
- 自测结论
- 已知限制和风险

然后让 Codex 做：

- 变更影响分析
- 当前仓库最小回归矩阵
- 如果是后端仓库，补一份接口契约和调用方影响分析
- 是否需要补自动化

### 2. 测试执行

人工测试优先做：

- P0 主链路
- 平台差异项
- 容错和异常链路
- 体验类问题
- 高频历史故障点

如果当前提测的是后端仓库，人工或半自动验证优先做：

- 核心接口可用性
- 旧客户端兼容性
- 数据一致性
- 权限与鉴权
- 异步任务链路
- 配置和回滚风险

Codex 优先承担：

- 读源码和 diff
- 生成回归清单
- 生成测试数据和边界场景
- 补 web / API / 桌面端自动化脚手架
- 识别接口契约、数据库、任务队列、缓存、配置变更
- 输出受影响调用方和兄弟仓库 follow-up
- 汇总风险和发版判断

### 3. 发版前

让 Codex 基于源码、缺陷状态、构建结果、自动化结果输出结构化结论：

- `可发布`
- `有条件发布`
- `阻塞发布`

不要只给“能不能发”的口头判断，要保留证据链。

## 推荐优先级

如果你们现在只有 2 个测试，优先做这 3 件事：

1. 把每次提测都变成标准化输入
2. 把变更影响分析固化成固定 prompt
3. 把“当前仓库回归矩阵 + 后端契约影响 + 兄弟仓库 follow-up 判断”沉淀成可复用模板

## 文件说明

- `USAGE.md`  
  适合直接发给团队的整体使用文档
- `AGENTS.md`  
  业务项目级测试负责人指令
- `PROMPTS.md`  
  可直接在 Codex 里复用的 prompt 模板
- `.codex/config.toml`  
  适合 QA 工作流的 Codex 配置示例
- `.agents/skills/test-impact-analysis/SKILL.md`  
  提测影响分析 skill
- `.agents/skills/backend-contract-regression/SKILL.md`  
  后端仓库接口、数据层、异步任务回归 skill
- `.agents/skills/release-readiness/SKILL.md`  
  发版判断 skill
- `.agents/skills/cross-platform-regression/SKILL.md`  
  仓库内回归矩阵与兄弟平台 follow-up 判断 skill
