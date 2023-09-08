---
layout: post
title: python context
date: 2020-05-13
description: 
tags: python
categories: programming
---
## python context manager
更好地管理资源，清理资源。

### context class

```
class Open_File():

    def __init__(self, destination):
        pass

    def __enter__(self):
        pass

    def __exit__(self, exc_type, exc_val, traceback):
        pass

```

### context function

```
import os
from contextlib import contextmanager


@contextmanager
def change_dir(destination):
    try:
        cwd = os.getcwd()
        os.chdir(destination)
        yield
    finally:
        os.chdir(cwd)


with change_dir('Sample-Dir-One'):
    print(os.listdir())

with change_dir('Sample-Dir-Two'):
    print(os.listdir())

```

```
@contextmanager
def open_file(file, mode):
    f = open(file, mode)
    yield f
    f.close()


with open_file('sample.txt', 'w') as f:
    f.write('Lorem ipsum dolor sit amet, consectetur adipiscing elit.')

print(f.closed)
```
