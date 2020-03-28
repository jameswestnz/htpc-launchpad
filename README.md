# Configuration
Create a `.env` file in the root with the following values:

```sh
TRANSMISSION_DOWNLOAD_PATH= # absolute path to download directory
SONARR_TV_PATH= # absolute path to tv shows directory
RADARR_MOVIES_PATH= # absolute path to movies directory
TRANSMISSION_PASSWORD= # transmission password, required for sonarr/radarr
```

# Usage
## Start services:
```sh
docker-compose up -d
```

If you only want to start specific services:
```
docker-compose up -d transmission sonarr
```
