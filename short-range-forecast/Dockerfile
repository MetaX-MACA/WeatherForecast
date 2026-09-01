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
  && apt-get -y --no-install-recommends -qq install libpng-dev libjpeg-dev kmod pciutils curl unzip g++ vim wget ca-certificates \
  xz-utils libgl1 libgl1-mesa-dri libglapi-mesa libglib2.0-data libglvnd0 libglx-mesa0 libglx0 build-essential git libelf-dev libssl-dev libmsgpack-dev libnuma-dev \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/*

# MACA environment
ENV PATH=/opt/conda/bin:/opt/conda/condabin:$PATH

# pytorch environment
ENV DEVINFO_ROOT=/opt/maca
ENV ISU_FASTMODEL=1
ENV USE_TDUMP=OFF
ENV TMEM_LOG=OFF
ENV DEBUG_ITRACE=0

# active conda environment
RUN /opt/conda/condabin/conda init bash

WORKDIR /opt/ai-models

# install ai-models
RUN git clone https://github.com/ecmwf-lab/ai-models.git /opt/ai-models
RUN cd /opt/ai-models && pip install .

# install fourcastnet
RUN pip install ai-models-fourcastnet
RUN sed -i -E 's/torch\.load\(checkpoint_file, map_location=self\.device\)/torch.load(checkpoint_file, map_location=self.device, weights_only=False)/g' /opt/conda/lib/python3.12/site-packages/ai_models_fourcastnet/model.py

# install fourcastnetv2
RUN pip install ai-models-fourcastnetv2
RUN sed -i -E 's/torch\.load\(checkpoint_file, map_location=self\.device\)/torch.load(checkpoint_file, map_location=self.device, weights_only=False)/g' /opt/conda/lib/python3.12/site-packages/ai_models_fourcastnetv2/model.py

# install aurora
RUN pip install ai-models-aurora

# install jax
RUN pip install chex "jax==0.4.34" cartopy "numpy<2.0"
COPY maca-jax-0.4.34-py312-3.8.2.1-x86_64.tar.xz /opt
RUN mkdir -p /opt/jax && tar -xJf /opt/maca-jax-0.4.34-py312-3.8.2.1-x86_64.tar.xz -C /opt/jax
RUN pip install --no-deps /opt/jax/*/wheel/*.whl

# install graphcast and gencast
RUN git clone --branch v0.2 https://github.com/google-deepmind/weathernext.git /opt/weathernext
RUN cd /opt/weathernext && pip install .
RUN sed -i -E 's/jax\.NamedSharding\(mesh, jax\.P\(axis_name\)\)/jax.sharding.NamedSharding(mesh, jax.sharding.PartitionSpec(axis_name))/g' /opt/conda/lib/python3.12/site-packages/graphcast/rollout.py
RUN pip install ai-models-graphcast
RUN pip install dm-haiku==0.0.15
RUN pip install ai-models-gencast
RUN pip install zarr==3.1.5

CMD ["/bin/bash"]
