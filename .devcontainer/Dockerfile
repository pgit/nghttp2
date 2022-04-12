# See here for image contents: https://github.com/microsoft/vscode-dev-containers/tree/v0.194.0/containers/debian/.devcontainer/base.Dockerfile

# [Choice] Debian version: bullseye, buster, stretch
ARG VARIANT="bullseye"
FROM mcr.microsoft.com/vscode/devcontainers/base:0-${VARIANT}
# FROM mcr.microsoft.com/vscode/devcontainers/cpp:0-${VARIANT}

# ** [Optional] Uncomment this section to install additional packages. **
# RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
#     && apt-get -y install --no-install-recommends <your-package-list-here>

#
# Debian: CMake, git
#
RUN apt-get update && \
    apt-get -y upgrade && \
    apt-get -y install cmake git libc++abi-dev libc++-dev bzip2 && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

#
# Latest LLVM (beyond version 7 already installed using apt-get)
#
# https://pspdfkit.com/blog/2020/visual-studio-code-cpp-docker/
#
ARG VARIANT="bullseye"
ARG LLVM_VERSION=14
ARG LLVM_GPG_FINGERPRINT=6084F3CF814B57C1CF12EFD515CF4D18AF4F7421
RUN wget -O - https://apt.llvm.org/llvm-snapshot.gpg.key | apt-key add - && \
    echo "deb http://apt.llvm.org/${VARIANT}/ llvm-toolchain-${VARIANT}-${LLVM_VERSION} main" >> /etc/apt/sources.list && \
    apt-get update && \
    apt-get -y install --no-install-recommends \
        llvm-${LLVM_VERSION} \
        clang-${LLVM_VERSION} \
        lldb-${LLVM_VERSION} \
        libc++-${LLVM_VERSION}-dev \
        libc++abi-${LLVM_VERSION}-dev \
        clang-tidy-${LLVM_VERSION} \
        clangd-${LLVM_VERSION} \
        lldb-${LLVM_VERSION} \
        libunwind-${LLVM_VERSION}-dev && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

ADD llvm-alternatives.sh .
RUN bash -c ./llvm-alternatives.sh

#
# Boost
#
ARG BOOST_VERSION=1_78_0
ARG BOOST_VERSION_DOT=1.78.0
RUN wget https://boostorg.jfrog.io/artifactory/main/release/${BOOST_VERSION_DOT}/source/boost_${BOOST_VERSION}.tar.bz2 && \
    tar xjf boost_${BOOST_VERSION}.tar.bz2 && \
    rm boost_${BOOST_VERSION}.tar.bz2 && \
    cd boost_${BOOST_VERSION} && \
    ./bootstrap.sh --with-toolset=clang && \
    ./b2 -j8 --with-system --with-thread --with-date_time --with-regex --with-serialization --with-filesystem --with-coroutine install && \
    cd .. && \
    rm -rf boost_${BOOST_VERSION}

#
# Other development libraries
#
RUN apt-get update && export DEBIAN_FRONTEND=noninteractive && \
    apt-get -y install --no-install-recommends \
       ninja-build \
       libxml2-dev libcunit1-dev libev-dev libssl-dev libc-ares-dev libevent-dev zlib1g-dev && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*
