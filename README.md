>[!note]
>This Repo is very much in WIP and expect some changes and updates in the future!

# Minecraft-Behind-CG-NAT

This document aims toward setting up a Minecraft server behind CG-NAT (Carrier Grade NAT) which can prevent users outside your network form accessing any service that's hosted within.

# What is CG-NAT?

CG-NAT aka "Carrier Grade - Network Address translation" and is often for residential use and to prevent IPv4 Exhaustion by adding a second layer of NAT. That means it's not possible to access any services that's hosted on your Local Network.

![alt text](<Pasted image 20240730132626.png>)
Credit: [Hostfi](https://www.hostifi.com/blog/cgnat-on-starlink-explained)

# Bypass CG-NAT (not open sourced)

There are plenty of different ways to bypass CG-NAT but not everything is for free. There are services such as [playit.gg](https://playit.gg/) and [Ngrok](https://ngrok.com/), both offer a free plan but are pretty limited such as limited bandwidth and no custom domains. If you don't worry about bandwidths and custom domains so are they a viable choice.

These services creates a Secure tunnel between your Minecraft Server and their relay servers that other users connect to and then gets routed through the tunnel to your server.

But if you're looking for a completely self hosted and open source solution,  check out [installation-with-docker](https://github.com/DatGamerboi101/Minecraft-Behind-CG-NAT?tab=readme-ov-file#installation-with-docker).

## How to bypass CG-NAT

To bypass CG-NAT you'll Need a Minecraft server and a VPS with a public IP and docker on both servers. You can use any VPS provider like [Linode](https://www.linode.com/) or Hetzner.

To get it working we need to use [rathole](https://github.com/rapiz1/rathole) which is a open source project for self hosted tunnel. But the official docker image only supports ``amd64`` so we need a fork of it by [archef2000](https://hub.docker.com/r/archef2000/rathole) which supports ``arm64`` (For arm based systems)

# Installation with docker

Make sure both ``docker`` and ``docker-compose`` commands work before continuing. With the help of [archef2000's](https://hub.docker.com/r/archef2000/rathole) fork and docker-compose it can be easily deployed.

>[!caution] 
>**Never** share or use simple tokens in `SERVICE_TOKEN_X=TOKEN_HERE` . It might be subjected to a brute force attack and risk compromising the tunnel.

You can find ``docker-compose`` examples below for both ``client`` and ``server``

## Docker-compose ``Server``

### ``docker-compose`` for vanilla server

```d
version: '1.0'
services:
  rathole-server:
    image: archef2000/rathole:latest
    container_name: rathole
    environment:
      - "RUST_LOG=info"
      - "ADDRESS=0.0.0.0:2333"
      - "SERVICE_TOKEN_1=TOKEN_1"
      - "SERVICE_NAME_1=minecraft"
      - "SERVICE_ADDRESS_1=0.0.0.0:25565"
    restart: unless-stopped
    network_mode: host
```

### ``docker-compose`` for use with Simple Voice mod

```d
version: '1.0'
services:
  rathole-server:
    image: archef2000/rathole:latest
    container_name: rathole
    environment:
      - "RUST_LOG=info"
      - "ADDRESS=0.0.0.0:2333"
      - "SERVICE_TOKEN_1=TOKEN_1"
      - "SERVICE_TOKEN_2=TOKEN_2"
      - "SERVICE_NAME_1=minecraft"
      - "SERVICE_NAME_2=voicechat"
      - "SERVICE_TYPE_2=udp"
      - "SERVICE_ADDRESS_1=0.0.0.0:25565"
      - "SERVICE_ADDRESS_2=0.0.0.0:24454"
    restart: unless-stopped
    network_mode: host
```

>[!note]
>Simple voice mod uses``port:24454`` by default and ``UDP`` so it's important to specify it with `SERVICE_TYPE_2=udp` 

## Docker-compose ``Client``

### ``docker-compose`` for vanilla server

```d
version: '1.0'
services:
  rathole-client:
    image: archef2000/rathole:latest
    command: client
    environment:
      - "RUST_LOG=info"
      - "ADDRESS=IP_OF_SERVER:2333"
      - "SERVICE_TOKEN_1=TOKEN_1"
      - "SERVICE_NAME_1=minecraft"
      - "SERVICE_ADDRESS_1=IP_OF_HOST:25565"
    restart: unless-stopped
    network_mode: host
```

### ``docker-compose`` for use with Simple Voice mod

```d
version: '1.0'
services:
  rathole-client:
    image: archef2000/rathole:latest
    command: client
    environment:
      - "RUST_LOG=info"
      - "ADDRESS=IP_OF_SERVER:2333"
      - "SERVICE_TOKEN_1=TOKEN_1"
      - "SERVICE_TOKEN_2=TOKEN_2"
      - "SERVICE_NAME_1=minecraft"
      - "SERVICE_NAME_2=voicechat"
      - "SERVICE_TYPE_2=udp"
      - "SERVICE_ADDRESS_1=IP_OF_HOST:25565"
      - "SERVICE_ADDRESS_2=IP_OF_HOST:24454"
    restart: unless-stopped
    network_mode: host
```

>[!note]
>Simple voice mod uses``port:24454`` by default and ``UDP`` so it's important to specify it with `SERVICE_TYPE_2=udp`

# Server hosting guide coming soon
- - -
This repo is under  **MIT lisence**