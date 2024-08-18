# Chapter 4: What are Docker Images

A Docker image is a lightweight, standalone, and executable package that includes everything needed to run a piece of software, including the code, runtime, system tools, libraries, and settings. Images are the building blocks of Docker containers.

## Key Concepts

1. **Layers**: Images are composed of multiple layers, each representing a set of changes to the filesystem.
2. **Base Image**: The foundation of an image, typically a minimal operating system.
3. **Parent Image**: An image that your image is built upon.
4. **Image Tags**: Labels used to version and identify images.
5. **Image ID**: A unique identifier for each image.

## Working with Docker Images

### Listing Images

To see all images on your local system:

```bash
docker images
```

Or use the more verbose command:

```bash
docker image ls
```

### Pulling Images from Docker Hub

To download an image from Docker Hub:

```bash
docker pull <image_name>:<tag>
```

Example:

```bash
docker pull ubuntu:20.04
```

If no tag is specified, Docker will pull the `latest` tag by default.

### Running Containers from Images

To run a container from an image:

```bash
docker run <image_name>:<tag>
```

Example:

```bash
docker run -it ubuntu:20.04 /bin/bash
```

### Image Information

To get detailed information about an image:

```bash
docker inspect <image_name>:<tag>
```

### Removing Images

To remove an image:

```bash
docker rmi <image_name>:<tag>
```

or

```bash
docker image rm <image_name>:<tag>
```

To remove all unused images:

```bash
docker image prune
```

## Building Custom Images

### Using a Dockerfile

1. Create a file named `Dockerfile` with no extension.
2. Define the instructions to build your image.

Example Dockerfile:

```dockerfile
FROM ubuntu:20.04
RUN apt-get update && apt-get install -y nginx
COPY ./my-nginx.conf /etc/nginx/nginx.conf
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

3. Build the image:

```bash
docker build -t my-nginx:v1 .
```

### Building from a Running Container

1. Make changes to a running container.
2. Create a new image from the container:

```bash
docker commit <container_id> my-new-image:tag
```

## Image Tagging

To tag an existing image:

```bash
docker tag <source_image>:<tag> <target_image>:<tag>
```

Example:

```bash
docker tag my-nginx:v1 my-dockerhub-username/my-nginx:v1
```

## Pushing Images to Docker Hub

1. Log in to Docker Hub:

```bash
docker login
```

2. Push the image:

```bash
docker push my-dockerhub-username/my-nginx:v1
```

## Image Layers and Caching

Understanding layers is crucial for optimizing image builds:

1. Each instruction in a Dockerfile creates a new layer.
2. Layers are cached and reused in subsequent builds.
3. Ordering instructions from least to most frequently changing can speed up builds.

Example of leveraging caching:

```dockerfile
FROM ubuntu:20.04
RUN apt-get update && apt-get install -y nginx
COPY ./static-files /var/www/html
COPY ./config-files /etc/nginx
```

## Multi-stage Builds

Multi-stage builds allow you to use multiple FROM statements in your Dockerfile. This is useful for creating smaller production images.

Example:

```dockerfile
# Build stage
FROM golang:1.16 AS build
WORKDIR /app
COPY . .
RUN go build -o myapp

# Production stage
FROM alpine:3.14
COPY --from=build /app/myapp /usr/local/bin/myapp
CMD ["myapp"]
```

## Image Scanning and Security

Docker provides built-in image scanning capabilities:

```bash
docker scan <image_name>:<tag>
```

This helps identify vulnerabilities in your images.

## Best Practices for Working with Images

1. Use specific tags instead of `latest` for reproducibility.
2. Keep images small by using minimal base images and multi-stage builds.
3. Use `.dockerignore` to exclude unnecessary files from the build context.
4. Leverage build cache by ordering Dockerfile instructions effectively.
5. Regularly update base images to get security patches.
6. Scan images for vulnerabilities before deployment.

## Image Management and Cleanup

To manage disk space, regularly clean up unused images:

```bash
docker system prune -a
```

This removes all unused images, not just dangling ones.

## Conclusion

Docker images are a fundamental concept in containerization. They provide a consistent and portable way to package applications and their dependencies. By mastering image creation, optimization, and management, you can significantly improve your Docker workflows and application deployments.
