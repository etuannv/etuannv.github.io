---
title: "Price Tracking System"
date: 2020-01-01
tags: ["python", "web-scraping", "django", "automation"]
categories: ["projects"]
description: "Automated price tracking system that scrapes competitor prices daily and displays trends on a dashboard."
---

## Overview

Staying competitive in e-commerce means knowing what your competitors are charging — and knowing it every single day. Manually checking product prices across multiple websites is time-consuming, inconsistent, and simply not scalable when you're tracking dozens or hundreds of products.

The **Price Tracking System** automates this process entirely. It scrapes competitor product pages on a daily schedule, stores every price point in a structured database, and surfaces the data through a clean web dashboard with historical trend charts and price change alerts. With this system, you always have an accurate and up-to-date view of the competitive pricing landscape without lifting a finger.

## How It Works

The system uses a Python-based web scraper configured with a list of target product URLs and competitor websites. Every day, the scraper visits each page, extracts the current price, and stores it alongside a timestamp in the database. The Django dashboard then displays this data as interactive trend charts, comparison tables, and alert notifications whenever a significant price change is detected.

## Key Features

- **Fully automated daily scraping** — runs on a schedule and collects prices from all configured target pages without any manual intervention
- **Price history database** — every price change is recorded with a timestamp, building a comprehensive historical dataset that grows more valuable over time
- **Trend visualization** — interactive charts display price movements over custom date ranges, making it easy to spot patterns and seasonal trends
- **Price change alerts** — the system automatically flags significant price drops or increases, so you can react quickly to market changes
- **Multi-product, multi-competitor tracking** — monitor as many products and competitor websites as needed from a single dashboard
- **Clean comparison dashboard** — side-by-side price comparisons across competitors for any selected product or category
- **Data export** — price history data can be exported to CSV for further analysis in Excel, Google Sheets, or business intelligence tools

## Tech Stack

- **Python** for automated web scraping and data extraction
- **Django** for the backend application, data management, and dashboard
- **PostgreSQL** for structured price history storage
- **Chart.js** for interactive trend visualization on the frontend
- **Celery + Cron** for daily scrape job scheduling

## Use Cases

- **E-commerce sellers** monitoring competitor prices to adjust their own pricing strategy dynamically
- **Purchasing teams** tracking supplier or wholesale prices over time to identify the best time to buy
- **Market researchers** collecting pricing data across industries for reports or analysis
- **Product managers** monitoring how a product's market price evolves after launch

## Demo

A live demo is available to explore the dashboard, sample price history data, and trend charts.

Live demo: [pricetracking.etuannv.com](http://pricetracking.etuannv.com)  
Username: `viewer` | Password: `etuannv2019demo`

---

👉 Need a custom price tracker built for your market or industry? [Contact me](/contact) to discuss your project.

---

**See also:**
- [Power Price Tracking System](/projects/power-price-tracking/) — similar system for energy market prices
- [Best Proxies for Web Scraping](/posts/best-proxies-for-web-scraping/) — proxy solutions used in scraping projects
