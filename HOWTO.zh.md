# HOWTO · 操作手册

> 这套架构日常怎么用. **why** 见 [SPEC.md](SPEC.md). 已知失败模式见 [PATTERNS.md](PATTERNS.md).

中文 | [English](HOWTO.md)

---

## 1. 适不适合用 (30 秒判断)

| 项目特征 | 用本架构 |
|---|---|
| 多 session / 跨月份 / AI 协同 / 多 sub-task / 决策可能反转 | ✅ 适合 |
| 单 session / 单 task / 简单流程 / 纯人工写 | ❌ 过度 — 简单 README + git log 已足够 |

---

## 2. 我有 X 该写哪 (lookup 表)

| 我有的东西 | 落点 |
|---|---|
| 状态变化 / 决策 / 反转 / 任务完成 / 事件 | `STATE.md` (append **一行**, 不写段落) |
| Task spec (input/steps/output/判据/风险/关键决策) | `docs/TASKS.md` §对应 task |
| 完成报告 + 数据 trace | `reports/T<N>_report.md` (写一次, 只读) |
| 策略 / 红线 / 已锁决策 / DAG | `docs/ROADMAP.md` |
| 工具 quirk / 跨项目知识 | `~/.claude/projects/.../memory/` 等持久记忆 |
| 纯 registry (资源路径 / 资产清单) | `docs/<name>.yaml` |

**默认状态: 没有第 6 类内容**. 出现真实信号才加新落点.

---

## 3. 三个日常场景 (覆盖 90% 使用)

### 场景 A · Session 起步 (1 分钟 orient)

1. 读 `STATE.md` frontmatter (5 行) → 知 `active_task` + `in_flight_jobs` + `next_action`
2. 扫最近 3 条事件日志 → 知 AI 上次做了什么
3. 不对劲 → push back; 对劲 → 让 AI 按 next_action 继续

### 场景 B · Task 完成 (AI 跑完一个 task)

1. **更新 `STATE.md` frontmatter**: `active_task` 切下一 task, `in_flight_jobs` 清空, `next_action` 更新
2. **`STATE.md` 事件日志 append 一行**:
   ```
   YYYY-MM-DD · task:T<N> milestone · <一句话 summary + key data + JID>. ref: reports/T<N>_report.md
   ```
3. **新建 `reports/T<N>_report.md`** 写完整数据 + 物理解读
4. **`git commit`** (一个逻辑变更, message 说 **why**)
5. **不要**去 `TASKS.md` 或旧 report 头部加 "update" 段

### 场景 C · 决策反转 (之前的选择被推翻)

1. **`STATE.md` 事件日志 append**:
   ```
   YYYY-MM-DD · decision_reversal · <X → Y>. reason: <证据>. affected: <subinstances/tasks>. ref: <前一事件 id 或文档>
   ```
2. **若改变策略** → `docs/ROADMAP.md §已锁定决策` 加/改 1 行 (罕见)
3. **若 spec 真变了** → `docs/TASKS.md` §对应 task 字段就地改
4. **不要**修改 `reports/` (数据 trace frozen)
5. **不要**在任何地方写 `SUPERSEDED` banner (事件日志就是 audit trail)

---

## 4. 新项目从零起步 (~30 分钟 checklist)

```
[ ] mkdir <project> && cd <project> && git init
[ ] 把 templates/ 文件复制到项目根 + docs/ + instances/<active>/
[ ] 填占位符 (<PROJECT_NAME>, <ACTIVE_INSTANCE> 等)
[ ] CLAUDE.md          ≤30 行 — 5 类落点表 + Hard rules + Commit 策略
[ ] README.md          ≤30 行 — 顶层布局 + 入口表
[ ] docs/ROADMAP.md    策略 + 红线 + DAG 骨架
[ ] docs/CONVENTIONS.md  命名 + git workflow + frozen 边界
[ ] instances/<active>/STATE.md         YAML frontmatter + task graph + 事件日志骨架
[ ] instances/<active>/docs/TASKS.md    T1-TN spec only (不含 status)
[ ] instances/<active>/reports/         空目录, task 完成时填
[ ] (可选) instances/<frozen>/.aiignore  frozen 基线实例
[ ] git add -A && git commit -m "Bootstrap with anti-entropy-docs"
```

---

## 5. 老项目迁移 (1-2 小时)

如果你接手或恢复一个已经积累了 prose 状态的项目:

```
[ ] 新建 STATE.md. 从 TASKS.md / report 头部 / sub-instance prose 抽 status 字段 → frontmatter, 历史事件 → 事件日志
[ ] TASKS.md: 删 §当前状态 + 各 task §进行中状态 / §已踩坑 / §反转 narrative. 保留纯 spec
[ ] reports/T<N>_report.md: 头部 SUPERSEDED banner 堆叠收紧成 frontmatter
[ ] 顶层路由 docs (MD_INDEX 等) 归档到 docs/_archive/
[ ] 跨项目知识 (env quirk) 抽到 ~/.claude/projects/.../memory/
[ ] frozen 实例加 .aiignore
[ ] 验证: grep stale links + cross-reference 一致性 + 一个 commit 完成结构调整
```

---

## 6. 常见错误模式 (看到就停)

| 错误 | 正确 |
|---|---|
| Status prose 渗到 TASKS / reports / sub-instance 文档 | 所有 status → `STATE.md` |
| 反转加 `SUPERSEDED` banner 堆叠在 report 头部 | STATE 事件日志 append + report frontmatter 字段切换 |
| 列分类用 "词典 / enum" (视觉封闭集) | "示例 + 按需扩展" (避免 premature crystallization) |
| 在 CLAUDE.md 加新规则不标"特例"边界 | 特例规则末尾加 1 行 blockquote 说明 "本节为 X 特例 (理由 Y); 同类规则放 CONVENTIONS 不在此" |
| 同一信息在多文件 | 单源 + 跨文件指针引用 |
| 在 prose 改历史 (加 "correction" 段) | `git revert` + 新事件 |

---

## 7. 一句话哲学

> **每个事件只写一次; 一次书写只落在一个地方; 一个地方只承载一类内容.**

不理解具体情境? 回这一句 + [SPEC.md](SPEC.md) §3 机制. 架构是**派生的** — 任何规则都该能追溯到这 3 条.

---

**实践路径**: 用你项目里一个真实事件走一遍场景 A / B / C. 第一次会感觉别扭 (你会想往 TASKS 里加 prose). 重复 3-5 次后变肌肉记忆. 这套架构设计成**一旦内化就比传统做法更省力** — 不是更难.
