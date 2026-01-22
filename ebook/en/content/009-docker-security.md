# Chapter 9: Docker Security Best Practices

Security is a critical aspect of working with Docker, especially in production environments. This chapter will cover essential security practices to help you build and maintain secure Docker environments.

## 1. Keep Docker Updated

Always use the latest version of Docker to benefit from the most recent security patches.

```bash
sudo apt-get update
sudo apt-get upgrade docker-ce
```

## 2. Use Official Images

Whenever possible, use official images from Docker Hub or trusted sources. These images are regularly updated and scanned for vulnerabilities.

```yaml
version: '3.8'
services:
  web:
    image: nginx:latest  # Official Nginx image
```

## 3. Scan Images for Vulnerabilities

Use tools like Docker Scout or Trivy to scan your images for known vulnerabilities.

```bash
docker scout cve <image_name>
```

## 4. Limit Container Resources

Prevent Denial of Service attacks by limiting container resources:

```yaml
version: '3.8'
services:
  web:
    image: nginx:latest
    deploy:
      resources:
        limits:
          cpus: '0.50'
          memory: 50M
```

## 5. Use Non-Root Users

Run containers as non-root users to limit the potential impact of a container breach. This follows the principle of least privilege and significantly reduces security risks.

### 5.1 Creating a Dedicated User

For Debian/Ubuntu-based images:

```dockerfile
FROM node:14

# Create a non-root user with explicit UID/GID for better reproducibility
RUN groupadd -r myapp --gid=10001 && \
    useradd -r -g myapp --uid=10001 myuser

WORKDIR /app

# Copy application files with correct ownership to avoid a separate chown layer
COPY --chown=myuser:myapp . /app

# Switch to the non-root user
USER myuser

CMD ["node", "app.js"]
```

For Alpine-based images:

```dockerfile
FROM alpine:3.19

# Create a non-root user with explicit UID/GID
RUN addgroup -S myapp -g 10001 && \
    adduser -S -G myapp -u 10001 myuser

WORKDIR /app

# Copy with proper ownership
COPY --chown=myuser:myapp . /app

USER myuser

CMD ["./app"]
```

### 5.2 Running Existing Images as Non-Root

If you're using an existing image, you can specify a user at runtime:

```bash
# Run with numeric UID:GID (doesn't require the user to exist in the container)
docker run --user 10001:10001 nginx

# Or with docker-compose
```

```yaml
version: '3.8'
services:
  web:
    image: nginx
    user: "10001:10001"
```

### 5.3 Best Practices for Non-Root Containers

- Use explicit UIDs/GIDs (e.g., 10001) rather than usernames for consistency
- Prefer `COPY --chown` over separate `RUN chown` commands to reduce image layers
- Set appropriate permissions on volumes and bind mounts
- Remember that ports below 1024 require root privileges (use higher ports)
- Test your container thoroughly to ensure the non-root user has sufficient permissions

## 6. Use Secret Management

For sensitive data like passwords and API keys, use Docker secrets:

```bash
echo "mysecretpassword" | docker secret create db_password -
```

Then in your docker-compose.yml:

```yaml
version: '3.8'
services:
  db:
    image: mysql
    secrets:
      - db_password
secrets:
  db_password:
    external: true
```

## 7. Enable Content Trust

Sign and verify image tags:

```bash
export DOCKER_CONTENT_TRUST=1
docker push myrepo/myimage:latest
```

## 8. Use Read-Only Containers

When possible, run containers in read-only mode:

```yaml
version: '3.8'
services:
  web:
    image: nginx
    read_only: true
    tmpfs:
      - /tmp
      - /var/cache/nginx
```

## 9. Implement Network Segmentation

Use Docker networks to isolate containers:

```yaml
version: '3.8'
services:
  frontend:
    networks:
      - frontend
  backend:
    networks:
      - backend
networks:
  frontend:
  backend:
```

## 10. Regular Security Audits

Regularly audit your Docker environment using tools like Docker Bench for Security:

```bash
docker run -it --net host --pid host --userns host --cap-add audit_control \
    -e DOCKER_CONTENT_TRUST=$DOCKER_CONTENT_TRUST \
    -v /var/lib:/var/lib \
    -v /var/run/docker.sock:/var/run/docker.sock \
    -v /usr/lib/systemd:/usr/lib/systemd \
    -v /etc:/etc --label docker_bench_security \
    docker/docker-bench-security
```

## 11. Use Security-Enhanced Linux (SELinux) or AppArmor

These provide an additional layer of security. Ensure they're enabled and properly configured on your host system.

## 12. Implement Logging and Monitoring

Use Docker's logging capabilities and consider integrating with external monitoring tools:

```yaml
version: '3.8'
services:
  web:
    image: nginx
    logging:
      driver: "json-file"
      options:
        max-size: "200k"
        max-file: "10"
```

## Conclusion

Implementing these security best practices will significantly improve the security posture of your Docker environments. Remember, security is an ongoing process, and it's important to stay informed about the latest security threats and Docker security features.
