FROM nvidia/cuda:10.1-devel-ubuntu18.04
ENV CUDNN_VERSION 7.6.4.38
RUN apt-get update && apt-get install -y --no-install-recommends \
    libcudnn7=$CUDNN_VERSION-1+cuda10.1 \
libcudnn7-dev=$CUDNN_VERSION-1+cuda10.1 \
&& \
    apt-mark hold libcudnn7 && \
    rm -rf /var/lib/apt/lists/*
#set timezone
ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y tzdata
#install some packages
RUN apt-get update && apt-get install -y  \
    wget \
    curl \
    make \
    build-essential \
    libssl-dev \
    zlib1g-dev \
    libbz2-dev \
    libreadline-dev \
    libsqlite3-dev \
    llvm \
    libncurses5-dev \
    libncursesw5-dev \
    xz-utils \
    tk-dev \
    libffi-dev \
    liblzma-dev \
    tree \
    vim
WORKDIR /root/
#install Python
RUN wget https://www.python.org/ftp/python/3.7.0/Python-3.7.0.tar.xz \
    && tar xvf Python-3.7.0.tar.xz \
    && cd Python-3.7.0 \
    && ./configure --enable-optimizations \
    && make altinstall
ENV PYTHONIOENCODING "utf-8"
WORKDIR /root/Python-3.7.0
RUN ln -fs /root/Python-3.7.0/python /usr/bin/python
#install pip
RUN curl -kL https://bootstrap.pypa.io/get-pip.py | python 
WORKDIR /root/
ADD requirements.txt /root/
RUN pip install -r requirements.txt && rm requirements.txt
RUN rm Python-3.7.0.tar.xz
ENV PYTHONPATH "${PYTHONPATH}:/usr/local/lib/python3.7/site-packages"
CMD ["/bin/bash"]
