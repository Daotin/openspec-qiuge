# superpowers-openspec Schema

这是一个面向复杂需求的 OpenSpec 自定义 schema，用于把 OpenSpec 的规格生命周期和 Superpowers 的澄清、计划、执行纪律串起来。

## 适用场景

- 跨模块功能
- 架构调整
- API、权限、数据模型或业务规则变化
- 需要长期沉淀为 OpenSpec spec 的复杂需求
- 需要在实现前做明确需求澄清和详细执行计划的需求

小需求建议继续使用直接 Agent、Superpowers short plan 或 OpenSpec only。

## 流程

```text
design.md
→ proposal.md
→ specs/**/*.md
→ tasks.md
→ plan.md
→ apply
→ verify.md
→ archive
```

## 阶段推进规则

阶段推进只能由用户显式命令触发：

```text
/opsx:continue
/opsx:apply
/opsx:verify
/opsx:archive
```

普通对话反馈只修正当前阶段产物，不自动进入下一阶段。

示例：

```text
/opsx:continue feature-x   # 生成 tasks.md
```

用户反馈 `tasks.md` 太细时，AI 只修改 `tasks.md`，并按日志规则判断是否写入 `docs/logs/YYYYMMDD.md`。

只有用户再次调用：

```text
/opsx:continue feature-x
```

才进入 `plan.md` 阶段。

## 产物职责

| 产物 | 来源 | 作用 |
|---|---|---|
| `design.md` | Superpowers brainstorming | 用户确认后的完整设计素材 |
| `proposal.md` | OpenSpec | 变更目标、范围、能力和影响面 |
| `specs/**/*.md` | OpenSpec | 正式 delta spec |
| `tasks.md` | OpenSpec | 粗粒度交付清单 |
| `plan.md` | Superpowers writing-plans | 详细执行计划和执行模式 |
| `verify.md` | OpenSpec | 实现后验证报告 |
| `docs/logs/YYYYMMDD.md` | 条件追加 | 记录用户反馈、AI 修正、流程偏差 |

## 和 superpowers-bridge 的主要区别

| 项目 | superpowers-bridge | superpowers-openspec |
|---|---|---|
| 需求澄清产物 | `brainstorm.md` + `design.md` | 只保留 `design.md` |
| `design.md` 格式 | 固定结构 | 不强制格式，只要求信息完整 |
| `retrospective.md` | 默认生成 | 删除 |
| apply | worktree + subagent + verify + retrospective + archive + finishing | 只负责实现 |
| worktree | 强制创建 | 不创建 |
| 执行方式 | 强制 subagent | 用户在 `plan.md` 阶段选择 |
| verify | 包含 commit、worktree、PR 检查 | 只检查 OpenSpec、任务、规格、测试/构建结果 |
| archive | PR 前强制闭环 | verify 通过后 archive，与 git 无关 |
| 日志 | retrospective 承担复盘 | `docs/logs/YYYYMMDD.md` 条件追加 |
| 语言 | 英文和繁体中文混合 | 简体中文 |

## 日志规则

schema 只负责记录日志，不负责把日志沉淀进其他文档。

每个阶段结束前判断是否出现以下任一事件：

- `user-feedback`：用户指出 AI 的判断、实现、流程或产物有问题
- `ai-correction`：AI 根据反馈做了修正，且规则可复用
- `workflow-deviation`：schema 预期流程与实际执行不一致

若出现事件，append 到当天文件：

```text
docs/logs/YYYYMMDD.md
```

若没有事件，不写日志。

后续可以单独运行 log-promotion，让 AI 扫描 `docs/logs/YYYYMMDD.md`，把可复用规则补充到 `CLAUDE.md`、`openspec/config.yaml`、`README.md` 或 schema。

## apply 执行方式

`plan.md` 必须包含 Execution Mode。

```text
main-agent
subagent-driven-development
```

`main-agent`：

- 当前 Agent 按 `plan.md` 执行
- 每完成一个 task，运行对应验证命令
- 验证通过后同步勾选 `tasks.md`

`subagent-driven-development`：

- 使用 `superpowers:subagent-driven-development`
- 强制 TDD
- 强制 code review
- 每完成一个 task，同步勾选 `tasks.md`

两种方式都不创建 worktree。

## 安装

复制目录到项目的 OpenSpec schemas 下：

```bash
mkdir -p openspec/schemas
cp -R superpowers-openspec openspec/schemas/superpowers-openspec
openspec schema validate superpowers-openspec
```

## 使用

逐步流程：

```text
/opsx:new <change-name> --schema superpowers-openspec
/opsx:continue <change-name>
/opsx:continue <change-name>
/opsx:continue <change-name>
/opsx:continue <change-name>
/opsx:continue <change-name>
/opsx:apply <change-name>
/opsx:verify <change-name>
/opsx:archive <change-name>
```

阶段含义：

```text
/opsx:continue  # design.md
/opsx:continue  # proposal.md
/opsx:continue  # specs/**/*.md
/opsx:continue  # tasks.md
/opsx:continue  # plan.md
/opsx:apply     # 只负责实现
/opsx:verify    # verify.md
/opsx:archive   # 归档 OpenSpec change
```

## 版本

当前版本：`0.1.0`
