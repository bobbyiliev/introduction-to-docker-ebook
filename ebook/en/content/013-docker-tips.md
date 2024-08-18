# Chapter 13: Advanced Docker Concepts and Features

As you become more proficient with Docker, you'll encounter more advanced concepts and features. This chapter explores some of these topics to help you take your Docker skills to the next level even though this is beyond the scope of this introductory ebook.

## 1. Multi-stage Builds

Multi-stage builds allow you to create more efficient Dockerfiles by using multiple FROM statements in your Dockerfile.

```dockerfile
# Build stage
FROM golang:1.16 AS builder
WORKDIR /app
COPY . .
RUN go build -o main .

# Final stage
FROM alpine:latest
RUN apk --no-cache add ca-certificates
WORKDIR /root/
COPY --from=builder /app/main .
CMD ["./main"]
```

This approach reduces the final image size by only including necessary artifacts from the build stage.

## 2. Docker BuildKit

BuildKit is a next-generation build engine for Docker. Enable it by setting an environment variable:

```bash
export DOCKER_BUILDKIT=1
```

BuildKit offers faster builds, better cache management, and advanced features like:

- Concurrent dependency resolution
- Efficient instruction caching
- Automatic garbage collection

## 3. Custom Bridge Networks

Create isolated network environments for your containers:

```bash
docker network create --driver bridge isolated_network
docker run --network=isolated_network --name container1 -d nginx
docker run --network=isolated_network --name container2 -d nginx
```

Containers on this network can communicate using their names as hostnames.

## 4. Docker Contexts

Manage multiple Docker environments with contexts:

```bash
# Create a new context
docker context create my-remote --docker "host=ssh://user@remote-host"

# List contexts
docker context ls

# Switch context
docker context use my-remote
```

## 5. Docker Content Trust (DCT)

DCT provides a way to verify the integrity and publisher of images:

```bash
# Enable DCT
export DOCKER_CONTENT_TRUST=1

# Push a signed image
docker push myrepo/myimage:latest
```

## 6. Docker Secrets

Manage sensitive data with Docker secrets:

```bash
# Create a secret
echo "mypassword" | docker secret create my_secret -

# Use the secret in a service
docker service create --name myservice --secret my_secret myimage
```

## 7. Docker Health Checks

Implement custom health checks in your Dockerfile:

```dockerfile
HEALTHCHECK --interval=30s --timeout=10s CMD curl -f http://localhost/ || exit 1
```

## 8. Docker Plugins

Extend Docker's functionality with plugins:

```bash
# Install a plugin
docker plugin install vieux/sshfs

# Use the plugin
docker volume create -d vieux/sshfs -o sshcmd=user@host:/path sshvolume
```

## 9. Docker Experimental Features

Enable experimental features in your Docker daemon config (`/etc/docker/daemon.json`):

```json
{
  "experimental": true
}
```

This unlocks features like:
- Checkpoint and restore
- Rootless mode

## 10. Container Escape Protection

Use security options to prevent container escapes:

```bash
docker run --security-opt="no-new-privileges:true" --cap-drop=ALL myimage
```

## 11. Custom Dockerfile Instructions

Create custom Dockerfile instructions using ONBUILD:

```dockerfile
ONBUILD ADD . /app/src
ONBUILD RUN /usr/local/bin/python-build --dir /app/src
```

## 12. Docker Manifest

Create and push multi-architecture images:

```bash
docker manifest create myrepo/myimage myrepo/myimage:amd64 myrepo/myimage:arm64
docker manifest push myrepo/myimage
```

## 13. Docker Buildx

Buildx is a CLI plugin that extends the docker build command with the full support of the features provided by BuildKit:

```bash
# Create a new builder instance
docker buildx create --name mybuilder

# Build and push multi-platform images
docker buildx build --platform linux/amd64,linux/arm64 -t myrepo/myimage:latest --push .
```

## 14. Docker Compose Profiles

Use profiles in Docker Compose to selectively start services:

```yaml
services:
  frontend:
    image: frontend
    profiles: ["frontend"]
  backend:
    image: backend
    profiles: ["backend"]
```

Start specific profiles:

```bash
docker-compose --profile frontend up -d
```

## Conclusion

These advanced Docker concepts and features provide powerful tools for optimizing your Docker workflows, improving security, and extending Docker's capabilities. As you incorporate these techniques into your projects, you'll be able to create more efficient, secure, and flexible Docker environments.
