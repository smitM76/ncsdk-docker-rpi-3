#FROM ubuntu:16.04
FROM resin/rpi-raspbian:stretch

RUN sudo rm -rf /var/lib/apt/lists/*

# Install necessary packages for the installer
RUN apt-get update && apt-get install -y \
    lsb-release \
    build-essential \
    sed \
    sudo \
    tar \
    udev \
    wget \
    && apt-get clean


# remove below line in case you are having fast internet      
# below are the dependencies are the same as included in requirements_apt.txt and requirements_apt_raspbian.txt
# my network failuer causing entire make statement fail in case on any one dependencies GET fails 
# so i am adding them as separate steps  

RUN apt-get install --fix-missing -y unzip \
    coreutils \
    curl \ 
    git \ 
    python3 \ 
    libprotobuf-dev  \
    libleveldb-dev \
    libsnappy-dev \ 
    libopencv-dev \ 
    libhdf5-serial-dev \ 
    protobuf-compiler \ 
    libatlas-base-dev 

RUN apt-get install --fix-missing git \
    python3-dev \
    automake \
    cmake \
    make \
    byacc \
    lsb-release

RUN apt-get  install --fix-missing -y python3-scipy \
    libgflags-dev \
    libgoogle-glog-dev \
    liblmdb-dev \
    swig3.0 \
    graphviz \
    libxslt-dev \
    libxml2-dev \
    gfortran \
    python3-yaml \
    python3-nose \ 
    python3-tk \
    python3-pip \
    python-pip \
    tar \
    wget \
    libgraphviz-dev \
    libusb-1.0-0-dev

RUN sudo apt-get update && apt-get install -y \
    python3-markdown \
    python3-h5py \
    python3-lxml \
    python3-matplotlib \
    python3-protobuf \
    python3-dateutil \
    python3-scipy \
    python3-six \
    python3-networkx\
    make\
    wget \
    python3-dev \
    python3-pip \
    python-pip
# REMOVE TILL HERE


# required dependency as per https://ncsforum.movidius.com/discussion/803/docker-container-on-raspberry-pi [@milad]
# or modify requirements_apt_raspbian.txt and:
# -python3-markdown python3-h5py python3-lxml python3-matplotlib python3-protobuf python3-dateutil python3-scipy python3-six python3-networkx make wget python3-dev python3-pip python-pip
# +python3-markdown python3-h5py python3-lxml python3-matplotlib python3-protobuf python3-dateutil python3-scipy python3-six python3-networkx make wget python3-dev python3-pip python-pip python3-setuptools python-setuptools 

RUN apt-get install -y \
    python3-setuptools \
    python-setuptools

# this command is also part of make install I am instaling it already to avoid reinstallation of this eachtime when make install fails
RUN pip3 install scikit-image

# cloning ncsdk
RUN git clone -b ncsdk2 http://github.com/Movidius/ncsdk 
# changing to working version of ncsdk
RUN cd ncsdk \
&& git checkout v2.08.01.02
#removing ncsdk.conf and install.sh
RUN rm /ncsdk/ncsdk.conf && rm /ncsdk/install.sh

# this repository contains modified ncsdk.conf and install.sh
# modifications as below:
# ncsdk.conf : INSTALL_CAFFE=no
#install.sh : removed --trusted-host files.pythonhosted.org from each pip install statment
# this repository also contains .whl file for tensorflow-1.9.0 because the statment within the make install failing to download it
RUN  git clone https://github.com/smitM76/config-files-ncsdk-docker.git \
&& cp config-files-ncsdk-docker/ncsdk.conf /ncsdk \
&& cp config-files-ncsdk-docker/install.sh /ncsdk \
&& cp config-files-ncsdk-docker/tensorflow-1.9.0-cp35-none-linux_armv7l.whl /ncsdk


# Set the current working directory to the cloned ncsdk directory
WORKDIR "/ncsdk"

# installing dependencies for tensorflow
RUN apt-get install -y unzip coreutils curl git python3 libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libhdf5-serial-dev protobuf-compiler libatlas-base-dev git python3-dev automake cmake make byacc lsb-release python3-scipy libgflags-dev libgoogle-glog-dev liblmdb-dev swig3.0 graphviz libxslt-dev libxml2-dev gfortran python3-yaml python3-nose python3-tk python3-pip python-pip tar wget libgraphviz-dev libusb-1.0-0-dev

# installing tensorflow
RUN pip3 install tensorflow-1.9.0-cp35-none-linux_armv7l.whl

# Run the installer
RUN make install

RUN apt-get install -y \
    build-essential \
    cmake \
    git \
    wget \
    unzip \
    yasm \
    pkg-config \
    libswscale-dev \
    libtbb2 \
    libtbb-dev \
    libjpeg-dev \
    libpng-dev \
    libtiff-dev \
    libavformat-dev \
    libpq-dev

# installing openalpr
RUN apt-get install -y openalpr openalpr-daemon openalpr-utils libopenalpr-dev

# installing numpy -> required to install opencv
RUN pip install numpy

# installing opencv from source

ARG BUILDDIR="/tmp/build"
ARG PYTHON_VER="3.5.3"
WORKDIR ${BUILDDIR}

WORKDIR /
ENV OPENCV_VERSION="3.4.2"
RUN wget https://github.com/opencv/opencv_contrib/archive/${OPENCV_VERSION}.zip \
&& unzip ${OPENCV_VERSION}.zip \
&& rm ${OPENCV_VERSION}.zip

RUN wget https://github.com/opencv/opencv/archive/${OPENCV_VERSION}.zip \
&& unzip ${OPENCV_VERSION}.zip \
&& mkdir /opencv-${OPENCV_VERSION}/cmake_binary \
&& cd /opencv-${OPENCV_VERSION}/cmake_binary \
&& cmake -DBUILD_TIFF=ON \
  -DBUILD_opencv_java=OFF \
  -DOPENCV_EXTRA_MODULES_PATH=/opencv_contrib-${OPENCV_VERSION}/modules \
  -DWITH_CUDA=OFF \
  -DWITH_OPENGL=ON \
  -DWITH_OPENCL=ON \
  -DWITH_IPP=ON \
  -DWITH_TBB=ON \
  -DWITH_EIGEN=ON \
  -DWITH_V4L=ON \
  -DBUILD_TESTS=OFF \
  -DBUILD_PERF_TESTS=OFF \
  -DCMAKE_BUILD_TYPE=RELEASE \
  -DCMAKE_INSTALL_PREFIX=$(python3.5 -c "import sys; print(sys.prefix)") \
  -DPYTHON_EXECUTABLE=$(which python3.5) \
  -DPYTHON_INCLUDE_DIR=$(python3.5 -c "from distutils.sysconfig import get_python_inc; print(get_python_inc())") \
  -DPYTHON_PACKAGES_PATH=$(python3.5 -c "from distutils.sysconfig import get_python_lib; print(get_python_lib())") \
  .. \
&& make install \
# removing downloaded opencv zip file
&& rm /${OPENCV_VERSION}.zip \
&& rm -r /opencv-${OPENCV_VERSION}

# setting working directory to /app
WORKDIR /app

# your project here!
