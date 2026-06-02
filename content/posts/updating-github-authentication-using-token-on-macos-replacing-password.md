---
title: "Updating GitHub Authentication Using Token on macOS (Replacing Password)"
date: 2024-08-20
tags: ["github", "macos", "git", "authentication"]
categories: ["posts"]
description: "How to replace GitHub password authentication with a personal access token on macOS and store it in the Keychain."
---

With GitHub’s recent changes, passwords are no longer allowed for authentication when performing actions like check-in or check-out. Here’s how you can update your authentication method to use a personal access token instead of a password on macOS.

Table of Contents

- Step 1: Generate a Personal Access Token (PAT)
- Step 2: Update Git Credentials on macOS
- Step 3: Store Token in macOS Keychain

### Step 1: Generate a Personal Access Token (PAT)

1. Go to your GitHub account and navigate to Settings.
2. Under Developer settings, select Personal access tokens.
3. Click on Generate new token.
4. Select the scopes or permissions you need for your token, such as `repo` for repository access.
5. Click Generate token and copy the token somewhere safe (you won’t be able to see it again).

### Step 2: Update Git Credentials on macOS

Open Terminal and clear the old GitHub credentials so macOS will prompt for new ones the next time you interact with GitHub:

```bash
git credential-osxkeychain erase
host=github.com
protocol=https
# Press Enter twice
```

Alternatively, open the Keychain Access app and search for `github.com`, then delete any existing GitHub credential entries.

Now, when you run a Git command that requires authentication (for example `git push`), use your GitHub username and paste the personal access token when prompted for a password:

```bash
git push origin main
# Username: <your-github-username>
# Password: <paste your personal access token here>
```

macOS may offer to save the token to the Keychain after you enter it; accept to avoid entering it again.

### Step 3: Store Token in macOS Keychain

If you want to avoid entering the token repeatedly, allow macOS to store it in your Keychain when prompted. This keeps the token secure and lets Git operations use it automatically.

That's it — your GitHub authentication is now updated to use a personal access token on macOS.

---

**See also:**
- [Updating GitHub Authentication Using Token on macOS](/posts/github-token-authentication-macos/) — original 2021 guide
