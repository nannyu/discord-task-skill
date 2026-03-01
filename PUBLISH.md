# 发布到 ClawHub

本仓库以新项目 **discord-dynasty** 发布到 ClawHub。发布单元为 **`discord-task-center/`** 目录（含 SKILL.md、reference.md、clawhub.json、README.md、templates/）。架构借鉴自 [Ai Court](https://clawhub.ai/wanikua/ai-court)。

## 前置条件

- **GitHub 账号** 且注册时间 ≥ 1 周（ClawHub 要求）
- 已安装 **ClawHub CLI** 并登录

```bash
npm i -g clawhub
# 或 pnpm add -g clawhub

clawhub login
# 按提示在浏览器完成登录
```

## 发布步骤

1. **进入项目根目录**：
   ```bash
   cd /path/to/discord-task-skill
   ```

2. **以 discord-dynasty 发布**（建议用**绝对路径**）。版本号与 `discord-task-center/clawhub.json` 中的 `version` 一致；备注中已注明对 Ai Court 的架构借鉴：
   ```bash
   clawhub publish "$(pwd)/discord-task-center" \
     --slug discord-dynasty \
     --name "Discord Dynasty" \
     --version 1.1.0 \
     --tags latest \
     --changelog "Discord Dynasty: 明朝六部式管理频道模板（司礼监+六部）、多 Agent 协议（上朝/上奏/回传/巡检）。架构借鉴自 https://clawhub.ai/wanikua/ai-court"
   ```

3. 若 CLI 提示需要更多信息，按提示填写。发布成功后可在 https://clawhub.ai 搜索 **discord-dynasty** 查看。

## 后续更新

1. 修改 skill 后，先更新 **`discord-task-center/clawhub.json`** 里的 `version`（语义化版本：补丁 1.1.1、小功能 1.2.0、大变更 2.0.0）。
2. 在项目根目录执行（**slug 固定为 discord-dynasty**）：
   ```bash
   clawhub publish "$(pwd)/discord-task-center" \
     --slug discord-dynasty \
     --name "Discord Dynasty" \
     --version 1.1.1 \
     --tags latest \
     --changelog "描述本次改动"
   ```
3. 或使用 **`clawhub sync`**：在 skill 根目录配置好 slug 后，可用 `clawhub sync --all` 扫描并发布，可用 `--bump patch|minor|major` 自动递增版本。

## 说明

- **ClawHub 项目名**：**discord-dynasty**（安装：`clawhub install discord-dynasty`）。展示名称与描述见 `clawhub.json`，其中已注明架构借鉴自 [Ai Court](https://clawhub.ai/wanikua/ai-court)。
- 发布的是 **`discord-task-center/`** 目录，不是整个 repo；该目录内所有文件（含 `templates/*.json`）会一并上传。
- **路径**：`clawhub publish` 要求 path 为文件夹，相对路径有时会报错，建议用 `"$(pwd)/discord-task-center"`。
- **clawhub.json**：ClawHub 展示用 name、tagline、description（含 Ai Court 借鉴说明）、category、tags、version、license；发布时 `--version` 建议与其中 `version` 一致。
- **SKILL.md** 会被 ClawHub 索引并展示；变更日志通过 `--changelog` 附加到当次发布的版本。
