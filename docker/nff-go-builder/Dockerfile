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
    libpcap-dev \
    libelf-dev \
    libhugetlbfs-bin \
    libnuma-dev \
    libhyperscan-dev \
    liblua5.3-dev \
    libmnl-dev \
    libibverbs-dev

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
