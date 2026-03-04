# OpenClaw 配置与 Discord 参考文档

本文档汇总自 [docs.openclaw.ai](https://docs.openclaw.ai) 中关于 **openclaw.json** 配置以及 **Discord** 通道的官方说明，供本项目中集成或对接 OpenClaw Gateway 时查阅。

---

## 一、openclaw.json 概述

- **位置**：`~/.openclaw/openclaw.json`
- **格式**：JSON5（支持注释与尾逗号），普通 JSON 也可。
- **行为**：若文件不存在，OpenClaw 使用安全默认值；所有字段均为可选。
- **校验**：配置必须完整符合 schema，未知键、类型错误或无效值会导致 Gateway 拒绝启动；根级仅允许 `$schema` 作为例外。

常见使用场景：

- 调整会话、媒体、网络或 UI
- 设置模型、工具、沙箱或自动化（cron、hooks）
- 连接各通道并控制谁可以向机器人发消息

完整字段说明见：[Configuration Reference](https://docs.openclaw.ai/gateway/configuration-reference)。

---

## 二、配置的编辑方式

| 方式 | 说明 |
|------|------|
| **交互向导** | `openclaw onboard`（完整设置）、`openclaw configure`（配置向导） |
| **CLI 单键** | `openclaw config get/set/unset`，例如：`openclaw config set agents.defaults.workspace "~/.openclaw/workspace"` |
| **Control UI** | 打开 `http://127.0.0.1:18789`，使用 Config 标签（表单 + Raw JSON） |
| **直接编辑** | 编辑 `~/.openclaw/openclaw.json`，Gateway 会监听文件并自动应用（见下方热重载） |

校验失败时：

- 运行 `openclaw doctor --fix` 或 `openclaw doctor --yes` 尝试修复
- 仅诊断类命令可用（`openclaw doctor`、`openclaw logs`、`openclaw health`、`openclaw status`），Gateway 不会启动

---

## 三、配置热重载

Gateway 监听 `~/.openclaw/openclaw.json`，多数修改无需手动重启。

| 模式 | 行为 |
|------|------|
| `hybrid`（默认） | 安全变更即时热应用；需重启的变更会自动重启 |
| `hot` | 仅热应用安全变更，需重启时打日志警告，由你手动重启 |
| `restart` | 任何配置变更都触发重启 |
| `off` | 不监听文件，下次手动重启后生效 |

```json5
{
  gateway: {
    reload: { mode: "hybrid", debounceMs: 300 },
  },
}
```

**通常可热应用**：`channels.*`、`agents`、`session`、`messages`、`hooks`、`cron`、`tools`、`logging`、`bindings` 等。  
**需重启**：`gateway.*`（port、bind、auth、TLS 等）、`discovery`、`canvasHost`、`plugins`。

---

## 四、通用配置结构摘要

### 4.1 最小配置示例

```json5
{
  agents: { defaults: { workspace: "~/.openclaw/workspace" } },
  channels: { whatsapp: { allowFrom: ["+15555550123"] } },
}
```

### 4.2 通道通用策略（DM / 群组）

所有通道均支持：

- **DM 策略** `dmPolicy`：`pairing`（默认）| `allowlist` | `open` | `disabled`
- **群组策略** `groupPolicy`：`allowlist`（默认）| `open` | `disabled`

`open` 的 DM 需配合 `allowFrom: ["*"]`。

### 4.3 多文件拆分（$include）

```json5
{
  gateway: { port: 18789 },
  agents: { $include: "./agents.json5" },
  broadcast: { $include: ["./clients/a.json5", "./clients/b.json5"] },
}
```

- 路径相对当前文件解析；支持嵌套（最多 10 层）；数组多文件按顺序深度合并。

### 4.4 环境变量与占位符

- 可从 `~/.openclaw/.env` 或当前目录 `.env` 读取（不覆盖已有环境变量）。
- 在配置字符串中使用 `${VAR_NAME}` 引用环境变量；字面量用 `$${VAR}`。
- 支持 SecretRef（`source: "env" | "file" | "exec"`），见 [Secrets Management](https://docs.openclaw.ai/gateway/secrets)。

---

## 五、Discord 通道配置

### 5.1 状态与前置

- **状态**：支持 DM 与公会频道（官方 Discord Gateway）。
- **前置**：在 [Discord Developer Portal](https://discord.com/developers/applications) 创建应用与 Bot，并启用所需 Intents 与权限。

### 5.2 快速设置步骤

1. **创建应用与 Bot**  
   在 Developer Portal 新建应用，Bot 页创建 Bot，复制 **Bot Token**。

2. **启用 Privileged Intents**  
   - **Message Content Intent**（必选）  
   - **Server Members Intent**（推荐，用于角色白名单与名称解析）  
   - Presence Intent（可选）

3. **生成邀请链接**  
   OAuth2 URL Generator 勾选 `applications.commands`、`bot`，Bot 权限至少：Attach Files、Embed Links、Read Message History、Send Messages、View Channels（以及按需 Add Reactions 等）。用生成的 URL 将 Bot 加入你的服务器。

4. **开启开发者模式并收集 ID**  
   在 Discord 客户端：用户设置 → 高级 → 开启 Developer Mode。复制你的 **User ID** 和 **Server ID**。

5. **在 OpenClaw 中配置 Token（勿在聊天中发送）**

   ```bash
   openclaw config set channels.discord.token '"YOUR_BOT_TOKEN"' --json
   openclaw config set channels.discord.enabled true --json
   openclaw gateway
   ```

   或直接写配置文件：

   ```json5
   {
     channels: {
       discord: {
         enabled: true,
         token: "YOUR_BOT_TOKEN",
       },
     },
   }
   ```

   也可使用环境变量：`DISCORD_BOT_TOKEN`（仅对默认账号生效）；或使用 SecretRef。

6. **DM 配对**  
   启动 Gateway 后，在 Discord 里给 Bot 发 DM，会收到配对码。在其他已连接渠道（或 CLI）对 agent 说：「Approve this Discord pairing code: <code>」，或执行：

   ```bash
   openclaw pairing list discord
   openclaw pairing approve discord <CODE>
   ```

7. **（推荐）公会工作区**  
   仅允许指定服务器、可选仅指定频道与用户：

   ```json5
   {
     channels: {
       discord: {
         groupPolicy: "allowlist",
         guilds: {
           YOUR_SERVER_ID: {
             requireMention: true,
             users: ["YOUR_USER_ID"],
           },
         },
       },
     },
   }
   ```

   若希望私服里每条消息都回复（不需 @机器人），可设 `requireMention: false`。

### 5.3 Discord 配置项参考（channels.discord）

以下为 Discord 通道在 `openclaw.json` 中的主要字段（完整列表见 [Configuration Reference - Discord](https://docs.openclaw.ai/gateway/configuration-reference#discord)）。

```json5
{
  channels: {
    discord: {
      enabled: true,
      token: "your-bot-token",
      mediaMaxMb: 8,
      allowBots: false,
      dmPolicy: "pairing",
      allowFrom: ["1234567890", "123456789012345678"],
      groupPolicy: "allowlist",
      dm: {
        enabled: true,
        groupEnabled: false,
        groupChannels: ["openclaw-dm"],
      },
      guilds: {
        "123456789012345678": {
          slug: "friends-of-openclaw",
          requireMention: false,
          ignoreOtherMentions: true,
          reactionNotifications: "own",
          users: ["987654321098765432"],
          roles: ["123456789012345678"],
          channels: {
            general: { allow: true },
            help: {
              allow: true,
              requireMention: true,
              users: ["987654321098765432"],
              skills: ["docs"],
              systemPrompt: "Short answers only.",
            },
          },
        },
      },
      historyLimit: 20,
      textChunkLimit: 2000,
      chunkMode: "length",
      streaming: "off",
      maxLinesPerMessage: 17,
      replyToMode: "off",
      actions: {
        reactions: true,
        messages: true,
        threads: true,
        pins: true,
        search: true,
        memberInfo: true,
        roleInfo: true,
        channelInfo: true,
        voiceStatus: true,
        events: true,
        roles: false,
        moderation: false,
        presence: false,
      },
      ui: {
        components: { accentColor: "#5865F2" },
      },
      threadBindings: {
        enabled: true,
        idleHours: 24,
        maxAgeHours: 0,
        spawnSubagentSessions: false,
      },
      voice: {
        enabled: true,
        autoJoin: [
          { guildId: "123456789012345678", channelId: "234567890123456789" },
        ],
        daveEncryption: true,
        decryptionFailureTolerance: 24,
        tts: { provider: "openai", openai: { voice: "alloy" } },
      },
      retry: {
        attempts: 3,
        minDelayMs: 500,
        maxDelayMs: 30000,
        jitter: 0.1,
      },
    },
  },
}
```

要点摘要：

- **Token**：`channels.discord.token`，或 `DISCORD_BOT_TOKEN` 环境变量（默认账号）。
- **DM 策略**：`dmPolicy`：`disabled` | `open`（需 `allowFrom: ["*"]`）| `allowlist` | `pairing`（默认）。
- **公会策略**：`groupPolicy`：`disabled` | `allowlist` | `open`。存在 `channels.discord` 时安全默认为 `allowlist`。
- **公会/频道**：`guilds.<guildId>` 下可配置 `requireMention`、`ignoreOtherMentions`、`users`、`roles`、`channels.<name>`（allow、requireMention、users 等）。建议使用数字 ID，避免依赖名称匹配。
- **危险选项**：`dangerouslyAllowNameMatching: true` 仅作兼容用途，会重新启用可变名称/标签匹配。
- **流式预览**：`streaming`：`off` | `partial` | `block` | `progress`（progress 在 Discord 上映射为 partial）；旧键 `streamMode` 会被自动迁移。
- **回复标签**：`replyToMode`：`all` | `first` | `off`（默认）；`[[reply_to:]]`、`[[reply_to_current]]` 仍可在内容中使用。
- **线程绑定会话**：`threadBindings` 控制 `/focus`、`/unfocus`、`/agents`、`/session idle`、`/session max-age` 等；`spawnSubagentSessions: true` 时方可为 `sessions_spawn({ thread: true })` 自动创建/绑定线程。
- **语音**：`voice` 配置语音频道加入、自动加入、DAVE 加密与 TTS；Bot 需在目标语音频道具备 Connect + Speak 权限，使用 `/vc join|leave|status` 控制。
- **Exec 审批**：`execApprovals.target`：`dm` | `channel` | `both`；可配置 `approvers`、`enabled` 等。
- **代理**：`channels.discord.proxy` 可指定 HTTP(S) 代理用于 Gateway WebSocket 与启动时 REST。
- **Presence**：`status`、`activity`、`activityType`、`activityUrl`；`autoPresence` 可根据运行时健康状态更新状态。
- **多账号**：`channels.discord.accounts` 与 `channels.discord.defaultAccount`；命名账号不继承 `accounts.default.allowFrom`，但可继承顶层 `allowFrom`。

### 5.4 运行时与会话模型（Discord）

- Slash 命令在独立命令会话中执行（`agent::discord:slash:`），仍携带 `CommandTargetSessionKey` 到对话会话。
- 群组 DM 默认不启用（`dm.groupEnabled: false`）。
- 公会频道会话键为 `agent::discord:channel:`。
- 默认 `session.dmScope=main` 时，直连 DM 共享主会话；可通过 `session.dmScope: "per-channel-peer"` 等实现多用户隔离。
- 回复路由：Discord 收到的消息会回传到 Discord。

### 5.5 论坛频道与交互组件

- **论坛/媒体频道**：仅支持在子帖子（thread）中发帖。可通过 `openclaw message thread create` 创建帖子，或向论坛父频道发消息以自动建帖（首行作为标题）。
- **Components v2**：支持按钮、选择菜单、表单等；可设置 `allowedUsers` 限制点击者；`components.reusable: true` 可允许多次使用直至过期。

### 5.6 Discord 故障排查摘要

| 现象 | 建议 |
|------|------|
| 使用了未启用的 Intents 或收不到公会消息 | 在 Developer Portal 启用 **Message Content Intent**、**Server Members Intent**，修改后重启 Gateway |
| 公会消息被拦截 | 检查 `requireMention`、`channels.discord.guilds`、`groupPolicy`；若配置了 `guilds.*.channels`，仅列出的频道会被放行 |
| 设了 requireMention: false 仍被拦 | 检查是否被 guild/channel 的 `users` 白名单拒绝；`requireMention` 需写在 `channels.discord.guilds` 或对应 channel 下 |
| 长耗时处理超时或重复回复 | 调整 `channels.discord.eventQueue.listenerTimeout`（多账号在 `accounts.<id>.eventQueue.listenerTimeout`），例如 `120000` |
| DM/配对异常 | 确认未设 `dmPolicy: "disabled"` 或 `dm.enabled: false`；配对模式下需在别处批准配对码 |
| 语音 STT 出现 DecryptionFailed | 保持 `daveEncryption: true`、`decryptionFailureTolerance: 24`，并保持 OpenClaw 更新以获取自动重连逻辑；可参考 discord.js #11419 |
| 权限/审计不一致 | `openclaw channels status --probe` 仅对数字 channel ID 做权限检查；使用 slug 时需在运行时自行确认 |

常用诊断命令：`openclaw doctor`、`openclaw channels status --probe`、`openclaw logs --follow`。

---

## 六、配置示例（含 Discord）

### 6.1 多平台最小示例（含 Discord）

```json5
{
  agent: { workspace: "~/.openclaw/workspace" },
  channels: {
    whatsapp: { allowFrom: ["+15555550123"] },
    telegram: {
      enabled: true,
      botToken: "YOUR_TOKEN",
      allowFrom: ["123456789"],
    },
    discord: {
      enabled: true,
      token: "YOUR_DISCORD_BOT_TOKEN",
      dm: { allowFrom: ["123456789012345678"] },
    },
  },
}
```

### 6.2 多用户 DM 隔离（含 Discord）

```json5
{
  session: { dmScope: "per-channel-peer" },
  channels: {
    discord: {
      enabled: true,
      token: "YOUR_DISCORD_BOT_TOKEN",
      dm: {
        enabled: true,
        allowFrom: ["123456789012345678", "987654321098765432"],
      },
    },
  },
}
```

### 6.3 完整示例中的 Discord 片段（来自官方 Configuration Examples）

```json5
discord: {
  enabled: true,
  token: "YOUR_DISCORD_BOT_TOKEN",
  dm: { enabled: true, allowFrom: ["123456789012345678"] },
  guilds: {
    "123456789012345678": {
      slug: "friends-of-openclaw",
      requireMention: false,
      channels: {
        general: { allow: true },
        help: { allow: true, requireMention: true },
      },
    },
  },
},
```

---

## 与本项目其他文档

本项目为 **Discord 任务中心 + 六部框架** 的 OpenClaw 技能与协作体系：

- **[六部框架手册.md](六部框架手册.md)** — 多 Agent 六部协作（司礼监 + 吏户礼兵刑工）的流程、通信原则、配置示例与快速开始；部署六部时以该手册为准。
- **[discord-dynasty/](discord-dynasty/)** — 论坛任务帖（新建任务、归档任务、模型标签）、六部管理频道模板的 skill 说明与 API 参考；SKILL.md 供 Agent 遵循，reference.md 供实现与扩展查阅。

上述文档均以本文档为 **OpenClaw 与 Discord 配置基准**（openclaw.json 结构、通道策略、故障排查等）。

---

## 七、参考链接

- [Configuration](https://docs.openclaw.ai/configuration) — 配置总览与常见任务  
- [Configuration Reference](https://docs.openclaw.ai/gateway/configuration-reference) — 完整字段说明（含 Discord 节）  
- [Configuration Examples](https://docs.openclaw.ai/gateway/configuration-examples) — 官方示例  
- [Discord (Bot API)](https://docs.openclaw.ai/channels/discord) — Discord 通道完整说明  
- [Slash commands](https://docs.openclaw.ai/tools/slash-commands) — 斜杠命令  
- [Channel troubleshooting](https://docs.openclaw.ai/channels/troubleshooting) — 通道故障排查  
- [Pairing](https://docs.openclaw.ai/channels/pairing) — 配对流程  

---

*文档来源：docs.openclaw.ai，整理后供 discord-task-skill 项目参考。*
