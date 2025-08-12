# CnCNET Docker Tunnel Server based on .NET Core

This repository contains Dockerfiles to create a CnCNet tunnel server in Docker using .NET 8 or .NET 9 Core technology.

---

## Getting Started

### 1. Clone the Dockerfile you want (net8 or net9) from this repo to your server.

### 2. Build the Docker image

Replace `my-tunnel-server` with a name that helps you identify your container:

```sh
docker build -t my-tunnel-server .
```

3

Run your new container with the below:

```sh
docker run -d --name my-tunnel-server \
    -p 50000:50000/tcp \
    -p 50000:50000/udp \
    -p 50001:50001/tcp \
    -p 50001:50001/udp \
    -p 8054:8054/udp \
    -p 3478:3478/udp \
    --cap-add=NET_RAW --cap-add=NET_ADMIN \
    --restart unless-stopped \
    -v /path/to/host/logs:/logs \
    -e SERVER_NAME="My Custom Tunnel Name" \
    -e PORT1=50001 \
    -e PORT2=50000 \
    my-tunnel-server
```

You can edit your dockerfile to contain extra tunnel options using the options below:
```
Options:
  --n, --name <name> (REQUIRED)                            Name of the server
  --p, --tunnelport <tunnelport>                           Port used for the V3 tunnel server [default: 50001]
  --p2, --tunnelv2port <tunnelv2port>                      Port used for the V2 tunnel server [default: 50000]
  --m, --maxclients <maxclients>                           Maximum clients allowed on the tunnel server [default: 200]
  --nm, --nomasterannounce                                 Don't register to master [default: False]
  --masp, --masterpassword <masterpassword>                Master password []
  --maintenancepassword, --maip <maintenancepassword>      Maintenance password []
  --masterserverurl, --mu <masterserverurl>                Master server URL [default:
                                                           https://cncnet.org/master-announce]
  --i, --iplimit <iplimit>                                 Maximum clients allowed per IP address [default: 8]
  --nopeertopeer, --np                                     Disable STUN NAT traversal server (UDP 8054 & 3478)
                                                           [default: False]
  --3, --tunnelv3enabled                                   Start a V3 tunnel server [default: True]
  --2, --tunnelv2enabled                                   Start a V2 tunnel server [default: True]
  --sel, --serverloglevel                                  CnCNet server messages log level [default: Information]
  <Critical|Debug|Error|Information|None|Trace|Warning>
  --syl, --systemloglevel                                  Low level system messages log level [default: Warning]
  <Critical|Debug|Error|Information|None|Trace|Warning>
  --6, --announceipv6                                      Announce IPv6 address to master server [default: True]
  --4, --announceipv4                                      Announce IPv4 address to master server [default: True]
  --h, --tunnelv2https                                     Use https Tunnel V2 web server [default: False]
  --maxpacketsize, --mps <maxpacketsize>                   Maximum accepted packet size [default: 2048]
  --maxpingsglobal, --mpg <maxpingsglobal>                 Maximum accepted ping requests globally [default: 1024]
  --maxpingsperip, --mpi <maxpingsperip>                   Maximum accepted ping requests per IP [default: 20]
  --ai, --masterannounceinterval <masterannounceinterval>  Master server announce interval in seconds [default: 60]
  --c, --clienttimeout <clienttimeout>                     Client timeout in seconds [default: 60]
  --version                                                Show version information
  -?, -h, --help                                           Show help and usage information
```

So your dockerfile run command may end up looking like 
```
CMD ./cncnet-server --name "CnCNet UK | cncnet.org" --2 --3 --m 200 --p 50001 --p2 50000 --masp "masterpassword" > cncnet-server.log 2>&1 && tail -f
```
For example to change the servername and the official password needed to recognise your server as an official tunnel


# Using Docker Compose
For easier management, a docker-compose.yml is now included, you can simply update the SERVER_NAME variable and run docker compose up -d to get going. 

```yml
services:
  cncnet:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: cncnet-server
    restart: unless-stopped
    environment:
      SERVER_NAME: "MyTunnel"
      PORT1: 50001
      PORT2: 50000
    ports:
      - "50000:50000/tcp"
      - "50000:50000/udp"
      - "50001:50001/tcp"
      - "50001:50001/udp"
      - "8054:8054/udp"
      - "3478:3478/udp"
    volumes:
      - cncnet-logs:/logs
    cap_add:
      - NET_RAW
      - NET_ADMIN

volumes:
  cncnet-logs:

```
