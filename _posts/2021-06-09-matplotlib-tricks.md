---
layout: post
published: true
title: Matplotlib Tricks
subtitle: A Quick Table of Matplotlib Tricks
date: '2021-06-09'
---
## A Quick Table of Matplotlib Tricks

| Description | Code |
| ----------- | ----------- |
| Get style colors | `colors = plt.rcParams['axes.prop_cycle'].by_key()['color']`  |
| Plot lines with colormap | `color_val = (min_dst + 50) / np.min(min_dst + 50)` <br> `colors = plt.cm.jet(color_val)` <br> `fig, ax = plt.subplots(figsize=(8, 8))` <br> `for i in range(len(mins)):` <br> `print(i)` <br> `ax.plot(t, np.nanmean(min_mlat_sea[i], axis=1), color=colors[i], lw=color_val[i]+.1, alpha=.5)`|
| asdasd | asdasd |
