[![Docker Cloud Build Status](https://img.shields.io/docker/cloud/build/xtr0py/dnscrypt-proxy-2-docker.svg)](https://hub.docker.com/r/xtr0py/dnscrypt-proxy-2-docker)
![Docker Image Size (latest by date)](https://img.shields.io/docker/image-size/xtr0py/dnscrypt-proxy-2-docker)
[![Docker Pulls](https://img.shields.io/docker/pulls/xtr0py/dnscrypt-proxy-2-docker.svg)](https://hub.docker.com/r/xtr0py/dnscrypt-proxy-2-docker)

This is a Docker image containing [DNSCrypt Proxy 2.x](https://github.com/jedisct1/dnscrypt-proxy). You can use this to set up a DNS proxy on your local network which resolves queries using DNSCrypt or DNS-over-HTTPS (DoH) rather than sending plaintext DNS queries over the Internet.

This image comes with a configuration which:
- [x] Looks up queries via DNS-over-HTTPS (DoH) and DNSCrypt
- [x] Use only DNSSEC, no logging, no filtering servers
- [x] Listens at 172.172.172.53 (Minimal change required to your Distro)
- [x] Easily re-configured to listen on localhost or use custom .toml 
- [x] Excludes Cloudflare, Google, and Yandex servers
- [x] DNScrypt-proxy 2.0.45 
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

NOTE: By default this container will not be able to communicate with other stacks on the docker network. This is because it is on the 172.172.172.0/24 network. Any other container will be on a different segment and therefore not accessible. Exposing port 53 on localhost should provide dnscrypt-proxy resolution (uncomment the 'port:' directive in this docker-compose.yml) to any docker stack asking for it.
This introduces a new problem. You have to shut down your current dns service listening on port 53. In Ubuntu, this can be challenging. One solutions is to mask systemd-resolved and configure NetworkManager to stop managing /etc/resolv.conf. In Fedora 34, a simple solution is to mask systemd-resolved. Depending on the distro being used, these solutoins will vary. I plan on figuring out how to get docker to use the dnscrpyt-proxy-2-docker as its dns so that workarounds will not be necessary.
