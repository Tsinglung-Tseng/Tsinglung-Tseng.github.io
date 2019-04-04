The following NVIDIA® software must be installed on your system:

* NVIDIA® GPU drivers —CUDA 10.0 requires 410.x or higher.
* CUDA® Toolkit —TensorFlow supports CUDA 10.0 (TensorFlow >= 1.13.0)
* CUPTI ships with the CUDA Toolkit.
* cuDNN SDK (>= 7.4.1)
* (Optional) TensorRT 5.0 to improve latency and throughput for inference on some models.

## 安装方式

CUDA Toolkit 有两种安装方式: 

* Distribution-specific packages (RPM and Deb packages) #####
* A distribution-independent package (runfile packages)

It is recommended to use the distribution-specific packages, where possible.

```bash
# 查看可用版本
apt-cache policy cuda
```

折腾过程中不小心删掉了，cuda9.0的repo。导致apt更新失败
```bash
sudo dpkg --purge cuda-repo-<version>

sudo apt-get install cuda                                    # Ubuntu/ 也可以用这条命令升级cuda

```

## [Cuda](https://developer.nvidia.com/cuda-downloads?target_os=Linux&target_arch=x86_64&target_distro=Ubuntu&target_version=1804&target_type=deblocal)

[md5sum](https://developer.download.nvidia.cn/compute/cuda/10.1/Prod/docs/sidebar/md5sum.txt)

The fellow official [guidance](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/#ubuntu-installation) to install.

The packages installed by the packages above can also be installed individually by specifying their names explicitly. The list of available packages be can obtained with:

```bash
$ cat /var/lib/apt/lists/*cuda*Packages | grep "Package:"      # Ubuntu
```
