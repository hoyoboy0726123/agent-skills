---
name: github-pages-deploy
description: Deploy static frontend projects to GitHub Pages. Use when the user mentions deploying to GitHub, publishing a website to GitHub Pages, hosting static sites, or making HTML/CSS/JS projects publicly accessible online.
license: MIT
compatibility: Requires git, gh (GitHub CLI), and internet access
metadata:
  author: hoyoboy0726123
  version: "1.0"
---

# GitHub Pages Deployment

Deploy static frontend projects to GitHub Pages, making them accessible at `https://<username>.github.io/<repo-name>/`.

## When to Use This Skill

Activate this skill when the user:
- Asks to deploy a website to GitHub
- Wants to publish a static site
- Mentions GitHub Pages
- Needs to host HTML/CSS/JS files online
- Asks to make a frontend project publicly accessible

## Prerequisites Check

Before proceeding, verify the environment:

```bash
gh --version       # GitHub CLI must be installed
gh auth status     # Must be authenticated
git --version      # Git must be installed
```

If prerequisites fail, guide the user to install:
- GitHub CLI: https://cli.github.com/
- Then run: `gh auth login`

## Step-by-Step Instructions

### Step 1: Validate Project Type

Check if the project is a valid static frontend:

```bash
# Look for index.html (required)
ls index.html docs/index.html dist/index.html build/index.html 2>/dev/null

# Check for backend files (if found, STOP)
ls server.js app.py main.go *.php 2>/dev/null
```

**If backend files are detected, do NOT proceed.** Explain that GitHub Pages only supports static sites and suggest alternatives:
- Vercel (vercel.com)
- Netlify (netlify.com)
- Railway (railway.app)

### Step 2: Initialize Git

```bash
cd <project-path>
if [ ! -d .git ]; then
    git init
    git add .
    git commit -m "Initial commit"
fi
```

### Step 3: Create Repository and Push

```bash
gh repo create <repo-name> --public --source=. --push
```

Or if repository already exists:
```bash
git remote add origin https://github.com/<username>/<repo-name>.git
git branch -M main
git push -u origin main
```

### Step 4: Enable GitHub Pages

```bash
GITHUB_USER=$(gh api user -q .login)
gh api repos/$GITHUB_USER/<repo-name>/pages -X POST \
  -H "Accept: application/vnd.github+json" \
  -f source='{"branch":"main","path":"/"}'
```

### Step 5: Confirm Deployment

```bash
gh api repos/$GITHUB_USER/<repo-name>/pages --jq '.html_url'
```

## Common Edge Cases

| Scenario | Solution |
|----------|----------|
| Pages already enabled | Use `-X PUT` instead of `-X POST` |
| Not authenticated | Run `gh auth login` |
| No index.html found | Cannot deploy; explain requirement |
| Backend code detected | Reject and suggest Vercel/Netlify |

## Example Output

### Success Response
```
✅ Deployed successfully!

📦 Repository: https://github.com/<username>/<repo-name>
🌐 Live URL: https://<username>.github.io/<repo-name>/

The site may take 1-2 minutes to go live.
```

### Rejection Response (Backend Detected)
```
❌ Cannot deploy to GitHub Pages

This project contains backend code (server.js detected).
GitHub Pages only supports static HTML/CSS/JS sites.

Recommended alternatives:
• Vercel - https://vercel.com
• Netlify - https://netlify.com
• Railway - https://railway.app
```

## Updating an Existing Site

For already-deployed sites, simply push changes:

```bash
git add .
git commit -m "Update content"
git push origin main
```

GitHub automatically redeploys on push.
