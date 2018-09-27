---
title: Command line tricks
key: 20180715
tags: 法器集合
---

## Yum tricks

```bash
#添加proxy

sudo vim /etc/yum.conf
```

## mount nas
```bash
#mac
mount -t smbfs //<username>:<password>@192.168.1.121/hqlabshare /Users/vinc/hqlabshare
mount -t smbfs //<username>:<password>@192.168.1.121/share /Users/vinc/share
mount -t smbfs //<username>:<password>@192.168.1.121/longlong /Users/vinc/longlong

#linux
sudo mount -t cifs //192.168.1.121/hqlabshare /home/hqlabadmin/hqlabshare --verbose -o user=hqlabadmin,password=nb408a
```

## gpu加速的tf jupyter
```bash
sudo nvidia-docker run -it --name tf-notebook -d -p 8888:8888 -v /home/hqlabadmin/tf_notebook:/notebooks tensorflow/tensorflow:1.9.0-rc1-devel-gpu-py3
```

## 普通用户，加入docker用户组，去掉sudo
操作顺序:
```bash
$ sudo cat /etc/group | grep docker
#如果不存在docker组，可以添加
$ sudo groupadd docker
#添加当前用户到docker组
$ sudo gpasswd -a ${USER} docker
#重启docker服务
$ sudo systemctl restart docker
#如果权限不够
$ sudo chmod a+rw /var/run/docker.sock
```

## 获取所有容器的ip
```bash
docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' $(docker ps -q)

nvidia-docker run -it --rm -v ~/minimum:/minimum 192.168.1.129:5000/srf_with_anaconda:v2.11
```

## 查找json路径下的value
--format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}'

## ssh免密登陆
这里以登陆Google云服务器为例。

**方法1:导入公钥**
进入谷歌云平台页面 -> 计算引擎 -> 元数据 -> SSH 密钥，粘贴保存
谷歌就会把上面这段 public key 写入到 ~/.ssh/authorized_keys

经过实验，谷歌确实会把密钥写进去，但是不知道为啥依然无法连上。

**方法2:**
```bash
#手动写入私钥。然而登陆出现了这个问题：
Permission denied (publickey).

# 之所以会出现这种情况，因为谷歌默认把密码验证登录关了，需要自行打开
$ sudo vi /etc/ssh/sshd_config
PasswordAuthentication yes 
:wq!

# 改完要重启 ssh 服务
$ sudo service sshd restart
```

