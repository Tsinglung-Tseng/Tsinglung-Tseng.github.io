---
title: kubeLinkerd笔记
key: 20180430
tags: k8s
---

## 1. 本地运行linkerd

git clone linkerd 仓库

linkerd 安装包包括：

* config 目录：linkerd 配置文件存放于该目录
* disco 目录：如果采用基于文件方式进行服务发现，相关配置信息存于该目录
* logs 目录：默认 linkerd 将所产生的日志写于该目录
* linkerd 可执行文件
* docs 文档目录

```bash
# qinglong @ hqkube-master-zeng in ~/linkerd_local/linkerd-1.3.7 [17:22:29]
$ tree .
.
├── config
│   └── linkerd.yaml
├── disco
│   ├── thrift-buffered
│   ├── thrift-framed
│   └── web
├── docs
│   ├── CHANGES.md
│   └── README.md
├── linkerd-1.3.7-exec
└── logs
    └── access.log

4 directories, 8 files

#本地运行linkerd
# qinglong @ hqkube-master-zeng in ~/linkerd_local/linkerd-1.3.7 [13:20:35]
$ ls
config  disco  docs  linkerd  linkerd-1.3.7-exec  logs

# qinglong @ hqkube-master-zeng in ~/linkerd_local/linkerd-1.3.7 [13:20:36]
$ ./linkerd-1.3.7-exec config/linkerd.yaml
-XX:+AggressiveOpts -XX:+CMSClassUnloadingEnabled -XX:CMSInitiatingOccupancyFraction=70 -XX:+CMSParallelRemarkEnabled -XX:+CMSScavengeBeforeRemark -XX:InitialHeapSize=33554432 -XX:MaxHeapSize=1073741824 -XX:MaxNewSize=357916672 -XX:MaxTenuringThreshold=6 -XX:OldPLABSize=16 -XX:+PrintCommandLineFlags -XX:+ScavengeBeforeFullGC -XX:-TieredCompilation -XX:+UseCMSInitiatingOccupancyOnly -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:+UseConcMarkSweepGC -XX:+UseParNewGC -XX:+UseStringDeduplication
四月 25, 2018 1:20:58 下午 com.twitter.finagle.http.HttpMuxer$ $anonfun$new$1
信息: HttpMuxer[/admin/metrics.json] = com.twitter.finagle.stats.MetricsExporter(<function1>)
四月 25, 2018 1:20:58 下午 com.twitter.finagle.http.HttpMuxer$ $anonfun$new$1
信息: HttpMuxer[/admin/per_host_metrics.json] = com.twitter.finagle.stats.HostMetricsExporter(<function1>)
I 0425 13:20:58.392 CST THREAD1: linkerd 1.3.7 (rev=7cc7b8dc69287f442c8f22fed04fb1910246b896) built at 20180405-143013
I 0425 13:20:58.837 CST THREAD1: Finagle version 7.1.0 (rev=37212517b530319f4ba08cc7473c8cd8c4b83479) built at 20170906-132024
I 0425 13:21:00.293 CST THREAD1: Tracer: com.twitter.finagle.zipkin.thrift.ScribeZipkinTracer
I 0425 13:21:00.328 CST THREAD1: connecting to usageData proxy at Set(Inet(stats.buoyant.io/104.28.23.233:443,Map()))
I 0425 13:21:00.596 CST THREAD1: serving http admin on /127.0.0.1:9990
I 0425 13:21:00.613 CST THREAD1: serving int on /0.0.0.0:4140
I 0425 13:21:00.640 CST THREAD1: serving /host/thrift-framed on /0.0.0.0:4141
I 0425 13:21:00.652 CST THREAD1: serving /host/thrift-buffered on /0.0.0.0:4142
I 0425 13:21:00.660 CST THREAD1: initialized
```

在宿主机的浏览器访问 http://127.0.0.1:9990 可进入 linkerd 的管理界面，从上面你可以看到一些信息如请求数量、成功率、失败率等，除此之外，还可以从页面进行 dtab（Delegation Table）调测以及调整日志打印级别。

## 基于docker的安装

为docker新建一个目录，并把本地linkerd的config和disco目录拷进去。

```bash
# cd ~/linkerd_startup/docker-linkerd 进入docker linkerd目录
# 复制传统安装的配置信息以便使用
$ cp -r ../linkerd-1.3.7/{config,disco} . 

# qinglong @ hqkube-master-zeng in ~/linkerd_startup/docker-linkerd [13:27:21]
$ ls
config  disco

# 挂载/disco和linkerd.yaml 进容器， 利用宿主机网路开启linkerd服务
$ sudo docker run --rm --name linkerd --network host -v `pwd`/disco:/disco -v `pwd`/config/linkerd.yaml:/linkerd.yaml  buoyantio/linkerd:1.3.7 /linkerd.yaml
```

## 2. Linkerd on k8s

### STEP 1: INSTALL LINKERD
* 在k8s上安装linkerd
```bash
kubectl apply -f https://raw.githubusercontent.com/linkerd/linkerd-examples/master/k8s-daemonset/k8s/servicemesh.yml
```
* 确认安装是否成功
```bash
kubectl get svc l5d -o jsonpath="{.status.loadBalancer.ingress[0].*}"

#这里有问题，loadbalancer路径下没有入口
#此处给了个ingress入口，配置文件也咩有设置过ingress。坑啊……
#=======
INGRESS_LB=$(kubectl get svc l5d -o jsonpath="{.status.loadBalancer.ingress[0].*}")
open http://$INGRESS_LB:9990
#=======

# 如果集群不支持外部负载均衡，用hostIP
$ kubectl get po -l app=l5d -o jsonpath="{.items[0].status.hostIP}"
192.168.1.177%

$ kubectl get svc l5d -o 'jsonpath={.spec.ports[2].nodePort}'
30001%

open http://192.168.1.177:30001 #即是dashboard入口
```
或者 viewing the Linkerd admin dashboard by visiting http://localhost:9990 in your browser.
```bash
$ kubectl get pod -l app=l5d -o jsonpath='{.items[0].metadata.name}'
l5d-vlpmf%

kubectl port-forward $(kubectl get pod -l app=l5d -o jsonpath='{.items[0].metadata.name}') 9990 &
```

上面这段有几个概念，loadBalancer的ingerss，pod的hostIP，service的nodePort和name。

admin dasnboard的访问方式这里有3种。port-forward需要kubectl proxy 的支持，<hostIP>:<nodePort> 则不需要。$INGRESS_LB:9990 根本无法访问（why -_-）

### STEP 2: INSTALL THE SAMPLE APPS
