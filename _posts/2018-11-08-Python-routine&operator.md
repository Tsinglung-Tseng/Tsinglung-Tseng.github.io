## 需求分析

phantom 系统仿真需要大量重复跑许多次。每次仿真任务分4种尺度，每种101个子任务，分别在各自的工作。需要一个自动化的流程完成一连串的重建任务。

## Python 操作文件系统

```python
import pathlib  
import os.path 

import glob # 用 unix shell 的规则找到所有 match pattern 的路径。

```

Maybe you need to list all files in a directory of a given type, find the parent directory of a given file, or create a unique file name that does not already exist.

但是使用这些工具操作文件系统会很麻烦，逻辑无法抽离出来。

## pathlib

