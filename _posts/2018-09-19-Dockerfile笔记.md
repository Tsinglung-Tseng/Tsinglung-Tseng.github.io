---
title: Dockerfile笔记
key: 20180919
tags: Docker
---

## 镜像的构建

在镜像的构建过程中，Docker 会遍历 Dockerfile 文件中的指令，然后按顺序执行。在执行每条指令之前，Docker 都会在缓存中查找是否已经存在可重用的镜像，如果有就使用现存的镜像，不再重复创建。如果你不想在构建过程中使用缓存，你可以在 docker build 命令中使用 --no-cache=true 选项。

* 从一个基础镜像开始（FROM 指令指定），下一条指令将和该基础镜像的所有子镜像进行匹配，检查这些子镜像被创建时使用的指令是否和被检查的指令完全一样。如果不是，则缓存失效。
* 在大多数情况下，只需要简单地对比 Dockerfile 中的指令和子镜像。然而，有些指令需要更多的检查和解释。
对于 ADD 和 COPY 指令，镜像中对应文件的内容也会被检查，每个文件都会计算出一个校验和。文件的最后修改时间和最后访问时间不会纳入校验。在缓存的查找过程中，会将这些校验和和已存在镜像中的文件校验和进行对比。如果文件有任何改变，比如内容和元数据，则缓存失效。
* 除了 ADD 和 COPY 指令，缓存匹配过程不会查看临时容器中的文件来决定缓存是否匹配。例如，当执行完 RUN apt-get -y update 指令后，容器中一些文件被更新，但 Docker 不会检查这些文件。这种情况下，只有指令字符串本身被用来匹配缓存。
* 一旦缓存失效，所有后续的 Dockerfile 指令都将产生新的镜像，缓存不会被使用。

## Dockerfile

### RUN
不要使用 RUN apt-get upgrade 或 dist-upgrade，因为许多基础镜像中的「必须」包不会在一个非特权容器中升级。如果基础镜像中的某个包过时了，比如 foo，使用 apt-get install -y foo 会自动升级 foo 包。

**永远将 RUN apt-get update 和 apt-get install 组合成一条 RUN 声明**
```Dockerfile
RUN apt-get update && apt-get install -y \
        package-bar \
        package-baz \
        package-foo=1.3.*   # cache busting
        && rm -rf /var/lib/apt/lists/*   # 清理掉 apt 缓存 var/lib/apt/lists 可以减小镜像大小
```

### CMD
多数情况下，CMD 都需要一个交互式的 shell (bash, Python, perl 等)，例如 CMD ["perl", "-de0"]，或者 CMD ["PHP", "-a"]。使用这种形式意味着，当你执行类似 docker run -it python 时，你会进入一个准备好的 shell 中。CMD 应该在极少的情况下才能以 CMD ["param", "param"] 的形式与 ENTRYPOINT 协同使用，除非你和你的镜像使用者都对 ENTRYPOINT 的工作方式十分熟悉。

### ENV
为了方便新程序运行，你可以使用 ENV 来为容器中安装的程序更新 PATH 环境变量。例如使用 ENV PATH /usr/local/nginx/bin:$PATH 来确保 CMD ["nginx"] 能正确运行。

### ADD & COPY
虽然 ADD 和 COPY 功能类似，但一般优先使用 COPY。因为它比 ADD 更透明。COPY 只支持简单将本地文件拷贝到容器中，而 ADD 有一些并不明显的功能（比如本地 tar 提取和远程 URL 支持）。

如果你的 Dockerfile 有多个步骤需要使用上下文中不同的文件。单独 COPY 每个文件，而不是一次性的 COPY 所有文件，这将保证每个步骤的构建缓存只在特定的文件变化时失效。
```Dockerfile
RUN mkdir -p /usr/src/things \
    && curl -SL http://example.com/big.tar.xz \
    | tar -xJC /usr/src/things \
    && make -C /usr/src/things all # 使用的管道操作，可以避免删除中间文件。
```
