FROM ubuntu:20.04
ENV TZ=Europe/Moscow

# cascadeur
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone && \
    apt-get update && apt-get install -y --no-install-recommends \
        git \
        make \
        wget \
        ninja-build \
        gcc-10 \
        g++-10 \
        clang-11 \
        libc++-11-dev \
        libc++abi-11-dev \
        python3-pip \
        python3-sphinx \
        ccache \
        mesa-common-dev \
        python3.8 \
        python3.8-dev \
        libssl-dev \
        pkg-config \
        openssh-client \
        libxcb-xinerama0 && \
    ln -s /usr/bin/gcc-10 /usr/bin/gcc && \
    ln -s /usr/bin/g++-10 /usr/bin/g++ && \
    ln -s /usr/bin/clang-11 /usr/bin/clang && \
    ln -s /usr/bin/clang++-11 /usr/bin/clang++ && \
    pip3 install --upgrade conan numpy

# cmake
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone && \
    apt-get update && apt-get install -y --no-install-recommends \
        gnupg \
        ca-certificates && \
    wget -O - https://apt.kitware.com/keys/kitware-archive-latest.asc 2>/dev/null \
        | gpg --dearmor - \
        | tee /usr/share/keyrings/kitware-archive-keyring.gpg >/dev/null && \
    echo 'deb [signed-by=/usr/share/keyrings/kitware-archive-keyring.gpg] https://apt.kitware.com/ubuntu/ focal main' \
        | tee /etc/apt/sources.list.d/kitware.list >/dev/null && \
    apt-get update && \
    apt-get install -y --no-install-recommends cmake

# openh264
RUN apt-get install -y --no-install-recommends \
        nasm && \
    git clone https://github.com/cisco/openh264 /tmp/openh264 && \
    cd /tmp/openh264 && \
    make -j$(nproc) && \
    make install && \
    cd / && \
    rm -rf /tmp/openh264 && \
    ldconfig

# ffmpeg
RUN git clone https://git.ffmpeg.org/ffmpeg.git /tmp/ffmpeg && \
    cd /tmp/ffmpeg && \
    ./configure \
        --disable-all --enable-shared --disable-static --disable-network \
        --disable-autodetect --enable-ffmpeg --enable-small \
        --enable-avcodec --enable-avformat --enable-swscale \
        --enable-encoder=rawvideo,libopenh264 --enable-protocol=file \
        --enable-muxer=rawvideo,mp4 --enable-libopenh264 --prefix=/opt/ffmpeg && \
    make -j$(nproc) && \
    make install && \
    cd / && \
    rm -rf /tmp/ffmpeg

# fbxsdk
RUN apt-get install -y --no-install-recommends \
        xz-utils \
        libxml2-dev && \
    mkdir /fbx_sdk && cd /fbx_sdk && \
    wget --user-agent="Chrome" https://www.autodesk.com/content/dam/autodesk/www/adn/fbx/2020-2/fbx20202_fbxsdk_linux.tar.gz && \
    tar xfz fbx20202_fbxsdk_linux.tar.gz && \
    yes yes | ./fbx20202_fbxsdk_linux .

# python
RUN apt-get install -y --no-install-recommends \
        zlib1g-dev \
        zip \
        unzip && \
    rm -rf /var/lib/apt/lists/* && \
    git clone https://github.com/python/cpython /tmp/cpython && \
    cd /tmp/cpython && \
    git checkout v3.8.5 && \
    sed -i 's=#zlib zlibmodule.c -I$(prefix)/include -L$(exec_prefix)/lib -lz=zlib zlibmodule.c -I$(prefix)/include -L$(exec_prefix)/lib -lz=' Modules/Setup && \
    ./configure --enable-optimizations --enable-shared --prefix=/tmp && \
    make -j$(nproc) && \
    make install && \
    mkdir /python && \
    cp -P /tmp/lib/libpython3.8.so /tmp/lib/libpython3.8.so.1.0 /python && \
    mkdir /python/lib-dynload && \
    mv /tmp/lib/python3.8/lib-dynload/* /python/lib-dynload && \
    rm -rf /tmp/lib/python3.8/test && \
    cd /tmp/lib/python3.8 && \
    zip -r python38.zip * && \
    mkdir /python/lib && \
    cp /tmp/lib/python3.8/python38.zip /python/lib && \
    wget https://files.pythonhosted.org/packages/77/0b/41e345a4f224aa4328bf8a640eeeea1b2ad0d61517f7d0890f167c2b5deb/numpy-1.19.4-cp38-cp38-manylinux1_x86_64.whl && \
    mkdir /python/modules && \
    unzip numpy-1.19.4-cp38-cp38-manylinux1_x86_64.whl -d /python/modules && \
    rm -rf /tmp/bin /tmp/include /tmp/lib /tmp/share && \
    rm -rf /tmp/cpython

# Qt need to be installed to /Qt directory using the Qt Online Installer (https://www.qt.io/download)
