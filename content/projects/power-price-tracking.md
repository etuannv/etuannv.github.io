---
title: "Power Price Tracking System"
date: 2020-08-09
tags: ["python", "web-scraping", "django", "energy"]
categories: ["projects"]
description: "Daily automated scraper for electricity/power prices with trend visualization dashboard."
---

## Overview

Electricity and energy prices can fluctuate significantly from day to day, impacting household budgets, business operating costs, and investment decisions in the energy sector. Manually tracking these prices is tedious and error-prone — and by the time you notice a trend, it may already be too late to act on it.

The **Power Price Tracking System** solves this by automatically collecting electricity and power prices on a daily basis, storing them in a structured database, and presenting the data through an easy-to-read trend visualization dashboard. Whether you're monitoring energy costs for a business, conducting market research, or building a data feed for an energy trading platform, this system provides a reliable and always up-to-date source of price data.

## How It Works

A Python-based scraper runs automatically every day, visiting the relevant energy market websites or public data portals to collect the latest price listings. The data is cleaned, normalized, and stored in the database with a timestamp. The Django-powered dashboard then reads this data and renders interactive charts and tables, allowing users to view price trends over any selected time range.

## Key Features

- **Fully automated daily data collection** — the scraper runs on a schedule with no manual intervention required
- **Historical price database** — every price point is stored with a date and source, building a valuable long-term dataset over time
- **Trend visualization charts** — interactive line and bar charts show price movements over days, weeks, or months
- **Public-facing dashboard** — clean and simple UI accessible from any browser, no login required for viewing
- **Configurable data sources** — the system can be adapted to scrape from different energy market websites or regions
- **Exportable data** — price history can be exported for use in external analysis tools or reports

## Tech Stack

- **Python** for automated web scraping and data collection
- **Django** for the backend web application and data management
- **PostgreSQL** for structured time-series price data storage
- **Chart.js / Matplotlib** for trend visualization and chart rendering
- **Celery + Cron** for scheduling daily scrape tasks

## Use Cases

- Businesses monitoring energy costs to optimize operational budgets
- Energy market researchers tracking regional pricing trends over time
- Developers building price alert systems or energy trading tools
- Journalists and analysts covering the energy sector

## Demo

A live demo is available to explore the dashboard and historical price trends.

Live demo: [prating.etuannv.com](http://prating.etuannv.com)

---

👉 Need a custom price tracking or market monitoring system? [Contact me](/contact) to discuss your requirements.

---

**See also:**
- [Price Tracking System](/projects/price-tracking-system/) — similar system for e-commerce competitor prices
- [Best Proxies for Web Scraping](/posts/best-proxies-for-web-scraping/) — proxy solutions used in scraping projects
