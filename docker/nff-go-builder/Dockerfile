FROM ubuntu:latest

ARG MAKEFLAGS=-j2
ARG DEBIAN_FRONTEND=noninteractive

ENV GOROOT /opt/go
ENV PATH ${GOROOT}/bin:${GOPATH}/bin:${PATH}
ENV NFF_GO /nff-go
ENV NFF_VERSION v0.9.2

RUN apt-get -q update && apt-get -q -y install \
    make \
    build-essential \
    git \
    curl \
    wget \
    protobuf-compiler \
    libpcap-dev \
    libelf-dev \
    libhugetlbfs-bin \
    libnuma-dev \
    libhyperscan-dev \
    liblua5.3-dev \
    libmnl-dev \
    libibverbs-dev \
    linux-headers-`uname -r`

RUN cd /opt && curl -L -s https://dl.google.com/go/go1.13.1.linux-amd64.tar.gz | tar zx
RUN git clone -b v0.0.4 https://github.com/libbpf/libbpf
RUN make -C libbpf/src all install
RUN echo "/usr/lib64" > /etc/ld.so.conf.d/usrlib64.conf
RUN ldconfig

RUN git clone --recurse-submodules --branch ${NFF_VERSION} http://github.com/intel-go/nff-go ${NFF_GO}
RUN cd ${NFF_GO} && \
    go mod download && \
    make -j8

WORKDIR ${NFF_GO}

# Setup nff-go and dpdk environment variables
ENV RTE_TARGET x86_64-native-linuxapp-gcc
ENV DPDK_DIR dpdk
ENV DPDK_INSTALL_DIR ${RTE_TARGET}-install
ENV RTE_SDK ${NFF_GO}/dpdk/${DPDK_DIR}/${DPDK_INSTALL_DIR}/usr/local/share/dpdk

ENV CGO_LDFLAGS_ALLOW "-Wl,--((no-)?whole-archive|((start|end)-group))"
ENV CGO_CFLAGS "-I${RTE_SDK}/${RTE_TARGET}/include -O3 -std=gnu11 -m64 -pthread -march=native -mno-fsgsbase -mno-f16c -DRTE_MACHINE_CPUFLAG_SSE -DRTE_MACHINE_CPUFLAG_SSE2 -DRTE_MACHINE_CPUFLAG_SSE3 -DRTE_MACHINE_CPUFLAG_SSSE3 -DRTE_MACHINE_CPUFLAG_SSE4_1 -DRTE_MACHINE_CPUFLAG_SSE4_2 -DRTE_MACHINE_CPUFLAG_PCLMULQDQ -DRTE_MACHINE_CPUFLAG_RDRAND -DRTE_MACHINE_CPUFLAG_F16C -include rte_config.h -Wno-deprecated-declarations"
ENV CGO_LDFLAGS "-L${RTE_SDK}/${RTE_TARGET}/lib -Wl,--no-as-needed -Wl,-export-dynamic"
