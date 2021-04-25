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
* Use e.g. `python -m madrigalWeb.globalDownload --verbose --url=http://cedar.openmadrigal.org --outputDir="E:/dmsp" --user_fullname="Gregory Starr" --user_email=gstarr@bu.edu --user_affiliation="Boston University" --format="hdf5" --startDate="01/01/2010" --endDate="12/31/2021" --inst=8100 --kindat=10108,10109,10110,10111,10112,10113,10114,10115,10116,10117,10118,10119`

### DMSP Special Sensor Ultraviolet Spectrographic Imager (SSUSI)
* https://ssusi.jhuapl.edu/
* Several different data products from three satellites: f16, f17, f18
* pretty good data availability for 2000-
* I used it for auroral boundaries
* Use WinSCP
    * File Protocol: FTP
    * Encyption: TLS / SSL Implicit
    * Host name: spdf.gsfc.nasa.gov
    * Port: 990?
    * Anonymous Login: yes

### SWARM
* 2 Hz data, 3 Satellites
* Use WinSCP
    * File Protocol: FTP
    * Encyption: No encryption
    * Host name: swarm-diss.eo.esa.int
    * Anonymous Login: yes

### SuperMag
* https://supermag.jhuapl.edu/
* can download long dataset of indices
* downloading long dataset of magnetic field measurements is difficult

### OMNI Solar Wind Parameters
* includes solar wind parameters: B field, speed, temperature etc.
* includes geomagnetic indices: Kp, ap, Dst, etc
* Low resolution (1 hour averages) https://spdf.gsfc.nasa.gov/pub/data/omni/low_res_omni/
* high resolution (1 min) https://omniweb.gsfc.nasa.gov/ow_min.html