# Confirm Before Compaction Memory

一个兼容 Codex 和 Claude Code 的记忆压缩确认 Skill。

它的目标很简单：在上下文即将被压缩时，先把可能被丢弃的内容列出来，让用户逐项选择“保留”或“移除”，避免重要目标、约束和决策被摘要过程悄悄丢掉。

## 它会做什么

- 识别并保留当前目标、用户要求、硬约束、已确认决策和未完成事项。
- 把重复说明、过时方案、已完成的中间步骤等列为候选项。
- 优先使用“保留 / 移除”的选项式确认；不支持控件时使用编号选项。
- 默认保留。用户没有明确选择移除的内容，不会被主动丢弃。
- 用户回复“继续”等模糊指令时，按“全部保留”处理。

## Codex 使用方式

将本目录安装到 Codex 的 Skills 目录后，可以直接调用：

```text
$confirm-before-compaction-memory
```

也可以在长任务开始时说明：

```text
上下文压缩前，请先使用 confirm-before-compaction-memory，逐项让我确认哪些记忆可以移除。
```

## 安装

将整个目录复制到 Codex 的 Skills 目录，目录结构应保持为：

```text
confirm-before-compaction-memory/
├── SKILL.md
└── agents/
    └── openai.yaml
```

如果使用 Git：

```bash
git clone https://github.com/susie-jpg/confirm-before-compaction-memory.git
```

然后将仓库目录放入你的 Codex Skills 目录中。

## Claude Code 使用方式

Claude Code 的项目级 Skill 需要放在 `.claude/skills` 下。本仓库已经提供兼容目录：

```text
.claude/
└── skills/
    └── confirm-before-compaction-memory/
        └── SKILL.md
```

在项目目录中执行：

```bash
git clone https://github.com/susie-jpg/confirm-before-compaction-memory.git
mkdir -p .claude/skills/confirm-before-compaction-memory
cp confirm-before-compaction-memory/.claude/skills/confirm-before-compaction-memory/SKILL.md \\
  .claude/skills/confirm-before-compaction-memory/SKILL.md
```

然后在 Claude Code 中直接说明：

```text
上下文压缩前，请使用 confirm-before-compaction-memory，先逐项让我确认哪些记忆可以移除。
```

如果 Claude Code 已识别该 Skill，也可以通过技能选择入口调用它。

## 一个确认示例

```text
候选记忆：

C1：之前失败的搜索过程
    [保留] [移除]

C2：已经被新方案替代的实现讨论
    [保留] [移除]

C3：当前任务的验收标准
    [保留] [移除]

默认选择：全部保留
```

验收标准、用户明确要求和仍未完成的工作通常应保留；只有用户明确选择移除的候选项才会进入压缩后的工作记忆。

## 限制

这是一个 agent 工作流 Skill，不是宿主系统的底层压缩钩子。如果宿主在没有给 agent 留出确认机会的情况下直接启动隐藏式压缩，本 Skill 无法拦截它，也不会声称已经拦截成功。

因此，长任务最好在阶段性节点主动生成一次记忆检查点，并把真正重要的状态保存到项目文件或其他持久化存储中。

## License

MIT
