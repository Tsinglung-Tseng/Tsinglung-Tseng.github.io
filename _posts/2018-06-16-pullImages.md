---
title: pullimages
key: 20180616
tags: Docker
---

![yo](http://wx4.sinaimg.cn/mw690/0078IDjtly1fr0fopxj8hj30qz076dgl.jpg)

查找需要的镜像，到[谷歌镜像库](https://console.cloud.google.com/gcr/images/google-containers/GLOBAL)。


docker proxy上面 yum 已经把要的包安装完了，可以将 yum 的科学上网代理删去，但是需要进一步配置 docker 的科学上网代理，配置方法很简单
```bash
vi /etc/systemd/system/docker.service.d/http-proxy.conf
[Service]
Environment="ALL_PROXY=socks5://192.168.1.128:8388/" "NO_PROXY=localhost,127.0.0.1,192.168.1.185,192.168.1.133"

systemctl daemon-reload
systemctl restart docker
```

## docker images fitted kubernetes 1.9.6
```bash
docker pull gcr.io/google-containers/kube-controller-manager-amd64:v1.9.6
docker pull gcr.io/google-containers/kube-apiserver-amd64:v1.9.6
docker pull gcr.io/google-containers/kube-scheduler-amd64:v1.9.6
docker pull gcr.io/google-containers/etcd-amd64:3.1.10
docker pull gcr.io/google-containers/kube-proxy-amd64:v1.9.6
docker pull gcr.io/google-containers/pause-amd64:3.0
docker pull gcr.io/google-containers/k8s-dns-sidecar-amd64:1.14.7
docker pull gcr.io/google-containers/k8s-dns-kube-dns-amd64:1.14.7
docker pull gcr.io/google-containers/k8s-dns-dnsmasq-nanny-amd64:1.14.7
```

# 本地docker镜像库 
安装：
```bash
#指定本地目录挂载到容器内，并指定自动重启
$ sudo docker run -d -p 5000:5000 --restart=always -v /opt/data/registry:/tmp/registry registry

#如果已经启动了则可以使用如下命令：
$ docker update --restart=always <CONTAINER ID>
```

使用镜像库：
```bash
#把一个本地镜像push到私有仓库中(use registry(a image) as example)
$ sudo docker tag registry 192.168.1.133:5000/registry
$ sudo docker push 192.168.1.133:5000/registry:tag
```

**首次使用需要修改docker启动配置文件**
```bash
# Create or modify /etc/docker/daemon.json , add:
{ "insecure-registries":["192.168.1.133:5000","192.168.1.185:5000"] }

# Restart docker daemon
sudo service docker restart

#通过下面地址，可以查询仓库中所有镜像(Do not use proxy)
http://192.168.1.133:5000/v2/_catalog
#和某个镜像的具体版本
http://192.168.1.133:5000/v2/srf_with_anaconda/tags/list
```

### 重新挂载 gluster
```bash
sudo docker run -v /etc/glusterfs:/etc/glusterfs:z \
                -v /var/lib/glusterd:/var/lib/glusterd:z \
                -v /var/log/glusterfs:/var/log/glusterfs:z \
                -v /sys/fs/cgroup:/sys/fs/cgroup:ro \
                -d —privileged=true —net=host —name=gluster-4.0.2  \
                -v /mnt:/mnt:rshared \
                -v /var/lib/heketi/:/var/lib/heketi \
                -v /dev/:/dev \
                192.168.1.133:5000/gluster-centos:v4.0.2
```