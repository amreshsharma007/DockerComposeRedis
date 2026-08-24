# Redis Replication Setup Using Docker Compose

This repository provides a `docker-compose.yaml` file to set up a Redis replication cluster using Bitnami's Redis Docker images. The setup includes one Redis master node and two replica nodes.

## Services Overview

### 1. **redis-master**
- **Image**: `bitnami/redis:latest`
- **Ports**: 6370 (exposed on the host machine)
- **Environment Variables**:
  - `REDIS_REPLICATION_MODE`: master
  - `REDIS_PASSWORD`: `12345678`
  - `REDIS_PORT_NUMBER`: 6370
- **Volumes**:
  - `./src/redis/data:/bitnami/redis/data`: Maps the local Redis data directory to Bitnami's expected data path.
- **User**:
  - Bitnami Redis runs as a non-root user (`1001`) by default. The bind-mounted `./src/redis/data` directory must be owned by UID/GID `1001:1001` on the host.
  - If you see `Permission denied` errors for `appendonlydir` or `redis.conf`, fix the host directory permissions before starting the stack.
- **Network**: `redis`

### 2. **redis-replica1**
- **Image**: `bitnami/redis:latest`
- **Ports**: 6371 (exposed on the host machine)
- **Depends On**: `redis-master`
- **Environment Variables**:
  - `REDIS_REPLICATION_MODE`: slave
  - `REDIS_MASTER_HOST`: `redis-master`
  - `REDIS_MASTER_PORT_NUMBER`: 6370
  - `REDIS_MASTER_PASSWORD`: `12345678`
  - `REDIS_PASSWORD`: `my_replica_password`
  - `REDIS_PORT_NUMBER`: 6371
- **Network**: `redis`

### 3. **redis-replica2**
- **Image**: `bitnami/redis:latest`
- **Ports**: 6372 (exposed on the host machine)
- **Depends On**: `redis-master`
- **Environment Variables**:
  - `REDIS_REPLICATION_MODE`: slave
  - `REDIS_MASTER_HOST`: `redis-master`
  - `REDIS_MASTER_PORT_NUMBER`: 6370
  - `REDIS_MASTER_PASSWORD`: `12345678`
  - `REDIS_PASSWORD`: `my_replica_password`
  - `REDIS_PORT_NUMBER`: 6372
- **Network**: `redis`

### Network Configuration
- **Network Name**: `redis`
- **Driver**: `bridge`

## Prerequisites
1. Ensure that Docker and Docker Compose are installed on your system.
2. Clone or download this repository to your local machine.

## Usage

### Starting the Cluster
1. Navigate to the directory containing the `docker-compose.yaml` file.
2. Run the following command to start the Redis cluster:
   ```bash
   docker-compose up -d
   ```

### Accessing Redis Instances
- **Redis Master**: Connect to `localhost:6370`
- **Redis Replica 1**: Connect to `localhost:6371`
- **Redis Replica 2**: Connect to `localhost:6372`

### Stopping the Cluster
To stop and remove the containers, run:
```bash
docker-compose down
```

## Customization
- **Master Password**: Update `REDIS_PASSWORD` in the `docker-compose.yaml` file.
- **Replica Password**: Update `REDIS_PASSWORD` for replicas in the `docker-compose.yaml` file.
- **Ports**: Modify the port mappings (e.g., `6370:6370`) to fit your requirements.

## Notes
- The `ALLOW_EMPTY_PASSWORD` environment variable is commented out for better security. Use it only for development purposes.
- This setup uses Bitnami's Redis Docker images for robust and production-ready configurations.

## References
- [Bitnami Redis Docker Image Documentation](https://hub.docker.com/r/bitnami/redis)
