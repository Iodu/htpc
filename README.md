> Based on [sebgl/htpc-download-box](https://github.com/sebgl/htpc-download-box).
> Mainly the VPN part is ~~stolen~~ borrowed from him.

# Setup

## Preparation

### Volume mountings

- create `.env` file including (edit them to your preferences or not)

```sh
CONFIG_DIR=./volumes/config
COMPLETE_DIR=./volumes/complete
DOWNLOAD_DIR=./volumes/downloads
SCRIPTS_DIR=./volumes/scripts
BLACK_HOLE_DIR=${DOWNLOAD_DIR}/blackhole
PGID=1000
PUID=1000
TZ=Europe/Amsterdam
```

- run [make_volume_dirs.sh](./make_volume_dirs.sh)
- run [make_config_dirs.sh](./make_config_dirs.sh)

### VPN (PIA)

create file `vpn.auth` in `config/vpn` containing:

```
<PIA username>
<PIA password>
```

and add the PIA OpenVPN configuration file (download this from the PIA website):

- Netherlands.ovpn
  Now rename this to `vpn.conf`

Now open the file and change the following:

- remove `persist-tun` (or comment out by prefixing with: `;`)
- edit `auth-user-pass` to `auth-user-pass /vpn/vpn.auth`
- and add to the end of the file:

```
keepalive 10 30
pull-filter ignore "auth-token"
```

## Bazarr

> This will try to remove ads from subtitles
> `curl https://raw.githubusercontent.com/brianspilner01/media-server-scripts/master/sub-clean.sh > ./volumes/scripts/bazarr/sub-clean.sh`

# Launching

use command:

```
docker-compose -f docker-compose.torrent-stack.yaml up --remove-orphans
```

## Configurations

### Sonarr and Radarr

> Since Radarr is a Sonarr fork, the settings and interface are pretty much the same.

> Both of these configs can be done either via their own `config.xml` files or via the GUI/webinterface.
> Settings I would change:

- `Rename Movies/Rename Episodes` to `Yes`
- `Ignore Deleted Episodes` What works for you.

#### Add qbittorrent as a downloadclient:

- host: `vpn`
  - remember we are routing d\*eluge via vpn
- username: `admin` (default)
- password: `adminadmin` (default)

### Bazarr

**If you have done the curl to retreive the `sub-clean.sh` file:**

Now set postprocessing command to: `/scripts/sub-clean.sh '{{subtitles}}' --`

Now connect sonarr and radarr to bazarr:

- Hostname/IP Adress: `sonarr` or `radarr`
- API: can be found `Settings -> General` in `sonarr` or `radarr`

# Sonarr, Lidarr, Bazarr

https://sdekraa.tweakblogs.net/blog/18708/docker-compose-voor-het-configureren-van-je-docker-applicaties
https://github.com/sebgl/htpc-download-box

https://github.com/vche/pycliarr

https://github.com/totaldebug/PyArr
