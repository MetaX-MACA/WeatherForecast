# 沐曦GPU运行NowcastNet V1.0说明文档

[NowcastNet](https://codeocean.com/capsule/3935105/tree/v1)项目由第三方以MIT许可证发布，本文档说明如何在沐曦曦云C系列GPU上使用MACA PyTorch运行NowcastNet制作短临预报。

## 一、NowcastNet简介

NowcastNet是一个专为极端降水事件设计的、基于雷达观测数据的短临预报(nowcasting)深度学习大模型，由清华大学联合中国气象局开发。

## 二、沐曦GPU环境配置与运行

### 2.1 环境准备

使用沐曦开发者社区提供的[maca-pytorch镜像](https://developer.metax-tech.com/softnova/docker?chip_name=%E6%9B%A6%E4%BA%91C500%E7%B3%BB%E5%88%97&package_name=maca-pytorch:3.8.2.6-torch2.8-py312-ubuntu24.04-amd64)启动容器：

```bash
docker run -it --name test-nowcastnet \
  --device=/dev/dri \
  --device=/dev/mxcd \
  --group-add video \
  --shm-size=8G \
  --security-opt seccomp=unconfined \
  --ulimit memlock=-1 \
  --ulimit stack=67108864 \
  -v /path/to/NowcastNET-data:/data \
  cr.metax-tech.com/public-library/maca-pytorch:3.8.2.6-torch2.8-py312-ubuntu24.04-amd64 \
  /bin/bash
```

**参数说明：**

- `--device=/dev/mxcd --device=/dev/dri`：挂载沐曦GPU设备
- `--group-add video`：添加video组以访问GPU
- `--shm-size=8G`：设置共享内存大小
- `-v`：挂载工作目录，`/path/to/NowcastNET-data`对应[NowcastNET源码仓库](https://codeocean.com/capsule/3935105/tree/v1)的`data`目录，需手动下载

### 2.2 验证环境

进入容器后，先验证沐曦PyTorch与GPU是否可用：

```bash
python -c "import torch; assert torch.cuda.is_available(); print('PyTorch version:', torch.__version__); print('CUDA OK:', torch.cuda.get_device_name(0))"
```

输出应显示已安装沐曦定制版 PyTorch，MACA 环境可用：：

```text
PyTorch version: 2.8.0+metax3.8.2.2
CUDA OK: MetaX C500
```

### 2.3 安装NowcastNet及依赖

#### 2.3.1 安装系统依赖

```bash
apt update && apt install -y --no-install-recommends git libglib2.0-0 libgl1 libgl1-mesa-dri libglapi-mesa libglib2.0-data libglvnd0 libglx-mesa0 libglx0
```

#### 2.3.2 下载NowcastNet源代码

```bash
cd /opt
git clone https://git.codeocean.com/capsule-3935105.git /opt/NowcastNET
```

#### 2.3.3 安装Python依赖

```bash
pip3 install cartopy opencv-python einops scikit-image numpy==1.26.4
```

### 2.4 运行NowcastNet

```bash
cd /opt/NowcastNET/code
bash mrms_case_test.sh
bash mrms_large_case_test.sh
```

运行结束后，会在`/results`目录下生成预测结果。

## 三、Dockerfile使用

本目录提供可直接构建的Dockerfile，构建镜像前需要由使用者提前准备沐曦开发者社区提供的[maca-pytorch镜像](https://developer.metax-tech.com/softnova/docker?chip_name=%E6%9B%A6%E4%BA%91C500%E7%B3%BB%E5%88%97&package_name=maca-pytorch:3.8.2.6-torch2.8-py312-ubuntu24.04-amd64)。

构建镜像：

```bash
docker build -f /path/to/Dockerfile -t nowcastnet-maca:v1 .
```

启动容器：

```bash
docker run -it --rm \
  --device=/dev/mxcd \
  --device=/dev/dri \
  --group-add video \
  --shm-size=8G \
  -v /path/to/NowcastNET-data:/data \
  nowcastnet-maca:v1
```

其中，`/path/to/NowcastNET-data`对应[NowcastNET源码仓库](https://codeocean.com/capsule/3935105/tree/v1)的`data`目录，需手动下载。

执行命令：

```bash
cd code
bash mrms_case_test.sh
bash mrms_large_case_test.sh
```

## 四、维护与支持

沐曦仅维护本文档中的MACA环境配置说明。NowcastNet的源码、数据和论文请参考：

* [NowcastNET源码仓库](https://codeocean.com/capsule/3935105/tree/v1)
* [NowcastNET论文](https://www.nature.com/articles/s41586-023-06184-4.pdf)

---
本文档仅提供相关软件的配置与使用说明，不包含亦不分发前述软件的源代码或目标代码，且不涉及对其源代码的任何修改。您按照本文档配置、部署或使用相关软件时，应遵守适用许可证规定的条款及条件。相关软件的源代码、许可证全文、版权与归属声明及其他项目文档，请以其官方网站或原始发布页面为准。

Copyright (c) 2026 MetaX Integrated Circuits (Shanghai) Co., Ltd. All rights reserved.
