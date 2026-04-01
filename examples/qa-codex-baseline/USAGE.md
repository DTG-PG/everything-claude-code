# QA Codex Baseline 使用文档

这是一份面向测试负责人和小规模 QA 团队的整体使用文档。

适合这样的团队场景：

- 产品以工具类为主，数量持续增加
- Android、iOS、PC、macOS、Web 并存，但通常不是一个仓库
- 前端仓库和后端仓库分开管理、分开提测、分开发版
- 测试人数有限，需要靠流程和自动化兜底，而不是靠堆人
- 测试团队拥有源码读取权限，可以直接基于 diff 和代码做判断

## 1. 这套基线到底解决什么问题

这套基线不是为了让 Codex 替代测试，而是让 Codex 成为测试负责人的“分析器”和“放大器”。

它主要帮助你们解决 4 个问题：

1. 提测越来越快，测试来不及逐个重新理解改动
2. 产品越来越多，但很多能力高度相似，人工重复太多
3. 前后端分仓后，客户端改动和后端改动的风险模型完全不同
4. 发版前容易只给口头判断，缺少结构化证据

这套基线的目标是把测试工作重心，从“重复执行大量低价值回归”，转到：

- 提测分诊
- 风险缩圈
- 自动化补点
- 发版判断

## 2. 核心原则

### 当前仓库优先

默认把当前打开的仓库视为当前分析边界。

- Android 仓库，先只分析 Android
- PC 仓库，先只分析 PC
- 后端仓库，先只分析当前服务、接口、数据层、异步链路

不要因为同一个产品家族还有其他平台仓库，就默认全部纳入本次必测。

### 证据优先

所有测试判断优先来自：

1. 当前 diff 或 PR
2. 相关源码
3. 测试和构建结果
4. 需求说明
5. 历史缺陷记录

如果某个结论主要来自推断，需要明确写出“这是推断”。

### 风险优先

优先级永远高于覆盖率幻觉。

优先保证：

- P0 主链路
- 最近改动直接影响区域
- 高复用公共能力
- 高历史缺陷密度区域
- 平台或仓库类型特有风险

### 自动化优先级明确

优先自动化：

- 高频提测且稳定的流程
- 多产品可复用的能力
- 人工重复成本高的链路
- 可通过源码稳定定位的接口或页面

不优先自动化：

- 一次性活动页
- 高频变化且不稳定的 UI 细节
- 强依赖主观体验判断的问题

## 3. 文件组成与作用

这套基线主要由以下文件组成：

- [README.md](/d:/PyCode/everything-claude-code/examples/qa-codex-baseline/README.md)
  适合快速浏览，说明这套 baseline 是什么
- [AGENTS.md](/d:/PyCode/everything-claude-code/examples/qa-codex-baseline/AGENTS.md)
  项目级行为准则，决定 Codex 默认如何思考
- [PROMPTS.md](/d:/PyCode/everything-claude-code/examples/qa-codex-baseline/PROMPTS.md)
  可直接复制的 prompt 模板
- [.codex/config.toml](/d:/PyCode/everything-claude-code/examples/qa-codex-baseline/.codex/config.toml)
  Codex 的项目配置基线
- [test-impact-analysis](/d:/PyCode/everything-claude-code/examples/qa-codex-baseline/.agents/skills/test-impact-analysis/SKILL.md)
  通用提测影响分析
- [backend-contract-regression](/d:/PyCode/everything-claude-code/examples/qa-codex-baseline/.agents/skills/backend-contract-regression/SKILL.md)
  后端仓库回归分析
- [release-readiness](/d:/PyCode/everything-claude-code/examples/qa-codex-baseline/.agents/skills/release-readiness/SKILL.md)
  发版判断
- [cross-platform-regression](/d:/PyCode/everything-claude-code/examples/qa-codex-baseline/.agents/skills/cross-platform-regression/SKILL.md)
  当前仓库回归矩阵与兄弟仓库 follow-up 判断

## 4. 如何复制到业务项目

建议把当前 ECC 仓库当“母仓库”，不要直接改造成业务项目专用仓库。

推荐做法：

1. 保留当前仓库不动，作为上游参考
2. 在公司内部建立自己的 `qa-codex-baseline`
3. 从内部 baseline 复制到每个业务项目

复制到业务项目的最小集：

```text
AGENTS.md
.codex/
.agents/skills/
PROMPTS.md
USAGE.md
```

当前 QA baseline 目录里已经内置了这些通用 skill，不需要再额外从母仓库单独复制：

- `e2e-testing`
- `verification-loop`
- `eval-harness`
- `security-review`
- `documentation-lookup`
- `backend-patterns`
- `api-design`

也就是说，后续最简单的做法不是逐个挑 skill 复制，而是直接复制当前 QA baseline 下的整个 `.agents/skills/` 目录。

如果你们后面要做更轻量的版本，再按业务项目实际情况删减即可。

