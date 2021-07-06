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
| Plot lines with colormap | <code>colors = plt.cm.jet(values_0_to_1)<br>fig, ax = plt.subplots()<br>for i in range(len(lines)):<br>&nbsp;ax.plot(x, lines[i], color=colors[i])</code>|
| pcolormesh with alpha < 1 | `pcm = plt.pcolormesh(X, Y, C, alpha=.5, edgecolors='none', linewidth=0, antialiased=True)` |
| multiple polar plots with single colorbar | <code>fig = plt.figure(tight_layout=True)<br>gs = plt.GridSpec(1, 3, width_ratios=[30, 30, 1])<br>ax = fig.add_subplot(gs[0], polar=True)<br>ax.pcolormesh(C)<br>plt.colorbar(pcm, cax=fig.add_subplot(gs[-1]))</code> |
