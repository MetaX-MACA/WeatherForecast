# 沐曦GPU运行FourCastNet V2说明文档

FourCastNet V2模型是一个天气预报大模型，本文档说明如何在沐曦曦云C系列GPU上使用MACA PyTorch运行FourCastNet V2制作短期天气预报。

## 一、FourCastNet V2简介

FourCastNet V2是NVIDIA在FourCastNet基础上推出的升级版本，其核心改进在于引入了球面傅里叶神经算子（SFNO）。这一改进将模型从“平面”思维转向了“球面”思维，旨在提升预报的物理精确性与长期稳定性。

## 二、FourCastNet V2支持说明

ECMWF的[ai-models](https://github.com/ecmwf-lab/ai-models)是一个用于统一运行多个AI天气预报模型的Python/CLI框架，旨在让用户能利用ECMWF的开放数据，在个人的设备上便捷地运行前沿的AI气象预报大模型。该项目本身是一个统一的接口，而不包含某一个具体的天气AI模型，支持用第三方插件的方式安装和运行多种AI气象模型。本项目通过`ai-models`及FourCastNet V2的插件[ai-models-fourcastnetv2](https://github.com/ecmwf-lab/ai-models-fourcastnetv2)实现对FourCastNet V2模型的支持。

## 三、Dockerfile使用

本目录提供可直接构建的Dockerfile（位于short-range-forecast目录中），构建镜像前需要由使用者提前准备沐曦开发者社区提供的[maca-pytorch镜像](https://developer.metax-tech.com/softnova/docker?chip_name=%E6%9B%A6%E4%BA%91C500%E7%B3%BB%E5%88%97&package_name=maca-pytorch:3.8.2.6-torch2.8-py312-ubuntu24.04-amd64)。

### 3.1 构建镜像

构建镜像前，请从[沐曦资源中心](https://developer.metax-tech.com/softnova/search?package_name=maca-jax-0.4.34-py312-3.8.2.1-x86_64.tar.xz)下载 `maca-jax-0.4.34-py312-3.8.2.1-x86_64.tar.xz`，并与Dockerfile放在同一目录下。

执行命令：

```bash
docker build -f /path/to/Dockerfile -t ai-models-maca:3.8.2.6 .
```

### 3.2 FourCastNet V2模型准备

FourCastNet V2模型的权重文件较大，建议提前从ai-models-fourcastnetv2插件提供的[链接](https://sites.ecmwf.int/repository/ai-models/test-data/fourcastnetv2/small)下载，并通过`-v`选项挂载到容器内。需要下载的文件为`weights.tar`、`global_means.npy`和`global_stds.npy`。

### 3.3 启动容器

执行命令：

```bash
docker run -it --rm \
  --device=/dev/mxcd \
  --device=/dev/dri \
  --group-add video \
  --shm-size=8G \
  -v /path/to/FourCastNetV2-weights:/opt/fourcastnetv2 \
  ai-models-maca:3.8.2.6
```

### 3.4 运行FourCastNet V2模型

`ai-models`支持多种数据源作为模型的输⼊，包括MARS、CDS和⾃定义的grib格式数据。其中MARS对⾮欧盟成员不可⽤，CDS在模型运⾏时通过⽹络下载ERA5(ECMWF Reanalysis version 5)数据。以CDS数据源为例，运行模型：

```bash
export AI_MODELS_ASSETS=/opt/fourcastnetv2
ai-models --input cds --date 20230110 --time 0000 fourcastnetv2-small
```

其中`--input`参数指定数据源，`--date`和`--time`参数指定ERA5数据下载的日期和时间，`fourcastnetv2-small`为模型名称。更多`ai-models`命令行参数的使用方式请参考[ai-models](https://github.com/ecmwf-lab/ai-models)。

> 注意：`ai-models`通过[CDS API](https://cds.climate.copernicus.eu/api-how-to)下载ERA5数据，使用前需要创建账号。

## 四、维护与支持

沐曦仅维护本文档中的MACA环境配置说明。ai-models及ai-models-fourcastnetv2请参考：

* [ai-models源码仓库](https://github.com/ecmwf-lab/ai-models)
* [ai-models-fourcastnetv2插件](https://github.com/ecmwf-lab/ai-models-fourcastnetv2)

---
本文档仅提供相关软件的配置与使用说明，不包含亦不分发前述软件的源代码或目标代码，且不涉及对其源代码的任何修改。您按照本文档配置、部署或使用相关软件时，应遵守适用许可证规定的条款及条件。相关软件的源代码、许可证全文、版权与归属声明及其他项目文档，请以其官方网站或原始发布页面为准。

Copyright (c) 2026 MetaX Integrated Circuits (Shanghai) Co., Ltd. All rights reserved.
