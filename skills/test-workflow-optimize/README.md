# test-workflow-optimize

**自动化测试效率优化 / Automated test workflow optimization**

缩短日常开发获得可靠反馈的时间，同时保留完整验收的覆盖、业务语义和失败证据。

Shorten the time to reliable development feedback while preserving complete acceptance coverage, behavioral semantics, and failure evidence.

[中文](#中文) · [English](#english) · [Agent 指令 / Instructions](SKILL.md) · [技能集 / Collection](../../README.md)

## 中文

### 适用场景

- 全量测试有几千项，每次局部开发都要等待很久。
- 纯规则测试被公共 fixture 强制带入应用初始化、清库、密码哈希或引导数据重播。
- 并行测试相互干扰，事务优化改变了真实连接或提交语义。
- 测试选择缺少依据，日志难以定位失败，工具和文档难以持续维护。

技能提供诊断与实施方法，运行器由 agent 根据目标项目已有入口逐步增强。它适配不同语言与测试框架；来源实践的运行器和参数值是参考实现，本仓库提供可迁移的指令与设计资料。

### 核心设计

1. **先消除固定成本。** 分开测量排队、导入、setup、call 和 teardown；让纯测试不加载数据库与应用，让昂贵 fixture 按需执行。
2. **建立分层反馈。** 日常修改走快速或定向范围，完整验收保留全部约定覆盖。真实跨连接测试仍属于完整套件。
3. **让选择可解释。** 变更计划覆盖分支提交及工作区变化，沿依赖传播，登记隐式输入；未知或共享基础变化扩大范围。
4. **先隔离再并行。** 隔离数据库、文件与端口，管理共享机器预算；事务回滚仅用于语义兼容的测试。
5. **保留失败证据。** 区分收集、选中、执行、跳过、排除和错误。源码变化、空收集、超时与中断不能形成完整验收通过。
6. **便于持续扩展。** 统一本地与 CI 入口，使用版本明确、返回新对象的构造器，逐步迁移旧测试。

### 使用方式

安装方法见[仓库 README](../../README.md)。首次调用建议给出目标项目路径、项目约定，以及要探索还是要实施。

```text
请使用 $test-workflow-optimize，先读取项目开发约定和已有测试入口，诊断主要成本。
然后在隔离环境实施测试优化，保留业务断言与完整验收覆盖。
完成分层运行、影响选择、必要的资源治理、结果报告和维护文档，
用本项目实测确定并行数、超时与事务兼容范围。
```

只做探索可改为：“只诊断和提出建议，不修改项目或启动全量测试。”

建议按实际瓶颈交付：成本/语义地图、最小试点、可复用入口、必要的完整门及验收说明。不要为统一形式重写整个套件，也不要无新证据反复启动昂贵全量。涉及真实模型或外部付费服务时，按目标项目的授权流程处理。

### 匿名实测参考

以下为一个已有测试套件的已完成匿名样本，公开测试数量、耗时和验证条件。环境为本地 ARM64、pytest/xdist；数据库档使用每 worker 独立 PostgreSQL 实例，完整档未启用事务隔离。除特别注明外，耗时取 pytest 自身统计。

| 验证范围 | 实际结果 | 耗时 | 说明 |
| --- | --- | --- | --- |
| 快速档，串行 | 89 通过 | 5.95 秒 | 预检数据库连接为 0；应用与数据库 fixture 未加载 |
| 本实验室所有 PG 停机后的快速档 | 89 通过 | 6.22 秒 | 验证快速档不需要启动数据库 |
| 完整 Python，8 workers | 3772 通过、52 跳过 | 722.10 秒，约 12 分钟 | 3824 项均有执行报告；0 排除；8 worker 收集一致 |
| 真实跨连接档，8 workers | 112 通过 | 69.81 秒 | 本档另有 3712 项明确排除；这些用例在完整档中仍被覆盖 |
| 事务隔离定向样本，2 workers | 8 通过、2 排除 | 3.23 秒 | 两项跨连接 CAS 明确留给真实连接档验证 |
| 发布前 Web/静态检查 | 构建通过；Node 145 通过、1 失败；Ruff 通过 | 不与 Python 耗时合并比较 | 保留原失败结果 |

发布前整轮命令总耗时 **724.862 秒**，包装器总耗时 **725.122 秒**，排队/预检 **0.260 秒**。源码开始/结束指纹一致，没有收集、worker 或测试超时错误。52 项原跳过保留，并未计算为通过。

**发布前总结果为失败。** 唯一失败来自一项既有前端样式规则；相关产品代码和规则测试没有在测试优化中改动，也没有豁免失败。

这些数据说明快速反馈、完整覆盖、真实连接分类与结果留证已经落地；**没有同条件的前后全量对照，因此不宣称全量提速倍数，也不把 89 项快速档与 3824 项完整档相除作提速证明。** 8 workers、数据库实例数和时限均需在目标项目重新测量。

数据已与来源实践的验收记录、JSON 摘要和日志核对。此处只发布匿名汇总，原始记录不随技能公开；样本结果对应当时的固定版本，不代表后续版本或其他项目已通过验收。

### 文件导航与验证边界

| 文件 | 用途 |
| --- | --- |
| [SKILL.md](SKILL.md) | 触发范围、诊断、实施决策、约束与交付标准 |
| [design-principles.md](references/design-principles.md) | 各项设计背后的理由和迁移边界 |
| [implementation-guide.md](references/implementation-guide.md) | 选择器、fixture、隔离、资源治理、报告与验证细节 |
| [agents/openai.yaml](agents/openai.yaml) | Codex 技能列表元数据和调用示例 |

来源样本的实际运行结果与本 skill 的验证是两件事。本 skill 已做结构、引用、元数据检查及代表性场景复核；尚未在其他项目完成端到端迁移验收。扩展时记录目标项目自己的覆盖和耗时，不把来源数据写成预期保证。

## English

### When to use it

- A full suite contains thousands of cases and slows down every small development change.
- Shared fixtures force pure logic tests to initialize the application, clean databases, hash passwords, or replay bootstrap data.
- Parallel tests interfere with one another, or transaction optimizations alter real connection and commit semantics.
- Test selection lacks a clear rationale, failures are hard to locate, and tooling or documentation is difficult to maintain.

The skill provides a diagnostic and implementation method. The agent incrementally improves the target project's existing entrypoint for its language and runner. This repository provides transferable instructions and design references; runner implementations and parameter values from prior practice serve as examples.

### Core design

1. **Remove repeated fixed costs first.** Measure queueing, imports, setup, call, and teardown separately. Keep pure tests free of application/database initialization and load expensive fixtures only when needed.
2. **Provide layered feedback.** Use quick or targeted scopes during development and preserve the complete agreed scope for acceptance. Real cross-connection tests remain part of the full suite.
3. **Make selection explainable.** Include branch commits and working-tree changes, propagate dependencies, register implicit inputs, and expand coverage for unknown or shared foundational changes.
4. **Isolate before parallelizing.** Separate databases, files, and ports; manage shared machine capacity. Use rollback-based isolation only where semantics remain compatible.
5. **Keep failure evidence.** Distinguish collection, selection, execution, skips, deselection, and errors. Source changes, empty collection, timeouts, and interruptions cannot count as full acceptance success.
6. **Support continued growth.** Share one entrypoint between local development and CI, use explicit versioned builders that return fresh objects, and migrate existing tests incrementally.

### Usage

See the [repository README](../../README.md) for installation. Provide the target project, its conventions, and whether you want exploration or implementation.

```text
Use $test-workflow-optimize to read the project's conventions and existing test entrypoint,
diagnose the main costs, and implement improvements in an isolated environment.
Preserve behavioral assertions and full acceptance coverage. Deliver layered execution,
change-based selection, necessary resource management, reporting, and maintenance docs.
Determine concurrency, timeouts, and transaction compatibility from this project's measurements.
```

For exploration only, request diagnosis and recommendations without modifying the project or launching its full suite.

Deliver according to the observed bottlenecks: a cost/semantics map, a small pilot, a reusable entrypoint, the necessary complete checks, and acceptance notes. Preserve existing reliable components and avoid repeating expensive full runs without new evidence. Follow the target project's authorization process for real models or paid external services.

### Anonymized reference results

The following completed, anonymized sample reports test counts, timings, and validation conditions. It used a local ARM64 environment with pytest/xdist. Database profiles used independent PostgreSQL instances per worker; transaction isolation was disabled for the full suite. Durations are pytest-reported unless noted otherwise.

| Scope | Observed result | Duration | Notes |
| --- | --- | --- | --- |
| Serial quick profile | 89 passed | 5.95 s | Zero database connections in preflight; application/database fixtures were not loaded |
| Quick profile with all lab PG instances stopped | 89 passed | 6.22 s | Demonstrates that this profile does not require a running database |
| Full Python suite, 8 workers | 3,772 passed; 52 skipped | 722.10 s, about 12 minutes | All 3,824 items had execution reports; zero deselected; all 8 workers agreed on collection |
| Real cross-connection profile, 8 workers | 112 passed | 69.81 s | 3,712 other items explicitly deselected for this profile; full coverage remains in the full suite |
| Targeted transaction-isolation sample, 2 workers | 8 passed; 2 deselected | 3.23 s | Two cross-connection CAS cases remained assigned to real-connection validation |
| Release Web/static checks | Build passed; Node 145 passed and 1 failed; Ruff passed | Reported separately from Python timings | Original failure retained |

The release commands totaled **724.862 seconds**; the wrapper took **725.122 seconds**, including **0.260 seconds** of queueing/preflight. Start and end source fingerprints matched, with no collection errors, worker errors, or test timeouts. The original 52 skips remained skips rather than being counted as passes.

**Overall release status was failed.** The sole failure was a pre-existing front-end styling rule. Its related product code and rule test were unchanged by the testing optimization, and the failure was not exempted.

The records demonstrate implemented quick feedback, complete coverage, real-connection classification, and result evidence. **They do not establish a controlled before/after full-suite speedup. Comparing 89 quick cases against 3,824 full-suite items is not a valid speedup calculation.** Worker counts, database topology, and time limits must be measured again in each target project.

The figures were checked against the underlying acceptance records, JSON summaries, and logs. Only anonymized aggregates are published here; raw records are not included. Results refer to the fixed version used for that run, rather than acceptance of later versions or other projects.

### Files and validation limits

| File | Purpose |
| --- | --- |
| [SKILL.md](SKILL.md) | Trigger scope, diagnosis, implementation decisions, constraints, and delivery criteria |
| [design-principles.md](references/design-principles.md) | Design rationale and transfer limits |
| [implementation-guide.md](references/implementation-guide.md) | Selection, fixtures, isolation, resource management, reporting, and validation details |
| [agents/openai.yaml](agents/openai.yaml) | Codex discovery metadata and an invocation example |

The reference execution results and this skill's validation are distinct. The skill has received structural, link, and metadata checks plus a review of representative scenarios; it has not completed end-to-end adoption in another project. Record each target project's own coverage and timings rather than treating the reference numbers as guarantees. The core instructions and detailed references currently use Chinese, with English discovery metadata.
