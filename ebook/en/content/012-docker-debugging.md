# Chapter 12: Docker Troubleshooting and Debugging

Even with careful planning and best practices, issues can arise when working with Docker. This chapter covers common problems you might encounter and provides strategies for effective troubleshooting and debugging.

## 1. Container Lifecycle Issues

### Container Won't Start

If a container fails to start, use these commands:

```bash
# View container logs
docker logs <container_id>

# Inspect container details
docker inspect <container_id>

# Check container status
docker ps -a
```

### Container Exits Immediately

For containers that exit right after starting:

```bash
# Run the container in interactive mode
docker run -it --entrypoint /bin/sh <image_name>

# Check the ENTRYPOINT and CMD in the Dockerfile
docker inspect --format='{{.Config.Entrypoint}}' <image_name>
docker inspect --format='{{.Config.Cmd}}' <image_name>
```

## 2. Networking Issues

### Container Can't Connect to Network

To troubleshoot network connectivity:

```bash
# Inspect network settings
docker network inspect <network_name>

# Check container's network settings
docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' <container_id>

# Use a network debugging container
docker run --net container:<container_id> nicolaka/netshoot
```

### Port Mapping Issues

If you can't access a container's exposed port:

```bash
# Check port mappings
docker port <container_id>

# Verify host machine's firewall settings
sudo ufw status

# Test the port directly on the container
docker exec <container_id> nc -zv localhost <port>
```

## 3. Storage and Volume Issues

### Data Persistence Problems

For issues with data not persisting:

```bash
# List volumes
docker volume ls

# Inspect a volume
docker volume inspect <volume_name>

# Check volume mounts in a container
docker inspect --format='{{range .Mounts}}{{.Source}} -> {{.Destination}}{{"\n"}}{{end}}' <container_id>
```

### Disk Space Issues

If you're running out of disk space:

```bash
# Check Docker disk usage
docker system df

# Remove unused data
docker system prune -a

# Identify large images
docker images --format "{{.Size}}\t{{.Repository}}:{{.Tag}}" | sort -h
```

## 4. Resource Constraints

### Container Using Too Much CPU or Memory

To identify and address resource usage issues:

```bash
# Monitor resource usage
docker stats

# Set resource limits
docker run --memory=512m --cpus=0.5 <image_name>

# Update limits for a running container
docker update --cpus=0.75 <container_id>
```

## 5. Image-related Issues

### Image Pull Failures

If you can't pull an image:

```bash
# Check Docker Hub status
curl -Is https://registry.hub.docker.com/v2/ | head -n 1

# Verify your Docker login
docker login

# Try pulling with verbose output
docker pull --verbose <image_name>
```

### Image Build Failures

For issues during image builds:

```bash
# Build with verbose output
docker build --progress=plain -t <image_name> .

# Check for issues in the Dockerfile
docker build --no-cache -t <image_name> .
```

## 6. Docker Daemon Issues

### Docker Daemon Won't Start

If the Docker daemon fails to start:

```bash
# Check Docker daemon status
sudo systemctl status docker

# View Docker daemon logs
sudo journalctl -u docker.service

# Restart Docker daemon
sudo systemctl restart docker
```

## 7. Debugging Techniques

### Interactive Debugging

To debug a running container interactively:

```bash
# Start an interactive shell in a running container
docker exec -it <container_id> /bin/bash

# Run a new container with a shell for debugging
docker run -it --entrypoint /bin/bash <image_name>
```

### Using Docker Events

Monitor Docker events for troubleshooting:

```bash
docker events
```

### Logging

Configure and view container logs:

```bash
# View container logs
docker logs <container_id>

# Follow log output
docker logs -f <container_id>

# Adjust logging driver
docker run --log-driver json-file --log-opt max-size=10m <image_name>
```

## 8. Performance Debugging

### Identifying Performance Bottlenecks

Use these commands to identify performance issues:

```bash
# Monitor container resource usage
docker stats

# Profile container processes
docker top <container_id>

# Use cAdvisor for more detailed metrics
docker run \
  --volume=/:/rootfs:ro \
  --volume=/var/run:/var/run:ro \
  --volume=/sys:/sys:ro \
  --volume=/var/lib/docker/:/var/lib/docker:ro \
  --volume=/dev/disk/:/dev/disk:ro \
  --publish=8080:8080 \
  --detach=true \
  --name=cadvisor \
  google/cadvisor:latest
```

## 9. Docker Compose Troubleshooting

For issues with Docker Compose:

```bash
# View logs for all services
docker-compose logs

# Rebuild and recreate containers
docker-compose up -d --build

# Check the configuration
docker-compose config
```

## Conclusion

Effective troubleshooting and debugging are essential skills for working with Docker. By understanding these techniques and tools, you can quickly identify and resolve issues in your Docker environment. Remember to always check the official Docker documentation and community forums for the most up-to-date information and solutions to common problems.
