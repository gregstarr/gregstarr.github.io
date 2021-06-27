---
layout: post
published: true
title: Python Compilation Error
subtitle: quick fix
date: 6/27/2021
---
## Error

Something like this:
```
cannot find -lmsvcr140
    collect2.exe: error: ld returned 1 exit status
    error: command gcc.exe failed with exit status 1
```

## Solution

`pip install libpython`