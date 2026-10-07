## Evidence calibration

这是一个通用 agent skill，用于查找、核验和综合大量证据。当人工无法逐项检查来源和 AI 转述时，它要求 agent 核验关键论断、抽查其余证据、补查决定性缺口，并提出有条件的判断。

它同时约束两种失败：把有限证据夸大成有效或适用，以及因存在不确定性而只交付“待核验”。适用于研究、技术调查、方案比较和其他依赖证据的任务，不限定学科。

## 安装与使用

克隆本仓库后，将 `evidence-calibration/` 目录安装到你的 agent 技能路径。同名目录已存在时，先核对版本，不直接覆盖。

| 环境 | 常见用户技能路径 |
| --- | --- |
| Codex | `~/.codex/skills/` |
| 共享 agent skills | `~/.agents/skills/` |
| Claude Code | `~/.claude/skills/` |
| OpenCode | `~/.config/opencode/skills/` |

这些是常见安装位置；实际发现机制以本机配置为准。目录最终应包含 `evidence-calibration/SKILL.md`。本 skill 不依赖特定搜索平台，也不要求自动调用其他 agent。

在支持命名 skill 的环境中调用 `$evidence-calibration`。也可以明确要求 agent 阅读安装目录中的 `SKILL.md`，再执行任务。

示例请求：

```text
使用 evidence-calibration 比较候选方案，找出支持与限制证据。
逐项核验会改变选择的论断，抽查其他材料，补查决定性缺口。
给出有条件的首选或区分测试，并将交付放在独立目录。
本轮只研究和推荐验证，不实施正式变更。
```

## 输入

Agent 从请求和已有材料中整理目标问题、当前任务条件、已有来源、允许的操作、决策后果、交付格式及预算。用户无需填写完整表格；只有影响结论或授权范围的选择才需要确认。

## 过程

1. 拆解论断，标出会改变主结论、候选排序或安全边界的关键内容。
2. 搜索支持、验证、限制和替代证据，记录实际读取范围并识别同源转述。
3. 逐项核验关键论断，分开记录原文支持、任务适用性和推断。
4. 分层抽查其他论断，发现问题后扩大核验并修改受影响的结论。
5. 对决定性缺口做实际补查，不以“待核验”结束。
6. 比较候选，明确推荐的是继续调查、测试、采用还是暂缓。
7. 分别检查夸大与过度保守，再交付判断、依据和下一步。

## 输出

每次任务新建独立交付目录。用户未指定位置时，使用工作区中的以下结构。

```text
deliveries/evidence-calibration/YYYYMMDD-HHMMSS-task-name/
  Task_Spec.yaml
  Decision_Brief.md
  Evidence_Ledger.json
  Search_and_Audit_Log.json
  sources/                    按需创建
  _work/                      按需创建
```

报告说明当前判断、首选与备选、成立条件、可能推翻判断的结果、具体下一步和核验覆盖。台账保存论断与来源的对应；日志保存搜索、抽查、修订和缺口处理。项目其他文件默认只读，前轮产物不覆盖。用户明确只需要对话回答时不保存文件。

## 文件说明

- [SKILL.md](evidence-calibration/SKILL.md) 定义输入、七步过程和交付要求。
- [audit-protocol.md](evidence-calibration/references/audit-protocol.md) 提供字段、状态、抽样规则和输出模板。
- [openai.yaml](evidence-calibration/agents/openai.yaml) 提供支持该元数据格式的环境所需的显示信息。

## 限制

Skill 指令不能保证消除幻觉，抽查通过也不代表未查材料全部正确。来源真实、转述忠实、效果成立和适用于当前任务，是需要分别判断的问题。尚未完成实测时可以推荐验证，但不能声称已经有效。

本仓库提供执行流程及核验情境，不声称已经测得降低错误的效果。重大或不可逆行动仍需要更充分的证据和相应授权。
