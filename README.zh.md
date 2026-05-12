# anti-entropy-docs · 反熵文档架构

> 一套针对**长周期 + AI 协同**项目的文档架构,设计目标是防止那种慢性的熵累积 — 这种累积会把"文档齐全"的项目变成不可读的散文坟场.

中文 | [English](README.md)

---

## 这套架构解决什么问题

你跟 AI 协同启动了一个为期几个月的研究/工程项目. 你认真记录一切. 6 个月后, 你有 **150 份 markdown 文件**, 状态更新散布在 6 个位置, 每次决策反转都留下一个 `SUPERSEDED` 横幅, 然后**你不再信任自己的文档**, 因为你说不清哪个版本是最新的.

这是一个有特定根因的模式:

> **状态更新以 prose 形式累积在多个文件里**, 而不是只写一次到单一来源.

本仓库规范一套防止这种累积的架构. 它**不算全新发明** — 它借鉴了 event sourcing (软件)、state/spec 分离 (系统工程)、append-only logs (数据库). **新的部分是把这些模式针对性应用到 AI 协同文档**, 配合显式的防膨胀模式 + 真实的失败→成功 case study.

---

## 5 分钟概览

架构有 **3 条反熵机制** + **5 类内容**.

### 3 条机制

| 机制 | 规则 |
|---|---|
| **单源 (Single Source)** | 每类内容只有 1 个落点. 其他文件用指针引用, 不复述 prose. |
| **单向 (One-Way)** | 跨文件引用单向: 下游 read 上游, 下游不回写上游 prose. |
| **单写 (Append-Only)** | 事件 / 决策 / 反转不修改过去条目. 修正走 git revert + 新事件. |

### 5 类内容

| 内容 | 落点 | 格式 |
|---|---|---|
| 状态 / 决策 / 事件 / 反转 | `<instance>/STATE.md` (append 一行) | MD + YAML frontmatter |
| Task spec (input/steps/output/判据/风险) | `<instance>/docs/TASKS.md` | MD |
| 完成报告 + 数据 trace | `<instance>/reports/T<N>_report.md` (写一次, 之后只读) | MD |
| 策略 / 红线 / 已锁决策 / DAG | `docs/ROADMAP.md` | MD |
| 跨项目知识 (工具坑 / 计算环境 quirk) | `~/.claude/projects/.../memory/` | MD |

**没有任何内容应该同时存在于两处**. 如果两个文件描述同一状态, 其中一个已经过期.

---

## 快速判断: 适不适合用?

| 项目特征 | 用本架构 |
|---|---|
| 多 session / 跨月份 / AI 主动协同 / 多 sub-task / 决策可能反转 | ✅ 适合 |
| 单 session / 单 task / 简单流程 / 纯人工写 | ❌ 过度 — 简单 README + git log 已足够 |

---

## 仓库内容

| 文件 | 是什么 |
|---|---|
| [`README.md`](README.md) | 英文版 pitch (主版) |
| [`README.zh.md`](README.zh.md) | 本文件 |
| [`SPEC.md`](SPEC.md) | 完整规范: 3 机制 / 5 落点 / 7 原则 / 文件布局 |
| [`HOWTO.md`](HOWTO.md) | 操作手册: lookup 表 / 3 日常场景 / 新项目+迁移 checklist |
| [`HOWTO.zh.md`](HOWTO.zh.md) | 操作手册中文版 |
| [`PATTERNS.md`](PATTERNS.md) | 防膨胀模式 + 3 个失败模式 |
| [`case-studies/`](case-studies/) | 2 个匿名化案例 — 1 失败 (文档膨胀) + 1 成功 (state-sourced) |
| [`templates/`](templates/) | 复制即用的骨架文件 (CLAUDE.md / STATE.md / TASKS.md 等) |
| [`examples/minimal-research-project/`](examples/minimal-research-project/) | 一个可 fork 的最小示例, 看架构实际怎么落地 |

---

## 30 分钟起步

```bash
# 1. 复制 templates 进新项目
cp -r anti-entropy-docs/templates/* my-new-project/

# 2. 打开 templates 文件, 填占位符 (搜索 <PROJECT_NAME>, <INSTANCE_NAME> 等)
# 3. commit
cd my-new-project && git init && git add -A && git commit -m "Bootstrap with anti-entropy-docs"
```

详细步骤 → [`HOWTO.zh.md` §新项目起步](HOWTO.zh.md).

---

## 一句话哲学

> **每个事件只写一次; 一次书写只落在一个地方; 一个地方只承载一类内容.**

如果其他全忘了, 记住这一句. 仓库里所有规则都是从这里派生的.

---

## 状态

版本 0.1 (首次公开发布). 来自两个真实研究项目的演化 (一个失败, 一个成功 — 见 [`case-studies/`](case-studies/)). 架构**已稳定可用**, 文档会根据用户反馈迭代.

## 贡献

欢迎 issue / discussion. PR 尤其欢迎:
- 更多 case study (匿名化的失败或成功故事)
- 翻译 (其他语言)
- 工具脚本 (审计 / 模板生成器) — 故意保持最少, 建议先提案再做

## License

MIT — 见 [LICENSE](LICENSE). Templates + 方法论可自由使用于任何项目 (商业或非商业), 无需署名 (但欢迎).
