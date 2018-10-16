---
title: Command line tricks 2
key: 20180927
tags: 法器集合
---

### LC_CTYPE cannot set
```bash
# Insert into /etc/default/locale:
$ sudo vim /etc/default/locale

LC_CTYPE="en_US.UTF-8"
LC_ALL="en_US.UTF-8"
LANG="en_US.UTF-8"
```

### docker 内安装 sshd
```Dockerfile
FROM ubuntu:16.04

RUN apt-get update && apt-get install -y openssh-server
RUN mkdir /var/run/sshd
RUN echo 'root:screencast' | chpasswd
RUN sed -i 's/PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config

# SSH login fix. Otherwise user is kicked off after login
RUN sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd

ENV NOTVISIBLE "in users profile"
RUN echo "export VISIBLE=now" >> /etc/profile

EXPOSE 22
CMD ["/usr/sbin/sshd", "-D"]
```

```bash
$ docker run -d -P --name test_sshd eg_sshd
$ docker port test_sshd 22

0.0.0.0:49154

# 此处可能还需要进入容器执行：
$ service ssh restart
```

### [解决 Dockerfile 中 source 不生效](https://codeday.me/bug/20170702/31755.html)
```Dockerfile
RUN rm /bin/sh && ln -s /bin/bash /bin/sh
```

### git 
git pull wants you to either remove or save your current work so that the merge it triggers doesn't cause conflicts with your uncommitted work. Note that you should only need to remove/save untracked files if the changes you're pulling create files in the same locations as your local uncommitted files.

```bash
# Remove your uncommitted changes
## Tracked files
git checkout -f
## Untracked files
git clean -fd
# Save your changes for later
## Tracked files
git stash
## Tracked files and untracked files
git stash -u
## Reapply your latest stash after git pull:
git stash pop
```

### 逻辑运算集合图

![](https://cl.ly/c96720801a53/download/283cc23ed0431a3aa1d371d531606748.jpg)

### 对 numpy 数组进行 strip 操作
**2018.09.29 update**

今天在做仿真的时候遇到一个问题，之前把 phantom 的灰度值存进 h5 的时候为了使数组长度相同使用了 pad，在数组后加了数量不一的0或-1. 重建的时候需要把它们再除去。

```python
>>> ds.root.sheppLogans[123][0]
array([  0., 240., 235.,   5., 248., 243., 250., 245., 253., 255.,  -1.,
        -1.,  -1.,  -1.,  -1.,  -1.,  -1.,  -1.,  -1.,  -1.],
      dtype=float32)

>>> def trim(input_array, trim = -1):
>>>     return input_array[~np.in1d(input_array, trim).reshape(input_array.shape)]

>>> trim(ds.root.sheppLogans[123][0], -1)
array([  0., 240., 235.,   5., 248., 243., 250., 245., 253., 255.],
      dtype=float32)
```

### Vectoring python 
**2018.09.20 update**

```python
x, y = np.meshgrid(range(canvas.shape[0]), range(canvas.shape[1]))
mask = (x - p[0])**2 + (y - p[1])**2 <= r**2
canvas[mask] = level
```

### LC_ALL 
LC_ALL=C 是为了去除所有本地化的设置，让命令能正确执行。在Linux中通过locale来设置程序运行的不同语言环境，locale由ANSI C提供支持。

locale的命名规则为<语言>_<地区>.<字符集编码>，如zh_CN.UTF-8，zh代表中文，CN代表大陆地区，UTF-8表示字符集。

在locale环境中，有一组变量，代表国际化环境中的不同设置：
1.    LC_COLLATE
定义该环境的排序和比较规则
2.    LC_CTYPE
用于字符分类和字符串处理，控制所有字符的处理方式，包括字符编码，字符是单字节还是多字节，如何打印等。是最重要的一个环境变量。
3.    LC_MONETARY
货币格式
4.    LC_NUMERIC
非货币的数字显示格式
5.    LC_TIME
时间和日期格式
6.    LC_MESSAGES
提示信息的语言。另外还有一个LANGUAGE参数，它与LC_MESSAGES相似，但如果该参数一旦设置，则LC_MESSAGES参数就会失效。LANGUAGE参数可同时设置多种语言信息，如LANGUANE="zh_CN.GB18030:zh_CN.GB2312:zh_CN"。
7.    LANG
LC_*的默认值，是最低级别的设置，如果LC_*没有设置，则使用该值。类似于 LC_ALL。
8.    LC_ALL
它是一个宏，如果该值设置了，则该值会覆盖所有LC_*的设置值。注意，LANG的值不受该宏影响。
C"是系统默认的locale，"POSIX"是"C"的别名。所以当我们新安装完一个系统时，默认的locale就是C或POSIX。

### set
set - Set or unset values of shell options and positional parameters.

### Working with Random Numbers in Python
```python
>>> import random
>>> random.random()
0.11981376476232541
>>> random.random()
0.757859420322092
>>> random.random()
0.7384012347073081
```

* Generating Random Ints Between x and y
```python
>>> import random
>>> random.randint(1, 10)           #[x, y] 
10
>>> random.randrange(1, 10)         #[x, y)
5
>>> random.uniform(1, 10)           #  float numbers that lie within a specifc [x, y] 
7.850184644194309

>>> items = ['one', 'two', 'three', 'four', 'five']
>>> random.shuffle(items)
>>> items
['four', 'one', 'five', 'three', 'two']

>>> items = ['one', 'two', 'three', 'four', 'five']
>>> random.sample(items, 3)
['one', 'five', 'two']
>>> random.sample(items, 3)
['five', 'four', 'two']
```

