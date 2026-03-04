# Discord Dynasty

OpenClaw skill：明朝六部式 Discord 管理——司礼监+六部频道模板、一帖一任务、上朝/上奏/回传。需配合 [Discord skill](https://clawhub.ai/steipete/discord) 与 `channels.discord` 配置使用。架构借鉴自 [Ai Court](https://clawhub.ai/wanikua/ai-court)。

**ClawHub 发布单元**：本仓库的 `discord-dynasty/` 目录为 skill 内容，以 **Discord Dynasty** 发布到 ClawHub。

## 安装

从 ClawHub 安装（推荐）：

```bash
clawhub install discord-dynasty
```

或从本仓库复制 `discord-dynasty` 目录到 OpenClaw 工作区的 `skills/discord-dynasty/`。

## 依赖

- OpenClaw 已配置 **Discord**（`channels.discord` 含 token 等）
- 建议同时安装 Discord skill：`clawhub install steipete/discord`

若采用明朝六部式多 Agent 协作（司礼监 + 六部），请参阅 [六部框架手册.md](六部框架手册.md)；OpenClaw 与 Discord 配置以 [OpenClaw配置与Discord参考.md](OpenClaw配置与Discord参考.md) 为准。

## 功能

- **一帖 = 一任务 = 一 session**：每个论坛帖子对应一个独立任务和会话
- **新建任务**：用户说「新建任务」「开个任务：xxx」时，在论坛发帖并打上状态/属性/模型标签
- **归档任务**：用户说「归档这个任务」时，给当前帖打上「归档」标签
- **模型标签**：帖子上的模型标签决定该帖对话使用的模型（由后端按标签切换）
- **六部管理频道模板**（可选）：用户说「建六部管理频道」「按六部模板创建」时，按司礼监 + 吏户礼兵刑工结构创建整站类别与频道；详见 `discord-dynasty/reference.md` 第 10 节

## 文件说明

- **OpenClaw配置与Discord参考.md** — 从 [docs.openclaw.ai](https://docs.openclaw.ai) 汇总的 openclaw.json 与 Discord 通道配置参考（配置项、示例、故障排查）
- **六部框架手册.md** — 司礼监 + 六部多 Agent 协作的流程、配置示例与快速开始
- **discord-dynasty/** — skill 目录（ClawHub 发布此目录）
  - **SKILL.md** — 给 OpenClaw agent 的指令（何时触发、如何响应）
  - **reference.md** — Discord API 与实现参考；六部模板与多 Agent 协议见第 10、11 节
  - **templates/** — 明朝六部式频道模板（`six-ministries.json`、`six-ministries-minimal.json`）
  - **clawhub.json** — ClawHub 展示用元数据

## 发布到 ClawHub

已发布为 **Discord Dynasty**（slug: `discord-dynasty`）。更新后发布新版本：

```bash
# 1. 在 discord-dynasty/clawhub.json 中提升 version（如 1.2.0 → 1.2.1）
# 2. 登录（若未登录）
clawhub login

# 3. 在项目根目录执行
clawhub publish ./discord-dynasty --slug discord-dynasty --name "Discord Dynasty" --version <新版本号> --tags latest --changelog "简短更新说明"
```

用户安装：`clawhub install discord-dynasty`

## License

MIT
