FROM golang:1.23.5-bookworm AS golang
FROM denoland/deno:debian-2.1.7 AS deno
FROM debian:bookworm

LABEL org.opencontainers.image.source=https://github.com/nodx/nodxdc
LABEL org.opencontainers.image.description="NodX Dev Container"
LABEL org.opencontainers.image.licenses=MIT

# Base environment variables
ENV DEBIAN_FRONTEND="noninteractive"

# Install golang from docker image
COPY --from=golang /usr/local/go /usr/local/go
RUN mkdir -p /usr/local/gobin
ENV CGO_ENABLED=1
ENV GOBIN="/usr/local/gobin"
ENV PATH="$PATH:/usr/local/go/bin"
ENV PATH="$PATH:/usr/local/gobin"

# Install deno from docker image
COPY --from=deno /usr/bin/deno /usr/local/bin/deno

# Install system dependencies
RUN apt-get -qq update && \
    apt-get -qq install -y gcc wget curl iputils-ping net-tools git zip unzip p7zip-full && \
    rm -rf /var/lib/apt/lists/*

# Install dependencies with go install
RUN go install github.com/go-task/task/v3/cmd/task@v3.40.1 && \
    go install github.com/golangci/golangci-lint/cmd/golangci-lint@v1.63.4

# Default git config
# https://github.com/golangci/golangci-lint/issues/4033
RUN git config --global --add safe.directory '*'

# Add the startup script on every bash session
COPY ./startup.sh /usr/local/bin/startup.sh
RUN echo "\n\n" >> /root/.bashrc && \
    cat /usr/local/bin/startup.sh >> /root/.bashrc

# Sleep infinity to keep the container running
CMD ["sleep", "infinity"]
