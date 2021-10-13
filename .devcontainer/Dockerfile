# See here for image contents: https://github.com/microsoft/vscode-dev-containers/tree/v0.194.0/containers/debian/.devcontainer/base.Dockerfile

# [Choice] Debian version: bullseye, buster, stretch
ARG VARIANT="buster"
FROM mcr.microsoft.com/vscode/devcontainers/base:0-${VARIANT}

# ** [Optional] Uncomment this section to install additional packages. **
# RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
#     && apt-get -y install --no-install-recommends <your-package-list-here>

RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
    && apt-get -y install --no-install-recommends \
       gcc g++ make cmake \
       libboost1.74-dev libboost-system1.74-dev libboost-thread1.74-dev \
       libxml2-dev libcunit1-dev libev-dev libssl-dev libc-ares-dev libevent-dev zlib1g-dev

#
# Latest LLVM (beyond version 7 already installed using apt-get)
#
# https://pspdfkit.com/blog/2020/visual-studio-code-cpp-docker/
#
ARG VARIANT="bullseye"
ARG LLVM_VERSION=12
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
        libunwind-${LLVM_VERSION}-dev && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*
ENV DEBIAN_FRONTEND=
