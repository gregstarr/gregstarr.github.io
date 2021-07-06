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
| asdasd | asdasd |

* https://stackoverflow.com/questions/27092991/white-lines-in-matplotlibs-pcolor
