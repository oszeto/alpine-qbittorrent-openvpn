# [🐋 Alpine qBittorrent OpenVPN](https://github.com/guillaumedsde/alpine-qbittorrent-openvpn)

[![Gitlab pipeline status](https://img.shields.io/gitlab/pipeline/guillaumedsde/alpine-qbittorrent-openvpn)](https://gitlab.com/guillaumedsde/alpine-qbittorrent-openvpn/-/pipelines)
[![Docker Image Size (latest by date)](https://img.shields.io/docker/image-size/guillaumedsde/alpine-qbittorrent-openvpn)](https://hub.docker.com/r/guillaumedsde/alpine-qbittorrent-openvpn)
[![Docker Pulls](https://img.shields.io/docker/pulls/guillaumedsde/alpine-qbittorrent-openvpn)](https://hub.docker.com/r/guillaumedsde/alpine-qbittorrent-openvpn)
[![GitHub stars](https://img.shields.io/github/stars/guillaumedsde/alpine-qbittorrent-openvpn?label=Github%20stars)](https://github.com/guillaumedsde/alpine-qbittorrent-openvpn)
[![Docker Stars](https://img.shields.io/docker/stars/guillaumedsde/alpine-qbittorrent-openvpn)](https://hub.docker.com/r/guillaumedsde/alpine-qbittorrent-openvpn)
[![GitHub watchers](https://img.shields.io/github/watchers/guillaumedsde/alpine-qbittorrent-openvpn?label=Github%20Watchers)](https://github.com/guillaumedsde/alpine-qbittorrent-openvpn)
[![GitHub](https://img.shields.io/github/license/guillaumedsde/alpine-qbittorrent-openvpn)](https://github.com/guillaumedsde/alpine-qbittorrent-openvpn/blob/master/LICENSE.md)

This repository contains the code to build a docker container with the [qBittorrent](https://www.qbittorrent.org/) torrent client with all traffic routed through an OpenVPN tunnel with firewall rules preventing traffic outside of the tunnel.
The container is built automatically whenever the Alpine container is updated, the final image is available on the [docker hub](https://hub.docker.com/r/guillaumedsde/alpine-qbittorrent-openvpn).

This container is based on an [Alpine Linux](https://hub.docker.com/_/alpine) and uses the [S6-overlay](https://github.com/just-containers/s6-overlay) for starting setting up the firewall, VPN tunnel and lastly starting qBittorrent.
The image aims to be safe, small and generally minimal by installing as little dependencies as possible and running qBittorrent and OpenVPN as different unprivileged users.

## ✔️ Features summary

- 🏔️ Alpine Linux small and secure base Docker image
- 🤏 Minimal installed software and as few Docker layers as possible
- 🛡️ Minimal software dependencies installed
- 🖥️ Built for many platforms
- 🚇 Compatible with most OpenVPN providers
- 🛡️ Runs as unprivileged user with minimal permissions

## 🏁 How to Run

### `docker run`

```bash
$ docker run --cap-add=NET_ADMIN -d \
              -v /your/storage/path/:/downloads \
              -v /path/to/config/directory:/config \
              -v /etc/localtime:/etc/localtime:ro \
              -e OPENVPN_PROVIDER=PIA \
              -e OPENVPN_CONFIG=CA\ Toronto \
              -e OPENVPN_USERNAME=user \
              -e OPENVPN_PASSWORD=pass \
              -e PUID=1000 \
              -e PGID=1000 \
              -e LAN=192.168.0.0/16 \
              -p 8080:8080 \
              guillaumedsde/alpine-qbittorrent-openvpn:latest
```

### `docker-compose.yml`

```yaml
version: "3.3"
services:
  alpine-qbittorrent-openvpn:
    volumes:
      - "/your/storage/path/:/downloads"
      - "/path/to/config/directory:/config"
      - "/etc/localtime:/etc/localtime:ro"
    environment:
      - OPENVPN_PROVIDER=PIA
      - OPENVPN_CONFIG=CA\
      - OPENVPN_USERNAME=user
      - OPENVPN_PASSWORD=pass
      - PUID=1000
      - PGID=1000
      - LAN=192.168.0.0/16
    ports:
      - "8080:8080"
    image: guillaumedsde/alpine-qbittorrent-openvpn:latest
```

## 🖥️ Supported platforms

This container is built for many hardware platforms (yes, even ppc64le whoever uses that... 😉):

- linux/386
- linux/amd64
- linux/arm/v6
- linux/arm/v7
- linux/arm64
- linux/ppc64le

All you have to do is use a recent version of docker and it will pull the appropriate version of the image [guillaumedsde/alpine-qbittorrent-openvpn](https://hub.docker.com/repository/docker/guillaumedsde/alpine-qbittorrent-openvpn) from the docker hub.

## 🚇 OpenVPN configuration

### Officially supported

This image makes use of the [VPN providers'](https://haugene.github.io/docker-transmission-openvpn/supported-providers/) OpenVPN configurations from the latest version of [haugene/docker-transmission-openvpn](https://github.com/haugene/docker-transmission-openvpn/) cheers to that project 🍺!
It is possible I might have messed something up, so if one provider is not working for you, make sure to [leave an issue](https://github.com/guillaumedsde/alpine-qbittorrent-openvpn/issues/new/choose) on this repository's Github page.
Selecting a preloaded configuration works the same way as the haugene container (see below for an example).

### Custom OpenVPN config

If your provider is not in the supported list, you can mount your `.ovpn` file at `/config/openvpn/config.ovpn` optionally set your `OPENVPN_USERNAME` and `OPENVPN_PASSWORD` leaving the `OPENVPN_PROVIDER` empty and the container will load your configuration upon start.

## 🐌 Limitations

This image has a couple of limitations:

- **No IPv6 support** I have not installed iptables for IPv6 as such the firewall kill switch will probably not work with IPv6 (I have not tested it) if you need it, [file an issue](https://github.com/guillaumedsde/alpine-qbittorrent-openvpn/issues/new/choose) and I'll look into it when I have some time
- **Automatic restarts** I am looking into improving this to make the container more resilient

## 🙏 Credits

A couple of projects really helped me out while developing this container:

- 🍻 [0x022b/s6-openvpn](https://github.com/0x022b/s6-openvpn) for figuring out how the S6 overlay works, and for most of the code to run OpenVPN as an unprivileged user
- 🍻 [haugene/docker-transmission-openvpn](https://github.com/haugene/docker-transmission-openvpn) for general inspiration for the project and specifically, the OpenVPN configurations and the healthcheck script adapted in this repository
- 🏔️ [Alpine Linux](https://alpinelinux.org/) an awesome lightweight secure linux distribution used as the base for this container
- 🐋 The [Docker](https://github.com/docker) project (of course)
