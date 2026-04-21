## Warning, this file will no longer get updates due to Rans4ckeR not supporting it. For more up to date versions please use the dotnet9 version: https://github.com/CnCNet/cncnet-docker-dotnetcore-tunnel/blob/main/dockerfile-dotnet9

FROM ubuntu:latest

# Install Common Software Properties
RUN apt-get update && \
    apt-get install -y software-properties-common

# Add extra repository for backports
RUN add-apt-repository ppa:dotnet/backports -y

# Update and install necessary packages
RUN apt-get update && \
    apt-get install -y wget tar unzip dotnet-sdk-8.0 aspnetcore-runtime-8.0 dotnet-runtime-8.0 libssl-dev && \
    rm -rf /var/lib/apt/lists/*

# Add cncnet user and group, no password, home directory
RUN groupadd -r cncnet && useradd -r -g cncnet -m cncnet

# Set working directory
WORKDIR /app

# Set version and base URL as build args
ARG VERSION=v4.0.19
ARG BASE_URL=https://github.com/Rans4ckeR/cncnet-server/releases/download/${VERSION}/

# Default environment variables
ENV SERVER_NAME="My CnCNet tunnel"
ENV PORT1=50001
ENV PORT2=50000

# Download and unzip the correct architecture build
RUN ARCH=$(uname -m) && \
    case "$ARCH" in \
        x86_64) \
            FILE=cncnet-server-${VERSION}-net8.0-V2+V3-linux-x64.zip ;; \
        aarch64) \
            FILE=cncnet-server-${VERSION}-net8.0-V2+V3-linux-arm64.zip ;; \
        *) \
            echo "Unsupported architecture: $ARCH" && exit 1 ;; \
    esac && \
    wget ${BASE_URL}${FILE} -O /tmp/cncnet-server.zip && \
    unzip /tmp/cncnet-server.zip -d /app && \
    rm /tmp/cncnet-server.zip

# Change permissions to make cncnet-server executable
RUN chmod +x /app/cncnet-server

# Ensure /logs directory exists and set ownership
RUN mkdir -p /logs && chown cncnet:cncnet /logs /app

# Switch to non-root user
USER cncnet

# Expose the required ports
EXPOSE 50000/tcp 50000/udp 50001/tcp 50001/udp 8054/udp 3478/udp

# Start the tunnel server with env variables for name and ports, log to both file and stdout
CMD ["sh", "-c", "/app/cncnet-server --name \"${SERVER_NAME}\" --2 --3 --m 200 --p ${PORT1} --p2 ${PORT2} 2>&1 | tee /logs/cncnet-server.log"]
