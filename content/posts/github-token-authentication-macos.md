---
title: "Updating GitHub Authentication Using Token on macOS"
date: 2021-08-01
tags: ["github", "macos", "git", "authentication"]
categories: ["tips"]
description: "Step-by-step guide to replace GitHub password authentication with a personal access token on macOS."
---

With GitHub's recent changes, passwords are no longer allowed for authentication when performing actions like `git push` or `git pull`.

## Step 1: Generate a Personal Access Token

1. Go to GitHub → **Settings** → **Developer settings** → **Personal access tokens**
2. Click **Generate new token**
3. Select scopes: at minimum check `repo`
4. Click **Generate token** and **copy it immediately** (you won't see it again)

## Step 2: Clear Old Credentials on macOS

```bash
git credential-osxkeychain erase
host=github.com
protocol=https
# Press Enter twice
```

Or via Keychain Access app: search for `github.com` and delete the entry.

## Step 3: Use Token on Next Push

```bash
git push origin main
# Username: your GitHub username
# Password: paste your personal access token here
```

macOS will offer to save it to Keychain — say yes to avoid entering it again.

That's it! Your GitHub authentication is now updated to use a personal access token.
