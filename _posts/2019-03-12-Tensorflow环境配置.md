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
```