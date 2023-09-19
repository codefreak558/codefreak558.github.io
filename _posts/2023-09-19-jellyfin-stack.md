---
title: Jellyfin Docker
date: 2023-09-19 19:00:00 +0200
categories: [homelab]
tags: [homelab,server,jellyfin,sonarr,radarr,docker]     # TAG names should always be lowercase
---

## Docker Compose Jellyfin

After some searching for a good Jellyfin stack we came across the github repo made by [AdrienPoupa](https://github.com/AdrienPoupa/), the repo is called [Docker-Compose-Nas](https://github.com/AdrienPoupa/docker-compose-nas). This github repo fills our Jellyfin needs with having Sonarr, Radarr, Prowlarr and Jellyfin setup in one big `docker-compose` file.

## Table of Content

<!-- TOC -->
* [Docker Compose Jellyfin](#docker-compose-jellyfin)
  * [Table of Content](#table-of-content)
  * [Jellyfin Stack](#jellyfin-stack)
    * [Environment Variables](#environment-variables)
  * [Quick Start](#quick-start)
  * [Cloudflare Tunnels](#cloudflare-tunnels)
  * [Laptop Specific Configuration](#laptop-specific-configuration)
<!-- TOC -->


## Prerequisite's
1. Docker
2. Docker-Compose
3. Laptop or Server with Intel i3 or Ryzen 3 1200 and up
4. Domain Name
5. SSH connection to server


## Jellyfin Stack

The Jellyfin stack that we will use includes the following extra containers:
- Jellyfin
- Sonarr
- Radarr
- Prowlarr
- qBittorrent

We have done some editing to the `docker-compose.yml` file as using Jellyfin behind the traefik routor doesn't work well.

The first step you need to do is clone the github repo to your server with the following command:

```console
git clone https://github.com/AdrienPoupa/docker-compose-nas
cd docker-compose-nas
```

Once we have downloaded the Github repo we need to do some changed to the `docker-compose.yml` file along with creating the .env file.

The .env file can be made by copying the example file with this command:

```console
cp .env.example .env
```

Once this is done fill in the .env file with the details for everything that is needed. Here is the env variables that are used in this project.


## Environment Variables

| Variable                       | Description                                                                                                                                                                                            | Default                                          |
|--------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------|
| `COMPOSE_FILE`                 | Docker compose files to load                                                                                                                                                                           | `docker-compose.yml`                             |
| `COMPOSE_PATH_SEPARATOR`       | Path separator between compose files to load                                                                                                                                                           | `:`                                              |
| `USER_ID`                      | ID of the user to use in Docker containers                                                                                                                                                             | `1000`                                           |
| `GROUP_ID`                     | ID of the user group to use in Docker containers                                                                                                                                                       | `1000`                                           |
| `TIMEZONE`                     | TimeZone used by the container.                                                                                                                                                                        | `America/New_York`                               |
| `DATA_ROOT`                    | Host location of the data files                                                                                                                                                                        | `/mnt/data`                                      |
| `DOWNLOAD_ROOT`                | Host download location for qBittorrent, should be a subfolder of `DATA_ROOT`                                                                                                                           | `/mnt/data/torrents`                             |
| `PIA_LOCATION`                 | Servers to use for PIA                                                                                                                                                                                 | `ca` (Montreal, Canada)                          |
| `PIA_USER`                     | PIA username                                                                                                                                                                                           |                                                  |
| `PIA_PASS`                     | PIA password                                                                                                                                                                                           |                                                  |
| `PIA_LOCAL_NETWORK`            | PIA local network                                                                                                                                                                                      | `192.168.0.0/16`                                 |
| `HOSTNAME`                     | Hostname of the NAS, could be a local IP or a domain name                                                                                                                                              | `localhost`                                      |
| `ADGUARD_HOSTNAME`             | Optional - AdGuard Home hostname used, if enabled                                                                                                                                                      |                                                  |
| `ADGUARD_USERNAME`             | Optional - AdGuard Home username to show details in the homepage, if enabled                                                                                                                           |                                                  |
| `ADGUARD_PASSWORD`             | Optional - AdGuard Home password to show details in the homepage, if enabled                                                                                                                           |                                                  |
| `DNS_CHALLENGE`                | Enable/Disable DNS01 challenge, set to `false` to disable.                                                                                                                                             | `true`                                           |
| `DNS_CHALLENGE_PROVIDER`       | Provider for DNS01 challenge, [see list here](https://doc.traefik.io/traefik/https/acme/#providers).                                                                                                   | `cloudflare`                                     |
| `LETS_ENCRYPT_CA_SERVER`       | Let's Encrypt CA Server used to generate certificates, set to production by default.<br/>Set to `https://acme-staging-v02.api.letsencrypt.org/directory` to test your changes with the staging server. | `https://acme-v02.api.letsencrypt.org/directory` |
| `LETS_ENCRYPT_EMAIL`           | E-mail address used to send expiration notifications                                                                                                                                                   |                                                  |
| `CLOUDFLARE_EMAIL`             | CloudFlare Account email                                                                                                                                                                               |                                                  |
| `CLOUDFLARE_DNS_API_TOKEN`     | API token with `DNS:Edit` permission                                                                                                                                                                   |                                                  |
| `CLOUDFLARE_ZONE_API_TOKEN`    | API token with `Zone:Read` permission                                                                                                                                                                  |                                                  |
| `SONARR_API_KEY`               | Sonarr API key to show information in the homepage                                                                                                                                                     |                                                  |
| `RADARR_API_KEY`               | Radarr API key to show information in the homepage                                                                                                                                                     |                                                  |
| `PROWLARR_API_KEY`             | Prowlarr API key to show information in the homepage                                                                                                                                                   |                                                  |
| `JELLYFIN_API_KEY`             | Jellyfin API key to show information in the homepage                                                                                                                                                   |                                                  |
| `HOMEPAGE_VAR_TITLE`           | Title of the homepage                                                                                                                                                                                  | `Docker-Compose NAS`                             |
| `HOMEPAGE_VAR_SEARCH_PROVIDER` | Homepage search provider, [see list here](https://gethomepage.dev/en/widgets/search/)                                                                                                                  | `google`                                         |
| `HOMEPAGE_VAR_HEADER_STYLE`    | Homepage header style, [see list here](https://gethomepage.dev/en/configs/settings/#header-style)                                                                                                      | `boxed`                                          |
| `HOMEPAGE_VAR_WEATHER_CITY`    | Homepage weather city name                                                                                                                                                                             |                                                  |
| `HOMEPAGE_VAR_WEATHER_LAT`     | Homepage weather city latitude                                                                                                                                                                         |                                                  |
| `HOMEPAGE_VAR_WEATHER_LONG`    | Homepage weather city longitude                                                                                                                                                                        |                                                  |
| `HOMEPAGE_VAR_WEATHER_UNIT`    | Homepage weather unit, either `metric` or `imperial`                                                                                                                                                   | `metric`                                         |

The changes made to the `docker-compose.yml` file are the following

```yaml
version: "3.9"
services:
  .....
  qbittorrent:
    .....
      #- com.centurylinklabs.watchtower.depends-on=/vpn
      - homepage.group=Download
      - homepage.name=qBittorrent
      - homepage.icon=qbittorrent.png
      - homepage.href=/qbittorrent
      - homepage.description=Bittorrent client
      - homepage.weight=5
      - homepage.widget.type=qbittorrent
      - homepage.widget.url=http://qbittorrent:8080
      - homepage.widget.username=admin
      - homepage.widget.password=adminadmin
  # vpn:
  #   image: thrnz/docker-wireguard-pia
  #   container_name: vpn
  #   volumes:
  #     - ./pia:/pia
  #     - ./pia-shared:/pia-shared
  #   cap_add:
  #     - NET_ADMIN
  #     - SYS_MODULE
  #   environment:
  #     - LOC=${PIA_LOCATION}
  #     - USER=${PIA_USER}
  #     - PASS=${PIA_PASS}
  #     - LOCAL_NETWORK=${PIA_LOCAL_NETWORK}
  #     - PORT_FORWARDING=1
  #     - PORT_PERSIST=1
  #     - PORT_SCRIPT=/pia-shared/portupdate-qbittorrent.sh
  #     - EXIT_ON_FATAL=1
  #   sysctls:
  #     - net.ipv4.conf.all.src_valid_mark=1
  #     - net.ipv6.conf.default.disable_ipv6=1
  #     - net.ipv6.conf.all.disable_ipv6=1
  #     - net.ipv6.conf.lo.disable_ipv6=1
  #   healthcheck:
  #     test: ping -c 1 www.google.com || exit 1
  #     interval: 30s
  #     timeout: 10s
  #     retries: 3
  #   restart: always
  #   labels:
  #     # network mode is not supported: https://github.com/containrrr/watchtower/issues/1286#issuecomment-1214291660
  #     - com.centurylinklabs.watchtower.enable=false
  jellyfin:
    image: jellyfin/jellyfin
    container_name: jellyfin
    environment:
      - PUID=${USER_ID}
      - PGID=${GROUP_ID}
      - TZ=${TIMEZONE}
      - JELLYFIN_PublishedServerUrl=${HOSTNAME}/jellyfin
    group_add:
      - "109" # Change this to match your "render" host group id and remove this comment
    volumes:
      - ./jellyfin/config:/config
      - ./jellyfin:/cache
      - /home/serveradmin/public:/data
    ports:
      - "7359:7359/udp"
      - "1900:1900/udp"
      - "8096:8096"
    devices:
      - /dev/dri/renderD128:/dev/dri/renderD128
      # - /dev/dri/card0:/dev/dri/card0
    restart: always
    labels:
#      - traefik.enable=true
#      - traefik.http.routers.jellyfin.rule=(Host(`${HOSTNAME}`) && PathPrefix(`/jellyfin`))
#      - traefik.http.routers.jellyfin.tls=true
#      - traefik.http.routers.jellyfin.tls.certresolver=myresolver
#      - traefik.http.services.jellyfin.loadbalancer.server.port=8096
      - homepage.group=Media
      - homepage.name=Jellyfin
      - homepage.icon=jellyfin.png
      - homepage.href=http://10.21.21.23:8096
      - homepage.description=Media server
      - homepage.weight=3
      - homepage.widget.type=jellyfin
      - homepage.widget.url=http://10.21.21.23:8096
      - homepage.widget.key=${JELLYFIN_API_KEY}
  ....

networks:
  default:
    name: docker-compose-nas
```

The changes that have been made is disabling the VPN as we don't need our qBittorrent container behind a VPN. *This is not recommended*.
The second change made is commenting out the traefik labels in the Jellyfin container because of the issues we had when trying to set this up.


Once this has been done we can spin up the docker container.

## Quick Start

For the first time, run `./update-config.sh` to update the applications base URLs and set the API keys in `.env`.

If you want to show Jellyfin information in the homepage, create it in Jellyfin settings and fill `JELLYFIN_API_KEY`.

## Cloudflare Tunnels

We use Cloudflare Tunnels so that we can gain access to this stack outside of our home network.

The documentation for cloudflare tunnels can be found [here](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=&cad=rja&uact=8&ved=2ahUKEwjduPbMnLeBAxX-VfEDHbX4AkYQFnoECBgQAQ&url=https%3A%2F%2Fdevelopers.cloudflare.com%2Fcloudflare-one%2Fconnections%2Fconnect-networks%2F&usg=AOvVaw1eHe0lSHXFaD0OH3rwJ4Z0&opi=89978449)


## Laptop Specific Configuration

If the server is installed on a laptop, you may want to disable the suspension when the lid is closed:
```console
sudo nano /etc/systemd/logind.conf
```

Replace:
- `#HandleLidSwitch=suspend` by `HandleLidSwitch=ignore`
- `#LidSwitchIgnoreInhibited=yes` by `LidSwitchIgnoreInhibited=no`

Then restart: 
```console
sudo service systemd-logind restart
```
