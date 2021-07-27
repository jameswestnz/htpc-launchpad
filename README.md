# HTPC Launchpad
This project brings together common HTPC-related services into a single docker compose configuration.

## Goals
- Minimal configuration
- Remote access
- Portable

## Configuration
### Global
Create a `.env` file in the root directory with the following optional values:
```sh
# Global environment variables required by multiple services
DOCKER_SUBNET=172.50.0.0/24 # Defaults to 172.50.0.0/24. May conflict with existing networks.
TAILSCALE_SEARCH_DOMAIN=htpc.internal # Defaults to `htpc.internal`.
TRAEFIK_IP_ADDRESS=172.50.0.254 # Must be within DOCKER_SUBNET. Defaults to 172.50.0.254.
TRAEFIK_HOST_PORT=80 # Defaults to 80.
TRAEFIK_COREDNS_PORT=53 # Defaults to 53.
TRAEFIK_TRANSMISSION_PORT=51413 # Defaults to 51413.
TRANSMISSION_DOCKER_IMAGE=ghcr.io/linuxserver/transmission:latest # or: `haugene/transmission-openvpn:latest` to use OpenVPN settings.
TRANSMISSION_DOWNLOAD_PATH=./data/transmission/downloads # Recommended. Absolute path to download directory.
SONARR_TV_PATH=./data/sonarr/tv # Recommended. Absolute path to tv shows directory.
RADARR_MOVIES_PATH=RADARR_MOVIES_PATH # Recommended. Absolute path to movies directory.

# Enable service-specific environment files
TRANSMISSION_ENV_FILE=./env/transmission.env
JACKETT_ENV_FILE=./env/jackett.env
SONARR_ENV_FILE=./env/sonarr.env
RADARR_ENV_FILE=./env/radarr.env
OVERSEERR_ENV_FILE=./env/overseerr.env
TAILSCALE_ENV_FILE=./env/tailscale.env
COREDNS_ENV_FILE=./env/coredns.env
TRAEFIK_ENV_FILE=./env/traefik.env
```

### Service-specific
Service-specific environment files are stored in the `env` directory. Services with default environment variables will also have a `{service}.default.env` file which should not be modified.

#### Transmission
`env/transmission.env`
```sh
PASSWORD= # Recommended. Empty by default.

# haugene/transmission-openvpn options. See here for more information: https://haugene.github.io/docker-transmission-openvpn/
OPENVPN_PROVIDER=
OPENVPN_CONFIG=
OPENVPN_USERNAME=
OPENVPN_PASSWORD=
```

## Usage
### Start services:
```sh
docker compose up -d
```

If you only want to start specific services:
```
docker compose up -d transmission sonarr
```

## Tailscale
Tailscale enables remote devices to access services within the docker network. There are a few steps to get started, but generally don't need changing once setup.
### Authentication
**Note**: Required on first run, or any time you need to change any settings within `tailscale up`.
```sh
docker compose run tailscale \
  tailscale up \
    --advertise-routes=172.50.0.0/24 \
    --advertise-exit-node \
    --hostname=htpc
```

### Split-DNS
Split-DNS enables use of the `.htpc.internal` domain for Tailscale-connected devices. See more about split-DNS here: https://tailscale.com/kb/1054/dns/

Steps to enable the `.htpc.internal` domain:
- Enable "Magic DNS" within the Tailscale dashboard
- Configure "Global Nameservers" to a public service such as Google (8.8.8.8, 8.8.4.4) or Cloudflare (1.1.1.1)
- Add another nameserver equal to the `TRAEFIK_IP_ADDRESS` in your `.env` file (the default is `172.50.0.254`). Use the "Restrict to search domain" option, providing your `TAILSCALE_SEARCH_DOMAIN` value (`htpc.internal` by default) as the search domain

## Accessing services
Services will be accessible on the `.htpc.internal` domain once everything is configured correctly. The service-specific domains are as follows:
```
http://transmission.htpc.internal
http://jackett.htpc.internal
http://sonarr.htpc.internal
http://radarr.htpc.internal
http://overseerr.htpc.internal
http://traefik.htpc.internal
```

### From the host machine
Services are only exposed via Traefik, which requires use of the `.htpc.internal` domain. There are two options to access this domain from the host machine:
- **The preferred option**: Add an entry to your hosts file, pointing each of the service domains (`{service}.htpc.internal`) to your local IP; or
- Run the Tailscale client on your host
