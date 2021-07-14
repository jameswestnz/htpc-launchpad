# Configuration
Create a `.env` file in the root with the following values:

```sh
TAILSCALE_AUTH= # Required. Configure here: https://login.tailscale.com/admin/settings/authkeys
TRANSMISSION_PASSWORD= # Transmission password, required for sonarr/radarr
TRANSMISSION_DOWNLOAD_PATH= # Optional, but recommended. Absolute path to download directory
SONARR_TV_PATH= # Optional, but recommended. Absolute path to tv shows directory
RADARR_MOVIES_PATH= # Optional, but recommended. Absolute path to movies directory
DOCKER_SUBNET= # Optional. Defaults to 172.50.0.0/24, may conflict with existing networks
TRAEFIK_IP_ADDRESS= # Optional. Must be within DOCKER_SUBNET. Defaults to 172.50.0.1
```

# Usage
## Start services:
```sh
docker compose up -d
```

If you only want to start specific services:
```
docker compose up -d transmission sonarr
```

# Tailscale
## Access services when connected to Tailscale
```
http://transmission.172.50.0.1.nip.io
http://jackett.172.50.0.1.nip.io
http://sonarr.172.50.0.1.nip.io
http://radarr.172.50.0.1.nip.io
http://trafik.172.50.0.1.nip.io/dashboard/
```
