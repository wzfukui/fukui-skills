# fukui-skills

从真实实践中提炼的个人 agent 技能集，让经验能被其他项目复用，也能随着后续实践持续改进。

A personal collection of agent skills distilled from practical work, designed for reuse and improvement through further experience.

[中文](#中文) · [English](#english) · [技能导航 / Skill directory](#技能导航--skill-directory)

## 技能导航 / Skill directory

| 分类 / Category | 技能 / Skill | 用途 / Purpose | 文档 / Documentation |
| --- | --- | --- | --- |
| 开发与质量 / Development & quality | `test-workflow-optimize` | 优化已有自动化测试的反馈速度与可维护性，保留完整验收覆盖。 / Improve feedback speed and maintainability while preserving full acceptance coverage. | [中英介绍 / Bilingual README](skills/test-workflow-optimize/README.md) · [Agent 指令 / Instructions](skills/test-workflow-optimize/SKILL.md) |

索引随新增技能逐步扩展。每个技能的 README 解释用途、使用方式和验证边界，`SKILL.md` 提供 agent 执行指令，较长的资料放入按需阅读的 `references/`。

This directory grows as skills are added. Each skill's README explains its purpose, usage, and validation limits; `SKILL.md` contains agent instructions; longer material lives in references loaded as needed.

## 中文

### 设计原则

技能提供可迁移的判断方法：明确适用场景，检查目标项目的现实条件，尊重用户授权和项目约定，再实施和验证。来源实践的技术栈、参数和失败个案不会自动成为其他项目的强制配置。

首个技能聚焦测试效率：缩短日常开发获得可靠反馈的时间，同时保留完整验收的覆盖和证据。匿名实测样本中，快速档为 **89 项通过 / 5.95 秒**，完整 Python 套件为 **3772 项通过、52 项跳过 / 722.10 秒**；发布前总检查仍保留一项既有前端规则失败。完整数据、条件和限制见[技能 README](skills/test-workflow-optimize/README.md#匿名实测参考)。这些结果不代表其他项目的性能保证。

### 获取与使用

在个人代码目录克隆仓库：

```bash
mkdir -p ~/code
git clone https://github.com/wzfukui/fukui-skills.git ~/code/fukui-skills
```

Codex 用户首次安装单个技能时，将对应的整个技能目录复制到个人技能目录；已有同名技能时先比较版本再更新：

```bash
mkdir -p ~/.codex/skills
cp -R ~/code/fukui-skills/skills/test-workflow-optimize ~/.codex/skills/
```

仓库根目录是技能集，每个 `skills/<name>/` 才是可独立使用的技能目录。其他支持文件指令的 agent 可直接读取该目录的 `SKILL.md` 及其相对引用。

在新会话中调用，例如：

```text
请使用 $test-workflow-optimize，先诊断本项目已有测试的成本，再实施测试效率优化。
保留业务断言和完整验收覆盖，完成分层运行、环境隔离、结果报告及维护文档。
框架、并行数和时限根据本项目实测确定。
```

只希望探索时，明确说明“只诊断和提出建议，不修改项目或启动全量测试”。技能的使用不会自动扩大操作授权。

### 目录与扩展

```text
fukui-skills/
├── README.md
├── AGENTS.md
└── skills/
    └── test-workflow-optimize/
        ├── README.md
        ├── SKILL.md
        ├── agents/openai.yaml
        └── references/
            ├── design-principles.md
            └── implementation-guide.md
```

未来新增技能时，在 `skills/` 下添加独立目录，并更新上方导航。入口保持简洁，资料按需引用；仅在有明确复用价值时添加脚本或模板。技能介绍采用中英同文件形式，方便阅读与分享。

维护前阅读 [AGENTS.md](AGENTS.md)。发布前校验 frontmatter、相对链接、UI 元数据与代表性使用场景；新增脚本应实际运行验证。实测采用匿名汇总，注明范围和限制，不将格式校验写成跨项目验收，不发布项目身份、业务材料、凭据或原始私有日志。

目录组织参考 [lijigang/ljg-skills](https://github.com/lijigang/ljg-skills)。本仓库的测试优化内容由实践独立提炼。

## English

### Design principles

A skill should provide transferable judgment: define when it applies, inspect the target project's conditions, respect user authorization and project conventions, then implement and validate. A source example's stack, parameter values, and individual failures do not become mandatory configuration elsewhere.

The first skill focuses on testing efficiency: faster reliable development feedback while preserving complete acceptance coverage and evidence. An anonymized reference sample reported **89 quick tests passed in 5.95 seconds** and **3,772 full-suite Python tests passed with 52 skipped in 722.10 seconds**. The overall release check retained one pre-existing front-end rule failure. See the [skill README](skills/test-workflow-optimize/README.md#anonymized-reference-results) for conditions and limitations. These results do not guarantee performance in other projects.

### Get and use the skills

Clone the repository into your personal code directory:

```bash
mkdir -p ~/code
git clone https://github.com/wzfukui/fukui-skills.git ~/code/fukui-skills
```

For a first-time Codex installation, copy the entire selected skill directory into your personal skills directory. Compare versions before updating an existing skill with the same name:

```bash
mkdir -p ~/.codex/skills
cp -R ~/code/fukui-skills/skills/test-workflow-optimize ~/.codex/skills/
```

The repository root is a collection; each `skills/<name>/` folder is an independently usable skill. Other agents that accept file-based instructions can read its `SKILL.md` and follow the relative references.

Invoke the skill in a new conversation, for example:

```text
Use $test-workflow-optimize to diagnose this project's test costs and implement improvements.
Preserve behavioral assertions and full acceptance coverage. Deliver layered execution,
environment isolation, result reporting, and maintenance documentation.
Determine the framework, concurrency, and time limits from this project's measurements.
```

For exploration only, explicitly request diagnosis and recommendations without modifying the project or launching its full suite. Using a skill does not expand operational authorization.

### Structure and growth

The tree above separates the collection index, repository maintenance instructions, and individual skills. Add future skills as independent folders under `skills/` and update the directory table. Keep entrypoints concise and load detailed references as needed; add scripts or templates when they provide concrete reusable value. Human-facing skill introductions use Chinese and English in one file.

Read [AGENTS.md](AGENTS.md) before maintenance. Before publication, validate frontmatter, relative links, UI metadata, and representative usage scenarios; actually execute new scripts to verify them. Publish anonymized aggregate measurements with explicit scope and limits. Format validation is not cross-project acceptance, and project identities, business materials, credentials, or raw private logs do not belong in this collection.

The directory organization is inspired by [lijigang/ljg-skills](https://github.com/lijigang/ljg-skills). The testing guidance was independently distilled from practice. Core agent instructions and detailed references currently use Chinese, with English discovery metadata.
