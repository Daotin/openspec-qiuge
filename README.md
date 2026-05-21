# openspec-qiuge

这个仓库用于维护 OpenSpec 自定义 schema 和配套工作流。

## 内容

```text
superpowers-openspec/
  OpenSpec + Superpowers 复杂需求强流程 schema

log-promotion.md
  从 docs/logs/YYYYMMDD.md 中提炼 AI 执行经验，并沉淀到项目文档的独立维护流程
```

## superpowers-openspec

`superpowers-openspec` 适用于复杂需求，流程为：

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

核心约束：

- `design.md` 由 Superpowers brainstorming 产出，作为完整设计素材
- `proposal.md`、`specs/**/*.md`、`tasks.md` 由 OpenSpec 管理
- `plan.md` 由 Superpowers writing-plans 产出
- apply 只负责实现，不创建 worktree
- verify 与 git、PR 状态解耦
- 普通对话反馈只修正当前阶段产物，不自动进入下一阶段
- 仅在用户反馈、AI 修正、流程偏差时追加 `docs/logs/YYYYMMDD.md`

## log-promotion

`log-promotion.md` 是独立维护流程，用于定期读取项目里的：

```text
docs/logs/YYYYMMDD.md
```

并把可复用经验沉淀到：

```text
CLAUDE.md
openspec/config.yaml
README.md
schema
```

schema 只负责记录日志，`log-promotion` 由用户单独触发。

## 安装 schema

在目标项目根目录执行：

```bash
mkdir -p openspec/schemas
cp -R /path/to/openspec-qiuge/superpowers-openspec openspec/schemas/superpowers-openspec
openspec schema validate superpowers-openspec
```

## 使用 schema

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

## 维护建议

- schema 改动后运行 `openspec schema validate superpowers-openspec`
- 日志沉淀流程改动后同步检查 `superpowers-openspec/templates/log-entry.md`
- 小需求继续使用直接 Agent、Superpowers only 或 OpenSpec only
