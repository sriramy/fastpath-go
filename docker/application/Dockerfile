FROM gcr.io/buscr-netautotest-39446/github_sriramy_fastpath-go/nff-go-builder:latest

RUN apt-get -q update && apt-get -q -y install \
    ethtool

WORKDIR /app

COPY . /app