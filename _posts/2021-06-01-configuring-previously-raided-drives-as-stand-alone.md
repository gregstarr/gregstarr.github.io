---
layout: post
published: true
title: Configuring previously RAIDed drives as stand-alone
date: '2021-06-01'
---
## Tries
* Windows drive partition manager tool doesn't recognize the drives
* I tried using the seagate utilities but they also didn't recognize
* `sg_scan` lists the drives properly, but `sg_format --format DRIVE` fails
* I even ran DBAN on the drives which took ~55 hours

## Solution
* try running `sg_format -t 1 -Q --format DRIVE`
