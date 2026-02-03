---
name: github-pages-deploy
description: 部署純前端專案到 GitHub Pages。當用戶提到「部署到 GitHub」、「放到 GitHub Pages」、「發布網站到 GitHub」、「建立 GitHub 倉庫並部署」等關鍵字時使用此技能。
argument-hint: [專案路徑]
---

# GitHub Pages 部署技能

將純前端專案部署到 GitHub Pages，讓網站可透過 `https://<username>.github.io/<repo-name>/` 訪問。

## 前置條件

1. **已安裝 GitHub CLI**
   ```bash
   gh --version
   ```
   如果未安裝，請先安裝：
   - Windows: `winget install GitHub.cli`
   - macOS: `brew install gh`
   - Linux: `sudo apt install gh`

2. **已登入 GitHub**
   ```bash
   gh auth status
   ```
   如果未登入，執行：
   ```bash
   gh auth login
   ```

3. **已安裝 Git**
   ```bash
   git --version
   ```

## 執行前必須檢查

### 🔍 判斷是否為純前端專案

在執行部署前，**必須先檢查專案是否適合 GitHub Pages**：

**✅ 適合部署的專案（純前端）：**
- 根目錄或 `/docs` 有 `index.html`
- 只包含 HTML、CSS、JavaScript、圖片等靜態資源
- 靜態網站生成器輸出（如 Hugo、Jekyll、Hexo 的 build 結果）
- React/Vue/Angular 等框架的 **build 後產物**（`dist/`、`build/`）

**❌ 不適合部署的專案：**
- 需要後端伺服器（Node.js、Python、PHP、Ruby 等）
- 有 `server.js`、`app.py`、`main.go` 等後端入口
- 需要資料庫連線
- 使用 SSR（Server-Side Rendering）且未導出靜態檔案
- 只有原始碼沒有 build（如只有 `src/` 沒有 `dist/`）

### 檢查指令

```bash
# 檢查是否有 index.html
ls index.html 2>/dev/null || ls docs/index.html 2>/dev/null

# 檢查是否有後端檔案（如果存在則不適合）
ls server.js app.py main.go requirements.txt package.json 2>/dev/null
```

如果有 `package.json`，檢查是否為純前端：
```bash
# 檢查 dependencies 是否有後端框架
cat package.json | grep -E "(express|fastify|koa|next|nuxt|nest)"
```

**如果專案不適合 GitHub Pages，請向用戶說明原因並建議其他方案**（如 Vercel、Netlify、Railway、Render 等支援後端的平台）。

## 部署流程

### 步驟 1：初始化 Git（如果尚未初始化）

```bash
cd <專案路徑>

# 檢查是否已有 git
if [ ! -d .git ]; then
    git init
    git add .
    git commit -m "Initial commit"
fi
```

### 步驟 2：建立 GitHub Repository 並推送

```bash
# 建立公開 repo 並推送（repo 名稱使用資料夾名稱）
gh repo create <repo-name> --public --source=. --push
```

或者如果 repo 已存在：
```bash
git remote add origin https://github.com/<username>/<repo-name>.git
git branch -M main
git push -u origin main
```

### 步驟 3：開啟 GitHub Pages

```bash
# 取得目前登入的用戶名
GITHUB_USER=$(gh api user -q .login)

# 開啟 GitHub Pages（使用 main branch 根目錄）
gh api repos/$GITHUB_USER/<repo-name>/pages -X POST \
  -H "Accept: application/vnd.github+json" \
  -f source='{"branch":"main","path":"/"}'
```

如果要使用 `/docs` 目錄：
```bash
gh api repos/$GITHUB_USER/<repo-name>/pages -X POST \
  -H "Accept: application/vnd.github+json" \
  -f source='{"branch":"main","path":"/docs"}'
```

### 步驟 4：確認部署狀態

```bash
# 檢查 Pages 狀態
gh api repos/$GITHUB_USER/<repo-name>/pages

# 取得網站網址
echo "網站網址: https://$GITHUB_USER.github.io/<repo-name>/"
```

## 完整範例

### 範例 1：新專案部署

```bash
# 1. 進入專案目錄
cd my-website

# 2. 確認有 index.html
ls index.html

# 3. 初始化並提交
git init
git add .
git commit -m "Initial commit"

# 4. 建立 repo 並推送
gh repo create my-website --public --source=. --push

# 5. 開啟 GitHub Pages
GITHUB_USER=$(gh api user -q .login)
gh api repos/$GITHUB_USER/my-website/pages -X POST \
  -f source='{"branch":"main","path":"/"}'

# 6. 完成！
echo "部署完成！網址: https://$GITHUB_USER.github.io/my-website/"
```

### 範例 2：已有 repo，只需開啟 Pages

```bash
GITHUB_USER=$(gh api user -q .login)
REPO_NAME="existing-repo"

gh api repos/$GITHUB_USER/$REPO_NAME/pages -X POST \
  -f source='{"branch":"main","path":"/"}'
```

### 範例 3：更新已部署的網站

```bash
# 只需要 commit 並 push，GitHub 會自動重新部署
git add .
git commit -m "更新內容"
git push origin main
```

## 常見錯誤處理

### 錯誤：Pages already exists
```bash
# Pages 已開啟，可以用 PUT 更新設定
gh api repos/$GITHUB_USER/$REPO_NAME/pages -X PUT \
  -f source='{"branch":"main","path":"/"}'
```

### 錯誤：Not authenticated
```bash
# 重新登入
gh auth login
```

### 錯誤：Repository not found
```bash
# 確認 repo 名稱正確，或先建立 repo
gh repo create <repo-name> --public
```

## 回覆用戶的範本

### 成功部署
```
✅ 部署成功！

📦 Repository: https://github.com/<username>/<repo-name>
🌐 網站網址: https://<username>.github.io/<repo-name>/

（網站可能需要幾分鐘才能生效）
```

### 專案不適合 GitHub Pages
```
❌ 這個專案不適合部署到 GitHub Pages

原因：[說明原因，如「專案包含後端伺服器 (server.js)」]

GitHub Pages 只支援純前端靜態網站。

建議替代方案：
- Vercel (https://vercel.com) - 支援 Node.js、Next.js
- Netlify (https://netlify.com) - 支援 Serverless Functions
- Railway (https://railway.app) - 支援各種後端
- Render (https://render.com) - 支援容器化部署
```

## 參考資源

- [GitHub CLI 官方文件](https://cli.github.com/manual/)
- [GitHub Pages 官方文件](https://docs.github.com/en/pages)
- [GitHub REST API - Pages](https://docs.github.com/en/rest/pages)
