---
layout: post
title: Dump Error info to file (Python)
author: gstarr
categories:
  - Python
date: 'Fri Dec 27 2019 18:11:59 GMT-0500 (Eastern Standard Time)'
published: true
---
Call the following function inside an exception handler to collect the error and dump all of the relevant info to a file called `error.txt`. This includes the full stack trace. Very helpful!

```python
def dump_error(timestamped_dir):
    exc_type, exc_value, exc_traceback = sys.exc_info()
    traceback.print_tb(exc_traceback)
    traceback.print_exception(exc_type, exc_value, exc_traceback)
    traceback.print_exc()
    print(repr(traceback.extract_tb(exc_traceback)))
    print(repr(traceback.format_tb(exc_traceback)))
    with open(os.path.join(timestamped_dir, 'error.txt'), 'w') as f:
        traceback.print_tb(exc_traceback, file=f)
        f.write('\n\n')
        traceback.print_exception(exc_type, exc_value, exc_traceback, file=f)
        f.write('\n\n')
        traceback.print_exc(file=f)
        f.write('\n\n')
        f.write(repr(traceback.extract_tb(exc_traceback)))
        f.write('\n\n')
        f.write(repr(traceback.format_tb(exc_traceback)))
```


![](/assets/capture.png)
