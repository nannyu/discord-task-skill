# 发布到 ClawHub

本仓库的 **`discord-task-center/`** 目录为 ClawHub 的**发布单元**（仅该目录会被上传，含 SKILL.md、reference.md、clawhub.json、README.md、templates/）。

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

2. **发布 skill 目录**（建议用**绝对路径**，否则可能报 “Path must be a folder”）。版本号与 changelog 需与 `discord-task-center/clawhub.json` 中的 `version` 一致或按需递增：
   ```bash
   clawhub publish "$(pwd)/discord-task-center" \
     --slug discord-task-center \
     --name "Discord Task Center" \
     --version 1.1.0 \
     --tags latest \
     --changelog "Add six-ministries channel template (司礼监+六部), multi-agent protocol (上朝/上奏/回传/巡检). Task center, archive, model tags unchanged."
   ```

3. 若 CLI 提示需要更多信息，按提示填写。发布成功后可在 https://clawhub.ai 搜索 `discord-task-center` 查看。

## 后续更新

1. 修改 skill 后，先更新 **`discord-task-center/clawhub.json`** 里的 `version`（语义化版本：补丁 1.1.1、小功能 1.2.0、大变更 2.0.0）。
2. 在项目根目录执行（绝对路径）：
   ```bash
   clawhub publish "$(pwd)/discord-task-center" \
     --slug discord-task-center \
     --name "Discord Task Center" \
     --version 1.1.1 \
     --tags latest \
     --changelog "描述本次改动"
   ```
3. 或使用 **`clawhub sync`**：在 skill 根目录（即 `discord-task-center/`）配置好后，可用 `clawhub sync --all` 扫描并发布，可用 `--bump patch|minor|major` 自动递增版本。

## 说明

- 发布的是 **`discord-task-center/`** 目录，不是整个 repo；该目录内所有文件（含 `templates/*.json`）会一并上传。
- **路径**：`clawhub publish` 要求 path 为文件夹，相对路径有时会报错，建议用 `"$(pwd)/discord-task-center"`。
- **clawhub.json**：ClawHub 展示用 name、tagline、description、category、tags、version、license；发布时 `--version` 建议与其中 `version` 一致。
- **SKILL.md** 会被 ClawHub 索引并展示；变更日志通过 `--changelog` 附加到当次发布的版本。
