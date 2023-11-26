#
# https://github.com/microsoft/vscode-dev-containers/tree/main/containers/debian
#
ARG VARIANT
FROM mcr.microsoft.com/vscode/devcontainers/base:0-${VARIANT}

#
# Debian: update, git, some packages needed for boost
#
RUN apt-get update && export DEBIAN_FRONTEND=noninteractive && \
    apt-get -y upgrade && \
    apt-get -y install git bzip2 && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

#
# Latest LLVM (beyond version 7 already installed using apt-get)
#
# https://pspdfkit.com/blog/2020/visual-studio-code-cpp-docker/
#
ARG VARIANT
ARG LLVM_VERSION=17
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
        libunwind-${LLVM_VERSION}-dev \
        libclang-rt-${LLVM_VERSION}-dev && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

ADD llvm-alternatives.sh .
ARG LLVM_VERSION
RUN bash -c "./llvm-alternatives.sh ${LLVM_VERSION}"

#
# Boost
#
ARG BOOST_VERSION=1.83.0
RUN BV=$(echo "$BOOST_VERSION"|tr . _) && \
    wget https://boostorg.jfrog.io/artifactory/main/release/${BOOST_VERSION}/source/boost_${BV}.tar.bz2 && \
    tar xjf boost_${BV}.tar.bz2 && \
    rm boost_${BV}.tar.bz2 && \
    cd boost_${BV} && \
    ./bootstrap.sh --with-toolset=clang && \
    ./b2 -j8 --with-system --with-thread --with-date_time --with-regex --with-serialization --with-filesystem --with-coroutine install && \
    cd .. && \
    rm -rf boost_${BV}

#
# Other development libraries
#
RUN apt-get update && export DEBIAN_FRONTEND=noninteractive && \
    apt-get -y install --no-install-recommends \
       ninja-build gdb \
       libxml2-dev libcunit1-dev libev-dev libssl-dev libc-ares-dev libevent-dev zlib1g-dev liburing-dev \       
       libpcap-dev socat netcat tcpdump && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

#
# custom CMake
#
ARG CMAKE_VERSION=3.27.1
RUN wget https://github.com/Kitware/CMake/releases/download/v${CMAKE_VERSION}/cmake-${CMAKE_VERSION}-Linux-x86_64.sh -q -O /tmp/cmake-install.sh && \
    chmod u+x /tmp/cmake-install.sh && \
    mkdir /opt/cmake-${CMAKE_VERSION} && \
    /tmp/cmake-install.sh --skip-license --prefix=/opt/cmake-${CMAKE_VERSION} && \
    rm /tmp/cmake-install.sh && \
    ln -s /opt/cmake-${CMAKE_VERSION}/bin/* /usr/local/bin

ENV CC="/usr/bin/clang" \
    CXX="/usr/bin/clang++" \
    COV="/usr/bin/llvm-cov" \
    LLDB="/usr/bin/lldb"

#
# libc++, needed for cppcoro
#
ENV CXXFLAGS="-stdlib=libc++"

# -------------------------------------------------------------------------------------------------

#
# .NET (required by CMake via .NET Install Tool)
#
# https://docs.microsoft.com/de-de/dotnet/core/install/linux-debian
#
# RUN wget https://packages.microsoft.com/config/debian/11/packages-microsoft-prod.deb -O packages-microsoft-prod.deb && \
#     sudo dpkg -i packages-microsoft-prod.deb && \
#     rm packages-microsoft-prod.deb
#
# RUN apt-get update && \
#     apt-get install -y apt-transport-https && \
#     apt-get update && \
#     DOTNET_CLI_TELEMETRY_OPTOUT=1 apt-get install -y dotnet-sdk-6.0 && \
#     echo -e "\nexport DOTNET_CLI_TELEMETRY_OPTOUT=1" >>/etc/profile.d/dotnet-cli-tools-bin-path.sh 
