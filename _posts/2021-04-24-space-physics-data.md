---
layout: post
published: true
title: Space Physics Data
date: 4/24/2021
subtitle: Where to find and how to download
image: assets/preprocessing.png
---
## Downloading Space Physics Data

| Instrument | Description | Url  | Comments |
| :---       |      ----:  | ---: |     ---: |
| Global TEC Maps | | global tec in lat/lon bins every 5 minutes | Use `python -m madrigalWeb.globalDownload` | |
| DMSP Ion Drift Meter | ion drift measured along polar orbits | Use `python -m madrigalWeb.globalDownload` | |
| SWARM Ne | 2 Hz electron density data, 3 Satellites | FTP | Use WinSCP |
