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

### Traefik network label

The `traefik.docker.network` label explicitly tells Traefik which network to use to reach the service.

```yaml
labels:
  - "traefik.docker.network=${TRAEFIK_NET}"
```

**Why is this necessary?**

Traefik's Docker provider can have a default network configured (e.g., `traefik-sites`). Services on this default network don't need the label. However, if your service uses a different network (e.g., `traefik-tools`), the label overrides the default for this specific container.

This label is also useful when a container is connected to multiple networks (e.g., `traefik-net` + `service-net`). Without it, Traefik iterates over the container's networks in a non-deterministic order and may pick one where it's not connected, causing connection failures.

### Required modifications

- **`compose.yaml`**: Complete the image name (or build configuration), volumes, and additional environment variables as needed
- **`compose.override.yaml`**: Create this file for local overrides (not tracked by git)
- **`.docker/`**: Adapt this directory to store your service volumes (data, config, etc.)
- **`README.md`**: Replace this file content with documentation specific to your service
- **`CHANGELOG.md`**: Replace this file to track your service version history

### External services

If your service needs to communicate with services from other projects (other compose.yaml files), use an external network. This network must be created manually and will remain available even when containers are stopped.

1. Create the external network (once, on the host).

    ```bash
    docker network create my-service-net
    # or, to isolate the network from external access:
    docker network create --internal my-service-net
    ```

2. Uncomment `service-net` (in both `services.*.networks` and `networks` sections).

    ```yaml
    // compose.yaml or compose.override.yaml
    services:
      my-service:
        # ...
        networks:
          - service-net
          - traefik-net

    networks:
      service-net:
        external: true
        name: ${SERVICE_NET}
      # ...
    ```

3. Uncomment + edit `SERVICE_NET` to match the network name.

    ```dotenv
    // .env
    SERVICE_NET=my-service-net
    ```

4. In other projects that need to communicate with this service, add the same external network configuration.

    ```yaml
    // other-project/compose.yaml
    services:
      other-service:
        # ...
        networks:
          - my-service-net
          # ...

    networks:
      my-service-net:
        external: true
      # ...
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

### Secrets

If your service needs sensitive data (passwords, API keys, etc.), use Docker secrets instead of environment variables for better security.

1. Create your secret file in `.docker/.secrets/`.

    ```bash
    echo -n "my-secret-value" > .docker/.secrets/service-secret
    ```

2. Uncomment `secrets` (in both `services.*.secrets` and root `secrets` sections).

    ```yaml
    // compose.yaml or compose.override.yaml
    services:
      my-service:
        # ...
        secrets:
          - service-secret
        # ...

    secrets:
      service-secret:
        file: .docker/.secrets/service-secret.ext
    ```

3. In your container, secrets are available at `/run/secrets/<secret-name>`.

## Resources

- Docker Compose documentation : <https://docs.docker.com/compose/gettingstarted>
- Traefik documentation : <https://doc.traefik.io/traefik/getting-started/install-traefik/>
