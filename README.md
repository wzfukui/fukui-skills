# fukui-skills

从真实项目中提炼的个人 agent 技能集。每个技能提供明确的适用范围、决策依据和交付标准，帮助其他项目复用经验，同时适应自己的技术栈。

## 技能

| 名称 | 用途 | 入口 |
| --- | --- | --- |
| `test-workflow-optimize` | 诊断已有测试成本，优化分层反馈、影响选择、环境隔离、并行资源、报告与维护约定 | [SKILL.md](skills/test-workflow-optimize/SKILL.md) |

## 测试优化的核心思想

缩短日常开发获得可靠反馈的时间，同时保留完整验收的覆盖和证据。

- 先测量导入、fixture、执行与排队成本，优先减少每个测试重复承担的固定成本。
- 纯规则走快速档；真实数据库、锁和跨连接语义继续使用对应环境验证。
- 按可解释的变更影响选测试，不确定时扩大覆盖；完整门保留全部约定范围。
- 隔离资源之后再并行，并行数和事务兼容性由目标项目实测。
- 记录选中与实际执行数量、逐阶段失败、超时和中断，避免假成功。
- 统一本地与 CI 入口，使用版本明确的构造器和渐进迁移，便于后续扩展。

详细理由见[设计思想](skills/test-workflow-optimize/references/design-principles.md)，技术实施见[实施指南](skills/test-workflow-optimize/references/implementation-guide.md)。本技能不预置目标项目的并行数、数据库路径或超时，不复制来源项目的生产凭据、业务代码和任务材料。

## 安装与调用

将本仓库克隆到个人代码目录，例如：

```bash
git clone https://github.com/wzfukui/fukui-skills.git
```

Codex 用户可将 `skills/test-workflow-optimize/` 整个目录复制到自己的技能目录（通常为 `~/.codex/skills/`）。若已有同名技能，先比较版本再更新。仓库根目录不是技能目录；不要将 `.git` 或仓库全部文件复制进技能目录。

安装后，在新的会话中使用：

```text
请使用 $test-workflow-optimize，先诊断本项目已有测试的成本，再实施测试效率优化。
请保留业务断言和完整验收覆盖，完成分层运行、环境隔离、结果报告及维护文档。
具体框架、并行数和时限请根据本项目实测确定，不照搬其他项目。
```

尚未安装、或使用其他支持文件指令的 agent 时，可以要求其先读取 `skills/test-workflow-optimize/SKILL.md`，再按需读取相对引用文件。这样使用不依赖 Codex 专属 UI 元数据。

若只希望探索，在请求中说明“只诊断和提出建议，不修改项目或启动全量测试”。技能不会把探索请求当成实施授权。

## 目录与维护

```text
skills/
└── test-workflow-optimize/
    ├── SKILL.md
    ├── agents/openai.yaml
    └── references/
        ├── design-principles.md
        └── implementation-guide.md
```

新技能以独立目录添加，入口保持简洁；较长的原理和技术细节放入按需阅读的 references。更新时核对触发范围、相对链接、frontmatter、UI 元数据和现实使用场景，不能只验证文档格式。实际跨项目应用结果应明确记录，格式检查不代表所有项目实测通过。

目录组织参考 [lijigang/ljg-skills](https://github.com/lijigang/ljg-skills)。测试优化内容由本次项目经验独立提炼，当前使用单一 Markdown 版本。
