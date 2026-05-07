---
title: "Etsy Ads Reporting Tool"
date: 2025-05-23
tags: ["python", "web-scraping", "selenium", "etsy", "dashboard"]
categories: ["projects"]
description: "Automated tool to download and analyze Etsy advertisement reports that Etsy doesn't natively support exporting."
---

## Overview

Etsy doesn't let sellers download their advertisement reports directly — making it hard to analyze, optimize, or share ad performance data. This tool solves that problem completely.

## What It Does

The tool automatically logs into Etsy, collects both **daily and weekly ad reports**, converts them to CSV, and uploads to a secure web-based dashboard.

## Key Features

- **Automated data collection** — no manual copying
- **Daily & weekly reports** with spend, revenue, views, orders, and conversion ratios
- **Advanced filtering** by date range, department, class, listing ID, revenue/spend range
- **CSV export** for Excel/Google Sheets analysis
- **Cloud-based dashboard** — accessible from anywhere, no install needed

## Tech Stack

- Python + Selenium for automated Etsy login and data extraction
- Django dashboard for data display and filtering
- CSV export pipeline

## Security

Etsy credentials are never stored. Scraping runs on a secure server; reports are encrypted and private.

---

👉 **Interested in a similar tool?** [Contact me](/contact)
