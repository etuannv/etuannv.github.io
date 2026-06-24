---
title: "Python sử dụng Playwright với proxy"
date: 2021-01-11
tags: ["web-automation", "web-scraping", "python", "proxies", "selenium"]
categories: ["posts"]
description: "Cách sử dụng proxy (với tên đăng nhập và mật khẩu) trong Python Playwright cho web scraping và tự động hóa."
---

Playwright là một framework tự động hóa web do Microsoft cung cấp, tương tự Selenium. Chúng ta có thể dùng các framework tự động hóa web này để scrape (trích xuất) dữ liệu từ một website. Đôi khi chúng ta phải dùng [proxy](https://brightdata.grsm.io/p709e77) để vượt qua việc bị website chặn.

### Playwright là gì?

Playwright là một framework kiểm thử tự động (automation) web do Microsoft cung cấp. Nó tương tự Selenium.

Chúng ta có thể dùng các framework tự động hóa web này để scrape (trích xuất) dữ liệu từ một website. Đôi khi, chúng ta phải dùng proxy để vượt qua việc bị website chặn. Đoạn code dưới đây cho thấy cách dùng proxy với Playwright.

### Sử dụng Proxy với Playwright

Truyền cấu hình proxy (địa chỉ server, tên đăng nhập, và mật khẩu) trực tiếp vào lệnh `launch()`:

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

- Thay `server-address:port` bằng host và port của proxy server của bạn.
- Thay `My_user` và `My_password` bằng thông tin đăng nhập proxy của bạn.
- Ảnh chụp màn hình lưu dưới dạng `whoer.png` giúp bạn xác minh proxy IP đang được sử dụng đúng.

### Cài đặt Playwright

Nếu bạn chưa cài Playwright:

```bash
pip install playwright
playwright install
```

Nếu có câu hỏi nào, đừng ngần ngại [liên hệ với tôi](/vi/contact/).

Chúc may mắn!

---

**Xem thêm:**
- [Proxy tốt nhất cho Web Scraping](/vi/posts/best-proxies-for-web-scraping/) — chọn nhà cung cấp proxy phù hợp
