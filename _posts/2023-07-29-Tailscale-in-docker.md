---
title: Tailscale in docker
date: 2023-07-29 13:26:00 +/-TTTT
categories: [Network]
tags: [docker, vpn]     # TAG names should always be lowercase
---
[Tailscale](https://tailscale.com) is an easy to use system to create a mesh overlay network. Through the mesh network you can access all your devices
 that are signed in to Tailscale but its also possible to use one device as an exit node for all traffic or as a subnet router. If you want it is possible to run the exit node inside a docker container.

 This is a docker-compose file that works for me. The tskey needed can be generated on tailscales website.

```yaml
version: '3.3'
services:
    tailscale:
        container_name: tailscaled
        volumes:
            - '/docker/tailscale:/var/lib'
            - '/dev/net/tun:/dev/net/tun'
        network_mode: host
        environment:
            - TS_AUTHKEY=<insert-tskey-here>
            - TS_STATE_DIR=/var/lib/tailscale.state
            - TS_EXTRA_ARGS=--advertise-exit-node
            - TS_ROUTES=192.168.11.0/24
        image: tailscale/tailscale
(END)
```

If not already enabled you need to enable ip forwarding to make this work.
```bash
sudo echo 'net.ipv4.ip_forward = 1' | sudo tee -a /etc/sysctl.conf
sudo echo 'net.ipv6.conf.all.forwarding = 1' | sudo tee -a /etc/sysctl.conf
```
