CUDA Development Environment
==================================

This image is for CUDA development OpenBayes.

Have
----------------------------------

- [x] CUDA 10.1
- [x] CUDA 10.0
- [x] CUDA 9.2
- [x] CUDA 9.1
- [x] CUDA 9.0
- [x] CUDA 8.0
- [x] Anaconda 3 2019.07
- [x] cmake 3.15.7
- [x] google test (ubuntu 18.04 apt)
- [x] google benchmark (githun master branch)

Key
-----------------------------------

### For CUDs


```Dockerfile
ENV NVIDIA_VISIBLE_DEVICES all
ENV NVIDIA_DRIVER_CAPABILITIES compute,utility
```

and

GPU-concerned libraries or environment.

### For OpenBayes

1. jupyter
2. https://s3.cn-north-1.amazonaws.com.cn/openbayes-releases/metrics-daemon/metrics-daemon_linux