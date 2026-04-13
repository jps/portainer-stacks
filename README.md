# Portainer Stacks

A collection of Docker Compose stack files designed for deployment via [Portainer](https://www.portainer.io/). These stacks power a home server setup with media management, home automation, monitoring, and networking services.

## Stacks

| Stack | File | Description | Ports |
|-------|------|-------------|-------|
| [Arr Suite](#arr-suite) | `arr.yml` | Media automation (Prowlarr, Radarr, Sonarr, FlareSolverr) | 9696, 7878, 8989, 8191 |
| [Deluge](#deluge) | `deluge.yml` | BitTorrent client | 8112, 6881, 58846 |
| [Grafana](#grafana) | `grafana.yml` | Monitoring & dashboards | 3000 |
| [Home Assistant](#home-assistant) | `home-assistant.yml` | Home automation platform | Host networking |
| [MQTT](#mqtt) | `mqtt.yml` | Eclipse Mosquitto message broker | 58880, 58881 |
| [OpenClaw](#openclaw) | `openclaw.yml` | Personal AI assistant | 18789, 18790 |
| [Pi-hole](#pi-hole) | `pi-hole.yml` | Network-wide ad blocker & DNS | 53, 67, 80 |
| [Plex](#plex) | `plex.yml` | Media server | 32400 |

## Stack Details

### Arr Suite

The *arr stack provides automated media management:

- **Prowlarr** — Indexer manager that integrates with Radarr and Sonarr
- **Radarr** — Movie collection manager
- **Sonarr** — TV series collection manager
- **FlareSolverr** — Proxy server to bypass Cloudflare protection for indexers

### Deluge

A lightweight BitTorrent client with a web UI accessible on port 8112. Downloads are stored on the NAS at `/mnt/nas/downloads/deluge`.

### Grafana

Grafana OSS for building monitoring dashboards and visualising metrics. Accessible on port 3000.

### Home Assistant

A home automation platform running in privileged mode with host networking to allow device discovery. Configuration is persisted in a named Docker volume.

### MQTT

Eclipse Mosquitto MQTT broker for IoT device communication. Commonly used alongside Home Assistant. MQTT is exposed on port 58880 and WebSockets on port 58881.

### OpenClaw

A personal AI assistant. Workspace changes are automatically backed up to a Git repository on a configurable interval. By default, the sync service runs in **backup** mode — local changes are committed and pushed to GitHub. Set `OPENCLAW_SYNC_MODE` to `two-way` to also pull remote changes (conflicts are resolved in favour of local).

| Variable | Description |
|----------|-------------|
| `OPENCLAW_GATEWAY_TOKEN` | Token to secure the gateway API |
| `OPENCLAW_CONFIG_REPO` | HTTPS URL of the workspace config Git repository |
| `OPENCLAW_CONFIG_BRANCH` | Config repo branch (default: `main`) |
| `OPENCLAW_GIT_TOKEN` | GitHub personal access token (or fine-grained token) with push access to the config repo |
| `OPENCLAW_SYNC_INTERVAL` | Sync interval in seconds (default: `300`) |
| `OPENCLAW_SYNC_MODE` | `backup` (default) — push only; `two-way` — pull then push, local wins on conflicts |
| `OPENCLAW_GIT_USER_NAME` | Git author name for auto-backup commits (default: `openclaw-auto-backup`) |
| `OPENCLAW_GIT_USER_EMAIL` | Git author email for auto-backup commits (default: `openclaw@localhost`) |

### Pi-hole

Network-wide ad blocker that acts as a DNS sinkhole. Can optionally function as a DHCP server (requires `NET_ADMIN` capability). Configuration is stored on the NAS at `/mnt/nas/appdata/pihole`.

### Plex

Plex Media Server with hardware transcoding support via `/dev/dri` device passthrough. Media libraries are mounted from the NAS at `/mnt/nas`.

## Prerequisites

- Docker and Docker Compose
- [Portainer](https://docs.portainer.io/start/install-ce) (Community or Business Edition)
- NAS or shared storage mounted at `/mnt/nas` (used by most stacks for config and media)

## Usage

### Deploying with Portainer

1. Log in to your Portainer instance.
2. Navigate to **Stacks** → **Add stack**.
3. Select **Repository** as the build method.
4. Point to this repository and specify the compose file for the stack you want to deploy.
5. Configure any required environment variables and deploy.

### Deploying with Docker Compose

You can also deploy any stack directly with Docker Compose:

```bash
docker compose -f <stack>.yml up -d
```

For example:

```bash
docker compose -f plex.yml up -d
```

## Configuration

Most stacks share the following defaults:

| Setting | Value |
|---------|-------|
| Timezone | `Europe/London` |
| PUID / PGID | `1000` |
| Restart policy | `unless-stopped` |

Adjust these values in each stack file to match your environment.

## License

This project is provided as-is for personal use.