复制完成后，在业务项目根目录运行：

```bash
codex
```

Codex 会自动读取项目里的 `AGENTS.md` 和 `.codex/` 配置。

## 5. 推荐的团队使用方式

### 提测前

开发提测时，至少要求输入这些信息：

- 需求摘要
- 分支或 PR
- 当前仓库类型
- 自测结论
- 已知限制
- 风险说明

然后让 Codex 做第一轮分析：

- 变更影响分析
- 当前仓库回归范围
- 是否需要兄弟仓库 follow-up
- 是否需要补自动化

### 测试执行中

测试不要一开始就全量铺开，而是按 Codex 的分析结果优先做：

- P0 冒烟
- 当前仓库直影响区域
- 高风险异常路径
- 历史高频缺陷点

### 发版前

让 Codex 统一输出结构化结论：

- `可发布`
- `有条件发布`
- `阻塞发布`

这个结论必须基于：

- 代码变更范围
- 测试结果
- 已知缺陷
- 自动化结果
- 兼容性和回滚风险

## 6. 按仓库类型使用

### A. 客户端仓库

适用于：

- Android
- iOS
- Windows
- macOS
- Web

常见关注点：

- 主链路是否可用
- 平台特有能力是否受影响
- 文件导入导出
- 权限
- 会员和额度
- 崩溃和性能基线

推荐优先使用的 prompt：

- 提测分诊
- 最小回归矩阵
- 自动化补点
- 发版判断
- 缺陷回归缩圈

### B. 后端仓库

适用于：

- API 服务
- 网关或 BFF
- 管理后台服务
- 任务调度和异步 worker
- 公共服务

常见关注点：

- 接口契约兼容性
- 请求和返回字段变化
- 鉴权和权限
- 数据库 schema 和迁移
- 缓存
- 消息队列和异步链路
- 配置、开关、灰度逻辑
- 错误码、日志、监控

推荐优先使用的 prompt：

- 提测分诊
- 后端仓库提测分诊
- 后端接口契约影响分析
- 数据库和异步任务风险分析
- 发版判断

### C. 共享内核或公共层仓库

适用于：

- 共享格式解析
- 共享文件处理内核
- 共享会员规则
- 公共协议定义

这类仓库最容易出现“当前仓库改了一点，但兄弟仓库全受影响”的情况。

此时重点不是全量铺开，而是做两层输出：

1. 当前仓库的直接回归范围
2. 兄弟仓库的 follow-up 建议和证据

## 7. 推荐输出标准

### 影响分析

建议固定输出这些字段：

- 当前仓库类型
- 改动摘要
- 当前仓库影响模块
- 当前仓库影响对象
- 兄弟仓库或调用方 follow-up 判断
- 风险等级
- 必测项
- 可延后项
- 建议补自动化项

### 回归矩阵

统一按这三层：

- `P0` 冒烟
- `P1` 重点回归
- `P2` 延后验证

### 发版判断

统一只允许这三种状态：

- `可发布`
- `有条件发布`
- `阻塞发布`

并同时列出：

- 阻塞项
- 已知风险
- 放行条件

## 8. 两个测试的推荐分工

如果你们团队现在只有 2 个测试，可以这样分工：

### 测试 A

更偏流程和自动化：

- 提测入口把关
- 让 Codex 做分诊和缩圈
- 跟进自动化补点
- 跟进接口契约和基础冒烟

### 测试 B

更偏风险和发版：

- 负责高风险人工验证
- 负责体验类和异常路径
- 负责缺陷回归缩圈
- 负责最终发版判断整理

两个测试都使用同一套输出口径和 prompt，不要各自形成两套方法。

## 9. 一周落地建议

### 第 1 步

先把 baseline 复制到一个真实业务项目，跑通：

- 启动 Codex
- 做一次提测分诊
- 做一次发版判断

### 第 2 步

选 1 个客户端仓库和 1 个后端仓库，分别沉淀：

- 一套固定 prompt
- 一套固定输出格式
- 一套固定提测入口信息

### 第 3 步

从历史提测里挑出最重复、最耗时的 3 条链路，作为第一批自动化补点目标。

## 10. 常见误区

- 误区 1：把当前仓库默认等于全平台
- 误区 2：把后端改动简单翻译成前端页面回归
- 误区 3：一上来就追求全量自动化
- 误区 4：发版只给口头结论，不保留证据
- 误区 5：所有提测都从零开始重新理解

## 11. 怎么判断这套基线是否真的起效

建议跟踪这几个指标：

- 每次提测从接手到完成分诊的耗时
- 平均每次提测人工回归条目数
- 自动化覆盖的高频链路数量
- 因缩圈不准导致的漏测次数
- 发版前结论的清晰度和复盘可追溯性

如果这些指标持续改善，说明这套基线在帮你们放大测试产能，而不是只增加文档负担。
