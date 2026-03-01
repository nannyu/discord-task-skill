# discord-task-skill

OpenClaw skill：将 Discord 论坛作为任务中心（看板 + 协作），支持一帖一任务、标签管理（状态/属性/模型/归档）、新建任务与归档。

## 发布版结构（适合 ClawHub）

Skill 的**发布单元**在仓库根目录的 **`discord-task-center/`** 下，可直接用于 ClawHub 上传：

```
discord-task-skill/
├── discord-task-center/     ← ClawHub 发布此目录
│   ├── SKILL.md
│   ├── reference.md
│   ├── clawhub.json
│   └── README.md
├── PUBLISH.md               ← 发布步骤
└── README.md
```

- **发布到 ClawHub**：在项目根目录执行  
  `clawhub publish discord-task-center --slug discord-task-center --name "Discord Task Center" --version 1.0.0 --tags latest`  
  详见 [PUBLISH.md](PUBLISH.md)。

- **在 OpenClaw 中使用**：安装后 skill 位于工作区 `skills/discord-task-center/`，需配合 Discord 与 `channels.discord` 配置；建议同时安装 `clawhub install steipete/discord`。

- **在 Cursor 中使用**：将 `discord-task-center` 目录复制到 `.cursor/skills/discord-task-center/`，Cursor 会从该路径加载。

## License

MIT
