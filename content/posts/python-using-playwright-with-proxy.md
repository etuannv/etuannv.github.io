---
title: "Python using Playwright with proxy"
date: 2021-01-11
tags: ["web-automation", "web-scraping", "python", "proxies", "selenium"]
categories: ["posts"]
description: "How to use a proxy (with username and password) in Python Playwright for web scraping and automation."
---

Playwright is a web automation framework provided by Microsoft, similar to Selenium. We can use these web automation frameworks to scrape (extract) data from a website. Sometimes we have to use a [proxy](https://brightdata.grsm.io/p709e77) to bypass blocking from a website.

### What is Playwright?

Playwright is a web autotest (automation) framework provided by Microsoft. It is similar to Selenium.

We may use these web automation frameworks to scrape (extract) data from a website. Sometimes, we have to use a proxy to bypass blocking from a website. The source code below shows how to use a proxy for Playwright.

### Using a Proxy with Playwright

Pass the proxy configuration (server address, username, and password) directly to the `launch()` call:

```python
from playwright.sync_api import sync_playwright

with sync_playwright() as p:
    browser = p.webkit.launch(
        headless=False,
        proxy={
            "server": "server-address:port",
            "username": "My_user",
            "password": "My_password",
        },
    )
    context = browser.new_context()
    page = context.new_page()
    page.goto("https://whoer.net")
    page.screenshot(path="whoer.png")
    browser.close()
```

- Replace `server-address:port` with your proxy server's host and port.
- Replace `My_user` and `My_password` with your proxy credentials.
- The screenshot saved as `whoer.png` lets you verify the proxy IP is being used correctly.

### Installing Playwright

If you haven't installed Playwright yet:

```bash
pip install playwright
playwright install
```

If you have any question, don't hesitate to [contact me](/contact/).

Good luck!

---

**See also:**
- [Best Proxies for Web Scraping](/posts/best-proxies-for-web-scraping/) — choosing the right proxy provider
