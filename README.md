# Tapomix / Docker-Service

Base template to configure a service installed with [Docker Compose](https://docs.docker.com/compose/) and running behind [Traefik](https://traefik.io/traefik).

## Installation

Copy the template from Github and specify a `PROJECT` name.  
Then reset the git history to start fresh.

```bash
git clone git@github.com:tapomix/skeleton-docker.git PROJECT
cd PROJECT
rm -rf .git
git init .
```

## Configuration

Copy the environment template file and edit it with your values:

```bash
cp .env.dist .env
```

### Environment Variables

| Variable | Description |
|----------|-------------|
| `CONTAINER_NAME` | Name of the Docker container |
| `SERVICE_VERSION` | Docker image version to use (default: `latest`) |
| `TRAEFIK_HOST` | Hostname for Traefik routing (default: `${CONTAINER_NAME}.docker.lan`) |
| `TRAEFIK_NET` | Traefik network name: `traefik-local` for dev, `traefik` for VPS |
| `TRAEFIK_PORT` | Port exposed by the service for Traefik |
| `UID` | User ID for file permissions (default: `1000`) |
| `GID` | Group ID for file permissions (default: `1000`) |
| `TZ` | Timezone (default: `Etc\Brussels`) |

> **Note:** All variables are required.

## Usage

This is a base template that needs to be adapted for each service you deploy.

### Required modifications

- **`compose.yaml`**: Complete the image name (or build configuration), volumes, and additional environment variables as needed
- **`compose.override.yaml`**: Create this file for local overrides (not tracked by git)
- **`.docker/`**: Adapt this directory to store your service volumes (data, config, etc.)
- **`README.md`**: Replace this file content with documentation specific to your service
- **`CHANGELOG.md`**: Replace this file to track your service version history

## Resources

- Docker Compose documentation : <https://docs.docker.com/compose/gettingstarted>
- Traefik documentation : <https://doc.traefik.io/traefik/getting-started/install-traefik/>
