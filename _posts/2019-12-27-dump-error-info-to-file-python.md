---
title: Dump Error info to file (Python)
author: gstarr
categories:
  - Python
date: 2019-12-27T23:11:59.934Z
thumbnail: /assets/uploads/capture.png
---
Call the following function inside an exception handler to collect the error and dump all of the relevant info to a file called `error.txt`. This includes the full stack trace. Very helpful!

```
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
        f.write(repr(traceback.format_tb(exc_traceback)))def dump_error(timestamped_dir):
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


![](capture.png)
