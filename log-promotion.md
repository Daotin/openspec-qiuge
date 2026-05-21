# Log Promotion Workflow

这个流程用于从项目内 `docs/logs/YYYYMMDD.md` 提炼 AI 执行经验，并沉淀到项目文档。它独立于 `superpowers-openspec` schema，由用户单独触发。

## 目标

把一次性日志里的可复用经验沉淀为长期规则，减少用户重复提醒 AI。

## 输入

默认读取当天日志：

```text
docs/logs/YYYYMMDD.md
```

也可以由用户指定日期或文件。

## 处理范围

只处理日志条目中的三类事件：

- `user-feedback`
- `ai-correction`
- `workflow-deviation`

优先处理 `Promotion Target` 中未完成的目标。

已包含 `### Promotion Result` 的日志条目视为已处理，直接跳过，不重复判断、不重复写入目标文件。

## 输出目标

按日志条目的 `Promotion Target` 更新对应文件：

| Target | 写入内容 |
|---|---|
| `CLAUDE.md` | AI 行为规则、项目偏好、容易重复犯的错误 |
| `openspec/config.yaml` | OpenSpec schema、流程或配置规则 |
| `README.md` | 给开发者看的项目说明、安装、使用和维护信息 |
| `schema` | schema instruction、模板、日志规则、阶段门槛 |
| `skip` | 不沉淀，只保留日志记录 |

## 执行步骤

1. 读取指定日志文件。
2. 找出包含 `Promotion Target` 且不包含 `### Promotion Result` 的条目。
3. 对每条日志判断是否具备可复用价值。
4. 将规则写入对应目标文件。
5. 回写日志条目，记录 promotion 结果。
6. 输出本次处理摘要。

## 可复用价值判断

满足任意一条，通常应沉淀：

- 用户明确指出 AI 重复犯的错误
- 用户提出以后都应遵守的流程规则
- AI 修改了 schema、模板、README 或配置
- 某个流程偏差未来可能再次发生
- 某个项目约束需要长期保留

以下情况通常标记为 `skip`：

- 只和一次性上下文有关
- 已经由当前代码或模板自然保证
- 用户反馈只针对当次措辞
- 没有清晰的复用触发条件

## 回写格式

在原日志条目末尾追加：

```markdown
### Promotion Result

- Status: promoted | skipped
- Updated: <CLAUDE.md / openspec/config.yaml / README.md / schema / none>
- Reason: <why>
- Date: YYYY-MM-DD
```

## 执行约束

- 只更新日志条目明确指向的目标文件。
- 不做无关重构。
- 不自动格式化无关文件。
- 写入规则要简短、可执行、带触发条件。
- 多条日志指向同一规则时合并成一条规则。
- 已包含 `### Promotion Result` 的日志条目不重复处理。

## 推荐提示词

```text
请执行 log-promotion。

输入日志：
docs/logs/YYYYMMDD.md

要求：
1. 读取所有不包含 `### Promotion Result` 的 user-feedback / ai-correction / workflow-deviation 条目
2. 判断是否有可复用价值
3. 按 Promotion Target 更新对应文件
4. 在日志条目末尾追加 Promotion Result
5. 最后输出本次沉淀了哪些规则、跳过了哪些条目
```
