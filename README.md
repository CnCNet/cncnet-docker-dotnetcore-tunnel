
# CnCNET Docker Tunnel Server based on Dot Net Core

A repo that contains a dockerfile to create a cncnet tunnel server in docker using the DOT NET 8 or DOT NET 9 Core technology.

1

Clone the dockerfile you want (net 8 or net 9) from this repo to your server.
You will need to edit your server name here too

2

Build the docker container from the docker file as below (feel free to update my-tunnel-server to a different name for ease of identifying in docker commands):

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
