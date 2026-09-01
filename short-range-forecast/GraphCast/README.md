# 沐曦GPU运行GraphCast说明文档

[GraphCast（现已改名为weathernext）](https://github.com/google-deepmind/weathernext)项目由第三方以Apache许可证发布，本文档说明如何在沐曦曦云C系列GPU上使用MACA JAX运行GraphCast制作短期天气预报。

## 一、GraphCast简介

GraphCast是Google DeepMind推出的一款基于图神经网络（GNN）的AI中期全球天气预报模型，它通过创新的图神经网络架构，证明了纯数据驱动的AI模型可以在速度和精度上超越传统物理模型，为天气预报带来了新的可能性。

## 二、GraphCast支持说明

ECMWF的[ai-models](https://github.com/ecmwf-lab/ai-models)是一个用于统一运行多个AI天气预报模型的Python/CLI框架，旨在让用户能利用ECMWF的开放数据，在个人的设备上便捷地运行前沿的AI气象预报大模型。该项目本身是一个统一的接口，而不包含某一个具体的天气AI模型，支持用第三方插件的方式安装和运行多种AI气象模型。本项目通过`ai-models`及GraphCast的插件[ai-models-graphcast](https://github.com/ecmwf-lab/ai-models-graphcast)实现对GraphCast模型的支持。

## 三、Dockerfile使用

本目录提供可直接构建的Dockerfile（位于short-range-forecast目录中），构建镜像前需要由使用者提前准备沐曦开发者社区提供的[maca-pytorch镜像](https://developer.metax-tech.com/softnova/docker?chip_name=%E6%9B%A6%E4%BA%91C500%E7%B3%BB%E5%88%97&package_name=maca-pytorch:3.8.2.6-torch2.8-py312-ubuntu24.04-amd64)。

### 3.1 构建镜像

构建镜像前，请从[沐曦资源中心](https://developer.metax-tech.com/softnova/search?package_name=maca-jax-0.4.34-py312-3.8.2.1-x86_64.tar.xz)下载 `maca-jax-0.4.34-py312-3.8.2.1-x86_64.tar.xz`，并与Dockerfile放在同一目录下。

执行命令：

```bash
docker build -f /path/to/Dockerfile -t ai-models-maca:3.8.2.6 .
```

### 3.2 GraphCast模型准备

GraphCast模型的权重文件较大，建议提前从[Google Cloud Bucket](https://console.cloud.google.com/storage/browser/dm_graphcast)提供的链接下载，并通过`-v`选项挂载到容器内。

### 3.3 启动容器

执行命令：

```bash
docker run -it --rm \
  --device=/dev/mxcd \
  --device=/dev/dri \
  --group-add video \
  --shm-size=8G \
  -v /path/to/GraphCast-weights:/opt/graphcast \
  ai-models-maca:3.8.2.6
```

### 3.4 运行GraphCast模型

`ai-models`支持多种数据源作为模型的输⼊，包括MARS、CDS和⾃定义的grib格式数据。其中MARS对⾮欧盟成员不可⽤，CDS在模型运⾏时通过⽹络下载ERA5(ECMWF Reanalysis version 5)数据。以CDS数据源为例，运行模型：

```bash
export AI_MODELS_ASSETS=/opt/graphcast
ai-models --input cds --date 20230110 --time 0000 graphcast
```

其中`--input`参数指定数据源，`--date`和`--time`参数指定ERA5数据下载的日期和时间，`graphcast`为模型名称。更多`ai-models`命令行参数的使用方式请参考[ai-models](https://github.com/ecmwf-lab/ai-models)。

> 注意：`ai-models`通过[CDS API](https://cds.climate.copernicus.eu/api-how-to)下载ERA5数据，使用前需要创建账号。

## 四、维护与支持

沐曦仅维护本文档中的MACA环境配置说明。GraphCast的源码、ai-models及ai-models-graphcast请参考：

* [GraphCast源码仓库（现已改名为weathernext）](https://github.com/google-deepmind/weathernext)
* [ai-models源码仓库](https://github.com/ecmwf-lab/ai-models)
* [ai-models-graphcast插件](https://github.com/ecmwf-lab/ai-models-graphcast)

---
本文档仅提供相关软件的配置与使用说明，不包含亦不分发前述软件的源代码或目标代码，且不涉及对其源代码的任何修改。您按照本文档配置、部署或使用相关软件时，应遵守适用许可证规定的条款及条件。相关软件的源代码、许可证全文、版权与归属声明及其他项目文档，请以其官方网站或原始发布页面为准。

Copyright (c) 2026 MetaX Integrated Circuits (Shanghai) Co., Ltd. All rights reserved.
