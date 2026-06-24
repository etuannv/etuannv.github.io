---
title: "Python: Lấy thời gian hiện tại theo múi giờ"
date: 2021-08-10
tags: ["python", "datetime", "timezone"]
categories: ["posts"]
description: "Cách lấy thời gian hiện tại theo một múi giờ cụ thể bằng thư viện pytz của Python."
---

Khi xây dựng các chương trình Python chạy trên nhiều khu vực, bạn thường cần làm việc với các múi giờ cụ thể.

## Lấy thời gian hiện tại theo một múi giờ cụ thể

```python
import pytz
from datetime import datetime

# Định nghĩa múi giờ
tz = pytz.timezone('Asia/Ho_Chi_Minh')

# Lấy thời gian hiện tại theo múi giờ đó
now = datetime.now(tz)
print(now.strftime('%Y-%m-%d %H:%M:%S %Z'))
# Kết quả: 2021-08-10 15:30:00 +07
```

## Liệt kê tất cả múi giờ có sẵn

```python
import pytz

for tz in pytz.all_timezones:
    print(tz)
```

## Các múi giờ phổ biến

| Khu vực | Chuỗi múi giờ |
|--------|----------------|
| Việt Nam | `Asia/Ho_Chi_Minh` |
| US Eastern | `America/New_York` |
| US Pacific | `America/Los_Angeles` |
| UTC | `UTC` |
| London | `Europe/London` |

## Cài đặt pytz

```bash
pip install pytz
```
