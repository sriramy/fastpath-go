FROM gcr.io/buscr-netautotest-39446/github_sriramy_fastpath-go/nff-go-builder:latest

ARG NFF_GO
ARG GO_VERSION=go1.15.5

RUN apt-get -q update && apt-get -q -y install \
    ethtool

RUN mkdir -p /tmp/dep \
    && wget --no-check-certificate -O /tmp/dep/golang.tar.gz -q https://dl.google.com/go/${GO_VERSION}.linux-amd64.tar.gz \
    && tar -C /usr/local -xzf /tmp/dep/golang.tar.gz \
    && rm -rf /tmp/dep

WORKDIR /app
COPY /app/ /app

RUN export PATH=$PATH:/usr/local/go/bin \
    && export GOBIN=/app/bin \
    && go install ./...
