FROM ubuntu:xenial

LABEL maintainer="Luis Martinez de Bartolome <luism@jfrog.com>"

ARG CONAN_VERSION

ENV CMAKE_VERSION_MAJOR_MINOR=3.18 \
    CMAKE_VERSION_FULL=3.18.2 \
    PYENV_ROOT=/opt/pyenv \
    PATH=/opt/pyenv/shims:${PATH} \
    CXX=/usr/bin/g++ \
    CC=/usr/bin/gcc

RUN dpkg --add-architecture i386 \
    && apt-get -qq update \
    && apt-get -qq install -y --no-install-recommends software-properties-common \
    && add-apt-repository ppa:git-core/ppa -y \
    && apt-get -qq update \
    && apt-get -qq install -y --no-install-recommends --no-install-suggests \
       sudo \
       build-essential \
       wget \
       git \
       libc6-dev-i386 \
       g++-multilib \
       libgmp-dev \
       libmpfr-dev \
       libmpc-dev \
       libc6-dev \
       nasm \
       dh-autoreconf \
       ninja-build  \
       libffi-dev \
       libssl-dev \
       pkg-config \
       subversion \
       zlib1g-dev \
       libbz2-dev \
       libsqlite3-dev \
       libreadline-dev \
       xz-utils \
       curl \
       libncurses5-dev \
       libncursesw5-dev \
       liblzma-dev \
       ca-certificates \
       autoconf-archive \
       && rm -rf /var/lib/apt/lists/* \
       && wget -q --no-check-certificate https://cmake.org/files/v${CMAKE_VERSION_MAJOR_MINOR}/cmake-${CMAKE_VERSION_FULL}-Linux-x86_64.tar.gz \
       && tar -xzf cmake-${CMAKE_VERSION_FULL}-Linux-x86_64.tar.gz \
          --exclude=bin/cmake-gui \
          --exclude=doc/cmake \
          --exclude=share/cmake-${CMAKE_VERSION_MAJOR_MINOR}/Help \
       --exclude=share/vim \
       && cp -fR cmake-${CMAKE_VERSION_FULL}-Linux-x86_64/* /usr \
       && rm -rf cmake-${CMAKE_VERSION_FULL}-Linux-x86_64 \
       && rm cmake-${CMAKE_VERSION_FULL}-Linux-x86_64.tar.gz \
       && curl -fL https://getcli.jfrog.io | sh \
       && mv jfrog /usr/local/bin/jfrog \
       && chmod +x /usr/local/bin/jfrog \
       && groupadd -f conan-1001 -g 1001 \
       && groupadd -f conan-1000 -g 1000 \
       && groupadd -f conan-2000 -g 2000 \
       && groupadd -f conan-999 -g 999 \
       && useradd -ms /bin/bash conan -g 1001 -G 1000,2000,999 \
       && printf "conan:conan" | chpasswd \
       && adduser conan sudo \
       && printf "conan ALL= NOPASSWD: ALL\\n" >> /etc/sudoers \
       && wget --no-check-certificate --quiet -O /tmp/pyenv-installer https://github.com/pyenv/pyenv-installer/raw/master/bin/pyenv-installer \
       && chmod +x /tmp/pyenv-installer \
       && /tmp/pyenv-installer \
       && rm /tmp/pyenv-installer \
       && update-alternatives --install /usr/bin/pyenv pyenv /opt/pyenv/bin/pyenv 100 \
       && PYTHON_CONFIGURE_OPTS="--enable-shared" pyenv install 3.7.13 \
       && pyenv global 3.7.13 \
       && ln -s /usr/lib/python3/dist-packages/lsb_release.py /opt/pyenv/versions/3.7.13/lib/python3.7/lsb_release.py \
       && pip install -q --upgrade --no-cache-dir pip \
       && pip install -q --no-cache-dir conan==${CONAN_VERSION} conan-package-tools \
       && chown -R conan:1001 /opt/pyenv \
       # remove all __pycache__ directories created by pyenv
       && find /opt/pyenv -iname __pycache__ -print0 | xargs -0 rm -rf \
       && update-alternatives --install /usr/bin/python python /opt/pyenv/shims/python 100 \
       && update-alternatives --install /usr/bin/python3 python3 /opt/pyenv/shims/python3 100 \
       && update-alternatives --install /usr/bin/pip pip /opt/pyenv/shims/pip 100 \
       && update-alternatives --install /usr/bin/pip3 pip3 /opt/pyenv/shims/pip3 100 \
       && apt-get -qq purge -y \
          libgmp-dev \
          libmpfr-dev \
          libmpc-dev \
          ninja-build \
          libffi-dev \
          libssl-dev \
          zlib1g-dev \
          libbz2-dev \
          libsqlite3-dev \
          libreadline-dev \
          libncurses5-dev \
          libncursesw5-dev \
          liblzma-dev \
       && apt-get -qq autoremove -y \
       && apt-get -qq autoclean \
       && apt-get -qq update \
       && rm -rf /var/lib/apt/lists/*

USER conan
WORKDIR /home/conan

RUN mkdir -p /home/conan/.conan \
    && printf 'eval "$(pyenv init -)"\n' >> ~/.bashrc \
    && printf 'eval "$(pyenv virtualenv-init -)"\n' >> ~/.bashrc