## /proc目录
![yo](http://wx3.sinaimg.cn/mw690/0078IDjtly1fr1xtumgboj31670vdtfs.jpg)

## kill
![](https://ws2.sinaimg.cn/large/006tKfTcgy1fsoxjhaaqfj316o0qegqu.jpg)

## find
![](https://ws3.sinaimg.cn/large/006tKfTcgy1fsoxjikzo7j316o0rh444.jpg)

## misc commands I
![](https://ws4.sinaimg.cn/large/006tKfTcgy1fsoxjgynxrj30ws0kajuj.jpg)

## bash tricks
![](https://ws3.sinaimg.cn/large/006tKfTcgy1fsoxjgo2k5j30w00jojuc.jpg)

## awk
![](https://ws3.sinaimg.cn/large/006tKfTcgy1fsoxjg79hxj315i0qmwjw.jpg)

## cat & friends
![](https://cl.ly/361B3P3z1W17/Image%202018-07-15%20at%202.59.57%20PM.png)

## 设置永久alias
更改~/.bashrc或/etc/bashrc，两者的区别，前者是针对单用户，后者针对全局用户。

## k8s tricks
kubectl get .....
| whole | 缩写 | 注释 |
| :--- | :-----:| ---:|
| all |  | 
| certificatesigningrequests | (aka 'csr') | 证书签名请求 |
| clusterrolebindings |  | 
| clusterroles | 
| componentstatuses  | (aka 'cs') |  组件状态 |
| configmaps |  (aka 'cm')
| controllerrevisions | 
| cronjobs | 
| customresourcedefinition |  (aka 'crd')
| daemonsets |  (aka 'ds')
| deployments |  (aka 'deploy')
| endpoints  | (aka 'ep')
| events |  (aka 'ev')
| horizontalpodautoscalers |  (aka 'hpa')
| ingresses |  (aka 'ing')
| jobs | 
| limitranges | (aka 'limits')
| namespaces |  (aka 'ns')
| networkpolicies |  (aka 'netpol')
| nodes |  (aka 'no')
| persistentvolumeclaims |  (aka 'pvc')
| persistentvolumes |  (aka 'pv')
| poddisruptionbudgets |  (aka 'pdb')
| podpreset | 
| pods |  (aka 'po')
| podsecuritypolicies |  (aka 'psp')
| podtemplates | 
| replicasets |  (aka 'rs')
| replicationcontrollers |  (aka 'rc')
| resourcequotas |  (aka 'quota')
| rolebindings | 
| roles | 
| secrets | 
| serviceaccounts |  (aka 'sa')
| services |  (aka 'svc')
| statefulsets |  (aka 'sts')
| storageclasses |  (aka 'sc')

## cnetos安装zsh
```bash
#1. 安装zsh包
yum -y install zsh

#2. 切换默认shell为zsh
chsh -s /bin/zsh

#3. 重启服务器让修改的配置生效
#这个没什么好多说的，我习惯在web端控制台直接重启。

#4. 安装on my zsh
curl
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
wget
sh -c "$(wget https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"

#实际上到这一步oh my zsh就已经安装完成了。

#5. 修改oh my zsh 主题

#查看oh my zsh主题
ls ~/.oh-my-zsh/themes
#显示如下

adben.zsh-theme          gallois.zsh-theme          nicoulaj.zsh-theme
af-magic.zsh-theme       garyblessington.zsh-theme  norm.zsh-theme
afowler.zsh-theme        gentoo.zsh-theme           obraun.zsh-theme
agnoster.zsh-theme       geoffgarside.zsh-theme     peepcode.zsh-theme
alanpeabody.zsh-theme    gianu.zsh-theme            philips.zsh-theme
amuse.zsh-theme          gnzh.zsh-theme             pmcgee.zsh-theme
apple.zsh-theme          gozilla.zsh-theme          pure.zsh-theme
arrow.zsh-theme          half-life.zsh-theme        pygmalion.zsh-theme
#修改主题
vim ~/.zshrc
#可以看到默认的主题是ZSH_THEME="robbyrussell" 改成自己喜欢的即可
```

## 查看系统及硬件信息
```bash
# 系统
uname -a               # 查看内核/操作系统/CPU信息
head -n 1 /etc/issue   # 查看操作系统版本
cat /proc/cpuinfo      # 查看CPU信息
hostname               # 查看计算机名
lspci -tv              # 列出所有PCI设备
lsusb -tv              # 列出所有USB设备
lsmod                  # 列出加载的内核模块
env                    # 查看环境变量

# 资源
free -m                # 查看内存使用量和交换区使用量
df -h                  # 查看各分区使用情况
du -sh <目录名>        # 查看指定目录的大小
grep MemTotal /proc/meminfo   # 查看内存总量
grep MemFree /proc/meminfo    # 查看空闲内存量 
uptime                 # 查看系统运行时间、用户数、负载
cat /proc/loadavg      # 查看系统负载 -->

# 磁盘和分区
mount | column -t      # 查看挂接的分区状态
fdisk -l               # 查看所有分区
swapon -s              # 查看所有交换分区
hdparm -i /dev/hda     # 查看磁盘参数(仅适用于IDE设备)
dmesg | grep IDE       # 查看启动时IDE设备检测状况

# 网络
ifconfig               # 查看所有网络接口的属性
iptables -L            # 查看防火墙设置
route -n               # 查看路由表
netstat -lntp          # 查看所有监听端口
netstat -antp          # 查看所有已经建立的连接
netstat -s             # 查看网络统计信息

# 进程
ps -ef                 # 查看所有进程
top                    # 实时显示进程状态

# 用户
w                      # 查看活动用户
id <用户名>            # 查看指定用户信息
last                   # 查看用户登录日志
cut -d: -f1 /etc/passwd   # 查看系统所有用户
cut -d: -f1 /etc/group    # 查看系统所有组
crontab -l             # 查看当前用户的计划任务

# 服务
chkconfig --list       # 列出所有系统服务
chkconfig --list | grep on    # 列出所有启动的系统服务

#程序
rpm -qa                # 查看所有安装的软件包

# 查看CPU信息（型号） 
cat /proc/cpuinfo | grep name | cut -f2 -d: | uniq -c 
    8  Intel(R) Xeon(R) CPU            E5410   @ 2.33GHz 
#(看到有8个逻辑CPU, 也知道了CPU型号) 

cat /proc/cpuinfo | grep physical | uniq -c 
      4 physical id      : 0 
      4 physical id      : 1 
#(说明实际上是两颗4核的CPU) 

getconf LONG_BIT 
   32 
#(说明当前CPU运行在32bit模式下, 但不代表CPU不支持64bit) 

cat /proc/cpuinfo | grep flags | grep ' lm ' | wc -l 
   8 
#(结果大于0, 说明支持64bit计算. lm指long mode, 支持lm则是64bit) 


#再完整看cpu详细信息, 不过大部分我们都不关心而已. 
dmidecode | grep 'Processor Information' 

#查看内 存信息 
cat /proc/meminfo 

uname -a 
#Linux euis1 2.6.9-55.ELsmp #1 SMP Fri Apr 20 17:03:35 EDT 2007 i686 i686 i386 GNU/Linux (查看当前操作系统内核信息) 

cat /etc/issue | grep Linux 
#Red Hat Enterprise Linux AS release 4 (Nahant Update 5) (查看当前操作系统发行版信息) 

#查看机器型号 
dmidecode | grep "Product Name"  

#查看网卡信息 
dmesg | grep -i eth
```

## vim显示行号
```bash
# 仅让当前用户显示行号
vim ~/.vimrc
set nu
# 加载配置
source .vimrc

# 让所有用户显示行号
vim /etc/vimrc
```

## 清除早已退出的容器
```bash
docker ps --filter "status=exited" | \
grep 'weeks ago' | awk '{print $1}' | \
xargs --no-run-if-empty docker rm
```