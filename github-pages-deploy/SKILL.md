---
name: github-pages-deploy
description: Deploy static frontend projects to GitHub Pages. Use when the user mentions deploying to GitHub, publishing a website to GitHub Pages, hosting static sites, or making HTML/CSS/JS projects publicly accessible online. Validates that the project is static (no backend code) before deployment.
license: MIT
---

# GitHub Pages Deployment

Deploy static frontend projects to GitHub Pages, making them accessible at `https://<username>.github.io/<repo-name>/`.

## Prerequisites

Verify environment before proceeding:

```bash
gh --version       # GitHub CLI required
gh auth status     # Must be authenticated
git --version      # Git required
```

If not installed: https://cli.github.com/ → then `gh auth login`

## Validation

Check if project is deployable:

```bash
# Must have index.html
ls index.html docs/index.html dist/index.html build/index.html 2>/dev/null

# Must NOT have backend files (if found, STOP)
ls server.js app.py main.go *.php 2>/dev/null
```

**If backend files detected**: Do NOT proceed. Explain GitHub Pages only supports static sites. Suggest Vercel, Netlify, or Railway instead.

## Deployment Steps

### 1. Initialize Git

```bash
cd <project-path>
[ ! -d .git ] && git init && git add . && git commit -m "Initial commit"
```

### 2. Create Repository and Push

```bash
gh repo create <repo-name> --public --source=. --push
```

Or for existing repo:
```bash
git remote add origin https://github.com/<username>/<repo-name>.git
git branch -M main
git push -u origin main
```

### 3. Enable GitHub Pages

```bash
GITHUB_USER=$(gh api user -q .login)
gh api repos/$GITHUB_USER/<repo-name>/pages -X POST \
  -H "Accept: application/vnd.github+json" \
  -f source='{"branch":"main","path":"/"}'
```

### 4. Confirm

```bash
gh api repos/$GITHUB_USER/<repo-name>/pages --jq '.html_url'
```

## Edge Cases

| Error | Solution |
|-------|----------|
| Pages already enabled | Use `-X PUT` instead of `-X POST` |
| Not authenticated | `gh auth login` |
| No index.html | Cannot deploy; static site requires index.html |

## Example Output

Success:
```
✅ Deployed successfully!
📦 Repository: https://github.com/<username>/<repo-name>
🌐 Live URL: https://<username>.github.io/<repo-name>/
```

Rejection:
```
❌ Cannot deploy to GitHub Pages
This project contains backend code (server.js).
Alternatives: Vercel, Netlify, Railway
```

## Updates

For existing sites, just push:
```bash
git add . && git commit -m "Update" && git push
```
