FROM nvidia/cuda:10.0-cudnn7-runtime
 
# PYTHON 2, DO NOT USE PYTHON 3!

LABEL maintainer "ed"

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
      libpng-dev \
      nano \
      python-pip \
      python-dev \
      python-opencv \
      ffmpeg \
      && \
  apt-get clean && \
  apt-get autoremove && \
  rm -rf /var/lib/apt/lists/*

#get bleeding tf 12 for cuda10
RUN cd /root && \
  wget "https://storage.googleapis.com/tf-performance/tf_binary/tensorflow-1.12.0.a6d8ffa.AVX2.CUDA10-cp27-cp27mu-linux_x86_64.whl"

RUN pip install \
    /root/tensorflow-1.12.0.a6d8ffa.AVX2.CUDA10-cp27-cp27mu-linux_x86_64.whl

#update pip for silence
RUN pip install --upgrade pip wheel setuptools
RUN pip install --upgrade opencv-python opencv-contrib-python \ 
    numpy Pillow scipy
    
#clone neurst repo
RUN cd /root && \
    git clone "https://github.com/cysmith/neural-style-tf.git" neurst && \
    chmod a+x /root/neurst/stylize*

# copy and apply the local patch
COPY ./ed.patch /root/neurst
RUN cd /root/neurst && \
    patch -p1 -i /root/neurst/ed.patch
        
# copy magic weights
#COPY ../imagenet-vgg-verydeep-19.mat /root/neurst/
RUN cd /root/neurst && \
  wget "http://www.vlfeat.org/matconvnet/models/imagenet-vgg-verydeep-19.mat"

# get and build flow-code
RUN mkdir /root/neurst/flow-code && cd /root/neurst/flow-code && \
  wget -nv -r -l1 -np -nd -P /root/neurst/flow-code \
    -A h,cpp,Makefile "http://vision.middlebury.edu/flow/code/flow-code" && \
  wget -nv -r -l1 -np -nd -P /root/neurst/flow-code/imageLib \
    -A h,cpp,Makefile \
    "http://vision.middlebury.edu/flow/code/flow-code/imageLib" && \
  cd imageLib && make && \
  cd .. && make all && \
  ./colortest 10 colors.png

WORKDIR "/root/neurst/"
CMD ["/bin/bash"]
