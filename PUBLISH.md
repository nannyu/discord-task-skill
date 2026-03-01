# 发布到 ClawHub

本仓库的 **`discord-task-center/`** 目录为 ClawHub 的发布单元，可直接上传。

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

2. **发布 skill 目录**（发布根目录下的 `discord-task-center` 文件夹）。建议用**绝对路径**，否则可能报 “Path must be a folder”：
   ```bash
   clawhub publish "$(pwd)/discord-task-center" \
     --slug discord-task-center \
     --name "Discord Task Center" \
     --version 1.0.0 \
     --tags latest \
     --changelog "Initial release: forum as task board, tags, archive, model tag."
   ```

3. 若 CLI 提示需要更多信息，按提示填写。发布成功后可在 https://clawhub.ai 搜索 `discord-task-center` 查看。

## 后续更新

修改 skill 后发布新版本（建议先改 `discord-task-center/clawhub.json` 里的 `version`）：

```bash
# 例如 1.0.0 → 1.0.1（在项目根目录执行，用绝对路径）
clawhub publish "$(pwd)/discord-task-center" \
  --slug discord-task-center \
  --name "Discord Task Center" \
  --version 1.0.1 \
  --tags latest \
  --changelog "描述本次改动"
```

或使用 `clawhub sync` 在配置的 skill 根目录下扫描并批量发布。

## 说明

- 发布的是 **`discord-task-center/`** 目录（含 SKILL.md、reference.md、clawhub.json、README.md），不是整个 repo。
- **路径**：`clawhub publish` 要求 path 为文件夹，相对路径有时会报错，建议在项目根目录用 `"$(pwd)/discord-task-center"`。
- **clawhub.json** 用于 ClawHub 展示的 tagline、category、tags 等。
- **SKILL.md** 会被 ClawHub 索引并展示。
