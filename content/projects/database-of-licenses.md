---
title: "Database of Licenses System"
date: 2019-03-27
tags: ["python", "web-scraping", "django", "database"]
categories: ["projects"]
description: "Automated system that scrapes automotive and contractor license data daily to track new licenses and status changes."
---

## Overview

The Database of Licenses System is a fully automated, two-module platform designed to collect, store, and monitor professional license data — specifically for the automotive and contractor industries. The system pulls data daily from public government databases, identifies newly issued licenses, and detects status changes on existing ones. This gives businesses, compliance teams, and researchers an always up-to-date view of the licensing landscape without any manual effort.

Whether you need to verify a contractor's credentials, track competitor activity, or stay on top of regulatory compliance, this system provides a reliable and searchable source of truth.

## How It Works

The system is split into two tightly integrated modules that work together seamlessly:

1. **Scraper Module** — A Python-based automated crawler that runs on a daily schedule. It visits the relevant government licensing portals, extracts the latest license records, compares them against the existing database, and flags any new entries or status changes (e.g., active → suspended, or new license issued). All data is stored in a structured relational database for fast querying.

2. **Dashboard Module** — A Django-powered web interface that presents the collected data in a clean, searchable format. Users can filter by license type, status, region, or date range. The dashboard also highlights recent changes and can send alerts when a tracked license is updated.

## Key Features

- **Daily automated data collection** from public government licensing sources
- **Change detection** — automatically identifies new licenses and status updates
- **Searchable web dashboard** with filters for license type, status, and date
- **License history tracking** — view the full timeline of any individual license
- **Alert system** for monitored licenses that change status
- **Scalable architecture** — easily extended to cover additional license categories or states

## Tech Stack

- **Python** for the web scraping and data pipeline
- **Django** for the backend web dashboard and REST API
- **PostgreSQL** for structured data storage and fast lookups
- **Celery + Cron** for scheduling and automating daily scrape jobs

## Use Cases

- Compliance teams verifying contractor or automotive license status before awarding contracts
- Businesses monitoring competitor licensing activity in their region
- Researchers and analysts tracking licensing trends across industries
- Legal and HR teams performing background verification

## Demo

A live demo is available for preview. You can log in and explore the dashboard with real data.

Live demo: [licensedb.etuannv.com](http://licensedb.etuannv.com)  
Username: `viewer` | Password: `etuannv2019demo`

---

👉 Need a custom license monitoring system for your industry? [Contact me](/contact) and let's discuss your requirements.

---

**See also:**
- [Best Proxies for Web Scraping](/posts/best-proxies-for-web-scraping/) — proxy solutions used in scraping projects
- [Price Tracking System](/projects/price-tracking-system/) — another automated data collection system
