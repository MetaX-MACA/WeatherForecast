##The 3-Clause BSD License
#
# Copyright (c) 2026 MetaX Integrated Circuits (Shanghai) Co., Ltd. All rights reserved.
#
#Redistribution and use in source and binary forms, with or without modification, are permitted provided that the following conditions are met:
#
#1. Redistributions of source code must retain the above copyright notice, this list of conditions and the following disclaimer.
#
#2. Redistributions in binary form must reproduce the above copyright notice, this list of conditions and the following disclaimer in the documentation and/or other materials provided with the distribution.
#
#3. Neither the name of the copyright holder nor the names of its contributors may be used to endorse or promote products derived from this software without specific prior written permission.
#
#THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
#

FROM cr.metax-tech.com/public-library/maca-pytorch:3.8.2.6-torch2.8-py312-ubuntu24.04-amd64

ENV TZ="Asia/Shanghai"

RUN apt-get update \
    && DEBIAN_FRONTEND=noninteractive apt-get install -y -qq tzdata \
    && apt-get -y --no-install-recommends -qq install libpng-dev libjpeg-dev kmod pciutils curl unzip g++ vim wget ca-certificates libglib2.0-0 \
    xz-utils libgl1 libgl1-mesa-dri libglapi-mesa libglib2.0-data libglvnd0 libglx-mesa0 libglx0 build-essential git libelf-dev libssl-dev libmsgpack-dev libnuma-dev \
    libgeos++-dev \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# MACA environment
ENV PATH=/opt/conda/bin:/opt/conda/condabin:$PATH
ENV PRIVATE_MEMORY_INIT_ONCE=1

# pytorch environment
ENV DEVINFO_ROOT=/opt/maca
ENV ISU_FASTMODEL=1
ENV USE_TDUMP=OFF
ENV TMEM_LOG=OFF
ENV DEBUG_ITRACE=0
ENV PYTORCH_DEFAULT_NLC=1

# active conda environment
RUN /opt/conda/condabin/conda init bash

RUN pip3 install cartopy opencv-python einops scikit-image numpy==1.26.4

RUN git clone https://git.codeocean.com/capsule-3935105.git /opt/NowcastNET
WORKDIR /opt/NowcastNET

CMD ["/bin/bash"]
