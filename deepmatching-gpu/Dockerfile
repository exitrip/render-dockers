FROM nvidia/cuda:8.0-cudnn5-devel

# PYTHON 2, DO NOT USE PYTHON 3!

LABEL maintainer "Shaun Berryman <shaun@shaunberryman.com>"

# Supress warnings about missing front-end. As recommended at:
# http://stackoverflow.com/questions/22466255/is-it-possibe-to-answer-dialog-questions-when-installing-under-docker
ARG DEBIAN_FRONTEND=noninteractive

# Install some dependencies
RUN apt-get update && \
    apt-get install -y \
      git \
      unzip \
      wget \
      build-essential \
      cmake \
      git \
      nano \
      pkg-config \
      libprotobuf-dev \
      libleveldb-dev \
      libsnappy-dev \
      libhdf5-serial-dev \
      protobuf-compiler \
      libatlas-base-dev \
      && \
    apt-get install --no-install-recommends -y libboost-all-dev && \
    apt-get install -y \
      libgflags-dev \
      libgoogle-glog-dev \
      liblmdb-dev \
      python-pip \
      python-dev \
      python-numpy \
      python-scipy \
      libopencv-dev \
      python-opencv \
      python-matplotlib \
      swig \
      ffmpeg \
      && \
  apt-get clean && \
  apt-get autoremove && \
  rm -rf /var/lib/apt/lists/*

#update pip for silence
RUN pip install --upgrade pip

# cuDNN v3
COPY cudnn-7.0-linux-x64-v3.0.8-prod.tgz /root/
RUN cd /root && \
#wget "http://192.168.1.6:8001/cudnn-7.0-linux-x64-v3.0.8-prod.tgz" && \
    tar xf cudnn-7.0-linux-x64-v3.0.8-prod.tgz && \
    rm cudnn-7.0-linux-x64-v3.0.8-prod.tgz && \
    mv /root/cuda/include/* /usr/include/ && \
    mv /root/cuda/lib64/* /usr/lib/x86_64-linux-gnu/ && \
    rm -rf /root/cuda

# DeepMatching GPU
COPY Makefile /root/
RUN cd /root && \
    # wget "DOWNLOAD FROM NVIDIA DIRECTLY" && \
    wget "http://lear.inrialpes.fr/src/deepmatching/code/deepmatching_gpu_1.0.zip" && \
    unzip deepmatching_gpu_1.0.zip && \
    rm deepmatching_gpu_1.0.zip && \
    cd web_gpudm_1.0 && \
    unzip caffe.zip && \
    rm caffe.zip && \
    cd caffe/python && \
    for req in $(cat requirements.txt); do pip install $req; done && \
    cd ../ && \
    mkdir build && \
    cd build && \
    cmake -DCUDA_ARCH_NAME="Manual" \ 
    -DCUDA_ARCH_BIN="52 60 61" -DCUDA_ARCH_PTX="60 61" .. && \
    make -j"$(nproc)" && \
    make install -j"$(nproc)" && \
    cd ../../ && \
    mv /root/Makefile /root/web_gpudm_1.0/ && \
    make all

WORKDIR "/root"
CMD ["/bin/bash"]
