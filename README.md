[![Docker Cloud Build Status](https://img.shields.io/docker/cloud/build/xtr0py/dnscrypt-proxy-2-docker.svg)](https://hub.docker.com/r/xtr0py/dnscrypt-proxy-2-docker)
![Docker Image Size (latest by date)](https://img.shields.io/docker/image-size/xtr0py/dnscrypt-proxy-2-docker)
[![Docker Pulls](https://img.shields.io/docker/pulls/xtr0py/dnscrypt-proxy-2-docker.svg)](https://hub.docker.com/r/xtr0py/dnscrypt-proxy-2-docker)

This is a Docker image containing [DNSCrypt Proxy 2.x](https://github.com/jedisct1/dnscrypt-proxy). You can use this to set up a DNS proxy on your local network which resolves queries using DNSCrypt or DNS-over-HTTPS (DoH) rather than sending plaintext DNS queries over the Internet.

This image comes with a configuration which:
- [x] Looks up queries via DNS-over-HTTPS (DoH) and DNSCrypt
- [x] Use only DNSSEC, no logging, no filtering servers
- [NO] Listens on all interfaces
- [X] Listens at 172.172.172.53 (Minimal change required to your Distro)
- [x] Excludes Cloudflare, Google, and Yandex servers
- [x] updated DNScrypt-proxy 2.0.45 
- [x] Uses public-resolvers v3

```yaml
# Point your DNS servers in NetworkManager or /etc/resolv.conf to '172.172.172.53'.
# When removing the comment hashes: make sure the line shifts left by 1 character.
# Uncomment 'ports:' to listen on localhost port 53 (0.0.0.0:53); fails when port 53 on localhost is already bound.
# uncomment 'volumes:' to specify a custom dnscrypt-proxy.toml
version: '3.3'
services:
    dnscrypt-proxy:
#        ports:
#           - '53:53/udp'
#        volumes:
#           - '/path/to/dnscrypt-proxy.toml:/config/dnscrypt-proxy.toml'
        image: xtr0py/dnscrypt-proxy-2-docker
        networks:
            dnscrypt_network:
                ipv4_address: 172.172.172.53
        restart: always
networks:
    dnscrypt_network:
        ipam:
            driver: default
            config:
                - subnet: "172.172.172.0/24"
                
```
