---
layout: post
published: true
title: Space Physics Data
date: '2021-04-24'
subtitle: Where to find and how to download
image: assets/tec_thumb.png
---
## Downloading Space Physics Data


### Global TEC Maps
* global tec in lat/lon bins every 5 minutes
* Use e.g. `python -m madrigalWeb.globalDownload --verbose --url=http://cedar.openmadrigal.org --outputDir=/tmp --user_fullname="Gregory+Starr" --user_email=gregstarr2@gmail.com --user_affiliation="none" --format="hdf5" --startDate="01/01/1950" --endDate="12/31/2021" --inst=8000 --kindat=3500 `

### DMSP Ion Drift Meter
* ion drift measured along polar orbits
* Use e.g. `python -m madrigalWeb.globalDownload --verbose --url=http://cedar.openmadrigal.org --outputDir="E:/dmsp" --user_fullname="Gregory Starr" --user_email=gstarr@bu.edu --user_affiliation="Boston University" --format="hdf5" --startDate="01/01/2010" --endDate="12/31/2021" --inst=8100 --kindat=10108,10109,10110,10111,10112,10113,10114,10115,10116,10117,10118,10119 `

### SWARM Ne
* 2 Hz electron density data, 3 Satellites
* FTP: Use WinSCP
