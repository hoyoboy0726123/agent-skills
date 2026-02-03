---
name: github-pages-deploy
description: Deploy static frontend projects to GitHub Pages. Use when user mentions "deploy to GitHub", "publish to GitHub Pages", "host on GitHub", or wants to make a static website publicly accessible.
---

# GitHub Pages Deployment

Deploy static frontend projects to GitHub Pages, making them accessible at `https://<username>.github.io/<repo-name>/`.

## Prerequisites Check

Before deploying, verify the environment:

```bash
# Check GitHub CLI
gh --version || echo "ERROR: gh CLI not installed"

# Check authentication
gh auth status || echo "ERROR: Not logged in - run: gh auth login"

# Check Git
git --version || echo "ERROR: Git not installed"
```

## Decision Tree

```
User wants to deploy → Is it a static frontend project?
    │
    ├─ Check for index.html
    │   └─ ls index.html docs/index.html dist/index.html build/index.html 2>/dev/null
    │
    ├─ Check for backend files (if found → REJECT)
    │   └─ ls server.js app.py main.go *.php 2>/dev/null
    │
    ├─ Check package.json for backend frameworks (if found → REJECT)
    │   └─ grep -E "(express|fastify|koa|nest|next|nuxt)" package.json
    │
    └─ Result:
        ├─ Static frontend → Proceed with deployment
        └─ Has backend → STOP and explain alternatives
```

## Deployment Flow

### Step 1: Validate Project

```bash
cd <project-path>

# Must have index.html somewhere
if ! ls index.html docs/index.html dist/index.html build/index.html 2>/dev/null; then
    echo "ERROR: No index.html found. Not a valid static site."
    exit 1
fi

# Must NOT have backend files
if ls server.js app.py main.go 2>/dev/null; then
    echo "ERROR: Backend files detected. Use Vercel/Netlify/Railway instead."
    exit 1
fi
```

### Step 2: Initialize Git (if needed)

```bash
if [ ! -d .git ]; then
    git init
    git add .
    git commit -m "Initial commit"
fi
```

### Step 3: Create Repository and Push

```bash
# Create public repo and push in one command
gh repo create <repo-name> --public --source=. --push
```

Or if repo already exists:
```bash
git remote add origin https://github.com/<username>/<repo-name>.git
git branch -M main
git push -u origin main
```

### Step 4: Enable GitHub Pages

```bash
GITHUB_USER=$(gh api user -q .login)

# Enable Pages on main branch, root directory
gh api repos/$GITHUB_USER/<repo-name>/pages -X POST \
  -H "Accept: application/vnd.github+json" \
  -f source='{"branch":"main","path":"/"}'
```

For `/docs` directory:
```bash
gh api repos/$GITHUB_USER/<repo-name>/pages -X POST \
  -f source='{"branch":"main","path":"/docs"}'
```

### Step 5: Verify Deployment

```bash
# Check Pages status
gh api repos/$GITHUB_USER/<repo-name>/pages --jq '.html_url'
```

## Error Handling

| Error | Solution |
|-------|----------|
| `Pages already enabled` | Use `-X PUT` instead of `-X POST` |
| `Not authenticated` | Run `gh auth login` |
| `Repository not found` | Check repo name or create it first |

## Response Templates

### Success
```
✅ Deployed successfully!

📦 Repository: https://github.com/<username>/<repo-name>
🌐 Live URL: https://<username>.github.io/<repo-name>/

(May take 1-2 minutes to go live)
```

### Rejection (Backend Detected)
```
❌ Cannot deploy to GitHub Pages

Reason: This project contains backend code (<detected-file>).
GitHub Pages only supports static frontend sites.

Alternatives:
• Vercel (vercel.com) - Node.js, Next.js, serverless
• Netlify (netlify.com) - Serverless functions
• Railway (railway.app) - Full backend support
• Render (render.com) - Containers, databases
```

## Updating Existing Site

For sites already deployed, just push changes:

```bash
git add .
git commit -m "Update content"
git push origin main
# GitHub automatically redeploys
```
