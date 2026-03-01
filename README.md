# discord-task-skill

OpenClaw skill：将 Discord 论坛作为任务中心（看板 + 协作），支持一帖一任务、标签管理（状态/属性/模型/归档）、新建任务与归档。

## 内容

- **`.cursor/skills/discord-task-center/`** — 供 OpenClaw 使用的 skill：
  - `SKILL.md`：Agent 指令（何时新建/归档任务、模型标签含义、与 ClawHub Discord skill 配合）
  - `reference.md`：Discord API 与实现参考、与官方 discord skill 结合说明

## 使用

- **在 OpenClaw 中**：将 `discord-task-center` 目录拷到工作区 `skills/discord-task-center/`，并与 [discord skill](https://clawhub.ai/steipete/discord) 一起使用。
- **在 Cursor 中**：保持 `.cursor/skills/discord-task-center/` 即可被 Cursor 加载。

## 安装 Discord 依赖（OpenClaw）

```bash
clawhub install steipete/discord
# 或 openclaw add @steipete/discord
```

配置 `channels.discord`（token、enabled 等）后即可在任务论坛内使用本 skill。

## License

MIT
