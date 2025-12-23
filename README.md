# Tapomix / Docker - Service

Base template to configure a service installed with [Docker Compose](https://docs.docker.com/compose/) and running behind [Traefik](https://traefik.io/traefik).

## Installation

Copy the template from Github and specify a `<PROJECT>` name.  
Then reset the git history to start fresh.

```bash
git clone https://github.com/tapomix/skeleton-docker.git <PROJECT>
cd <PROJECT>
rm -rf .git
git init .
```

## Configuration

Copy the environment template file and edit it with your values :

```bash
cp .env.dist .env
```

### Environment Variables

| Variable | Required | Description |
| -------- | :------: | ----------- |
| `CONTAINER_NAME` | **Yes** | Name of the Docker container |
| `SERVICE_VERSION` | **Yes** | Docker image version to use (default: *`latest`*) |
| `SERVICE_NET` | No | Network for inter-service communication |
| `SOCKET_PROXY_NET` | No | Network for socket proxy |
| `TRAEFIK_HOST` | **Yes** | Hostname for Traefik routing (default: *`${CONTAINER_NAME}.domain.ext`*) |
| `TRAEFIK_NET` | **Yes** | Traefik network name (default: *`traefik-net`*) |
| `TRAEFIK_PORT` | **Yes** | Port exposed by the service for Traefik |
| `TZ` | **Yes** | Timezone (default: *`Etc/UTC`*) |
| `UID` | **Yes** | User ID for file permissions (default: *`1000`*) |
| `GID` | **Yes** | Group ID for file permissions (default: *`1000`*) |

## Usage

This is a base template that needs to be adapted for each service you deploy.

### Required modifications

- **`compose.yaml`**: Complete the image name (or build configuration), volumes, and additional environment variables as needed
- **`compose.override.yaml`**: Create this file for local overrides (not tracked by git)
- **`.docker/`**: Adapt this directory to store your service volumes (data, config, etc.)
- **`README.md`**: Replace this file content with documentation specific to your service
- **`CHANGELOG.md`**: Replace this file to track your service version history

### Multiple services

If your stack requires multiple services to communicate, it's possible with a common network shared by services.

1. Add a network (e.g., `service-net`) to connect them.

    ```yaml
    // compose.yaml or compose.override.yaml
    services:
      service-A:
        # ...
        networks:
        - service-net
        - traefik-net

      service-B:
        # ...
        networks:
        - service-net

    networks:
      # ...
      service-net:
        name: ${SERVICE_NET}
    ```

2. Uncomment + edit `SERVICE_NET`.

    ```dotenv
    // .env
    SERVICE_NET=${CONTAINER_NAME}
    ```

### Socket proxy

If your service needs access to the Docker socket, use a socket proxy instead of mounting the socket directly for better security.

1. Uncomment `socket-proxy-net` (in both `services.*.networks` and `networks` sections).

    ```yaml
    // compose.yaml or compose.override.yaml
    services:
      my-service:
        networks:
        - socket-proxy-net
      # ...

    networks:
      socket-proxy-net:
        external: true
        name: ${SOCKET_PROXY_NET}
      # ...
    ```

2. Uncomment + edit `SOCKET_PROXY_NET`.

    ```dotenv
    // .env
    SOCKET_PROXY_NET=socket-proxy-${CONTAINER_NAME}
    ```

## Resources

- Docker Compose documentation : <https://docs.docker.com/compose/gettingstarted>
- Traefik documentation : <https://doc.traefik.io/traefik/getting-started/install-traefik/>
