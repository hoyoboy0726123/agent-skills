# 🤖 Agent Skills

給 AI 助手使用的技能庫，支援 Claude Code、Gemini CLI、OpenClaw 等。

## 前置條件

### 1. 安裝 Git

```bash
# Windows
winget install Git.Git

# macOS
brew install git

# Linux (Ubuntu/Debian)
sudo apt install git
```

### 2. 安裝 GitHub CLI (`gh`)

GitHub CLI 是 GitHub 官方命令列工具，用於建立 repo、管理 Pages 等操作。

```bash
# Windows
winget install GitHub.cli

# macOS
brew install gh

# Linux (Ubuntu/Debian)
sudo apt install gh

# 或使用 Conda
conda install gh --channel conda-forge
```

📖 官方文件：https://cli.github.com/

### 3. 登入 GitHub

```bash
gh auth login
```

依照提示選擇：
- GitHub.com
- HTTPS
- 透過瀏覽器登入

確認登入狀態：
```bash
gh auth status
```

---

## 技能列表

| 技能 | 說明 |
|------|------|
| [github-pages-deploy](./github-pages-deploy/) | 部署純前端專案到 GitHub Pages |

---

## 使用方式

### Claude Code

```bash
# 複製到個人技能目錄
mkdir -p ~/.claude/skills
cp -r github-pages-deploy ~/.claude/skills/

# 或複製到專案目錄
mkdir -p .claude/skills
cp -r github-pages-deploy .claude/skills/
```

使用時輸入 `/github-pages-deploy` 或讓 Claude 自動偵測。

### Gemini CLI / 其他 Agent

將 `SKILL.md` 內容提供給 Agent，或放到對應的指令目錄。

### OpenClaw

將技能資料夾放到 workspace 的 `skills/` 目錄下。

---

## 貢獻

歡迎提交 PR 新增更多技能！

每個技能應該：
1. 有獨立資料夾
2. 包含 `SKILL.md`（必要）
3. 使用 YAML frontmatter 定義 `name` 和 `description`

---

## 授權

MIT License
