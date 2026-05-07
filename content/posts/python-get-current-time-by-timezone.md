---
title: "Python: Get Current Time by Timezone"
date: 2021-08-10
tags: ["python", "datetime", "timezone"]
categories: ["python"]
description: "How to get the current time in a specific timezone using Python's pytz library."
---

When building Python programs that run across multiple regions, you often need to work with specific timezones.

## Get Current Time in a Specific Timezone

```python
import pytz
from datetime import datetime

# Define timezone
tz = pytz.timezone('Asia/Ho_Chi_Minh')

# Get current time in that timezone
now = datetime.now(tz)
print(now.strftime('%Y-%m-%d %H:%M:%S %Z'))
# Output: 2021-08-10 15:30:00 +07
```

## List All Available Timezones

```python
import pytz

for tz in pytz.all_timezones:
    print(tz)
```

## Common Timezones

| Region | Timezone String |
|--------|----------------|
| Vietnam | `Asia/Ho_Chi_Minh` |
| US Eastern | `America/New_York` |
| US Pacific | `America/Los_Angeles` |
| UTC | `UTC` |
| London | `Europe/London` |

## Install pytz

```bash
pip install pytz
```
