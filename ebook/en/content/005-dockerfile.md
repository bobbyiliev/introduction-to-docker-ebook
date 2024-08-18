# Chapter 5: What is a Dockerfile

A Dockerfile is a text document that contains a series of instructions and arguments. These instructions are used to create a Docker image automatically. It's essentially a script of successive commands Docker will run to assemble an image, automating the image creation process.

## Anatomy of a Dockerfile

A Dockerfile typically consists of the following components:

1. Base image declaration
2. Metadata and label information
3. Environment setup
4. File and directory operations
5. Dependency installation
6. Application code copying
7. Execution command specification

Let's dive deep into each of these components and the instructions used to implement them.

## Dockerfile Instructions

### FROM

The `FROM` instruction initializes a new build stage and sets the base image for subsequent instructions.

```dockerfile
FROM ubuntu:20.04
```

This instruction is typically the first one in a Dockerfile. It's possible to have multiple `FROM` instructions in a single Dockerfile for multi-stage builds.

### LABEL

`LABEL` adds metadata to an image in key-value pair format.

```dockerfile
LABEL version="1.0" maintainer="john@example.com" description="This is a sample Docker image"
```

Labels are useful for image organization, licensing information, annotations, and other metadata.

### ENV

`ENV` sets environment variables in the image.

```dockerfile
ENV APP_HOME=/app NODE_ENV=production
```

These variables persist when a container is run from the resulting image.

### WORKDIR

`WORKDIR` sets the working directory for any subsequent `RUN`, `CMD`, `ENTRYPOINT`, `COPY`, and `ADD` instructions.

```dockerfile
WORKDIR /app
```

If the directory doesn't exist, it will be created.

### COPY and ADD

Both `COPY` and `ADD` instructions copy files from the host into the image.

```dockerfile
COPY package.json .
ADD https://example.com/big.tar.xz /usr/src/things/
```

`COPY` is generally preferred for its simplicity. `ADD` has some extra features like tar extraction and remote URL support, but these can make build behavior less predictable.

### RUN

`RUN` executes commands in a new layer on top of the current image and commits the results.

```dockerfile
RUN apt-get update && apt-get install -y nodejs
```

It's a best practice to chain commands with `&&` and clean up in the same `RUN` instruction to keep layers small.

### CMD

`CMD` provides defaults for an executing container. There can only be one `CMD` instruction in a Dockerfile.

```dockerfile
CMD ["node", "app.js"]
```

`CMD` can be overridden at runtime.

### ENTRYPOINT

`ENTRYPOINT` configures a container that will run as an executable.

```dockerfile
ENTRYPOINT ["nginx", "-g", "daemon off;"]
```

`ENTRYPOINT` is often used in combination with `CMD`, where `ENTRYPOINT` defines the executable and `CMD` supplies default arguments.

### EXPOSE

`EXPOSE` informs Docker that the container listens on specified network ports at runtime.

```dockerfile
EXPOSE 80 443
```

This doesn't actually publish the port; it functions as documentation between the person who builds the image and the person who runs the container.

### VOLUME

`VOLUME` creates a mount point and marks it as holding externally mounted volumes from native host or other containers.

```dockerfile
VOLUME /data
```

This is useful for any mutable and/or user-serviceable parts of your image.

### ARG

`ARG` defines a variable that users can pass at build-time to the builder with the `docker build` command.

```dockerfile
ARG VERSION=latest
```

This allows for more flexible image builds.

## Best Practices for Writing Dockerfiles

1. **Use multi-stage builds**: This helps create smaller final images by separating build-time dependencies from runtime dependencies.

```dockerfile
FROM node:14 AS build
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=build /app/dist /usr/share/nginx/html
```

2. **Minimize the number of layers**: Combine commands where possible to reduce the number of layers and image size.

3. **Leverage build cache**: Order instructions from least to most frequently changing to maximize build cache usage.

4. **Use `.dockerignore`**: Exclude files not relevant to the build, similar to `.gitignore`.

5. **Don't install unnecessary packages**: Keep the image lean and secure by only installing what's needed.

6. **Use specific tags**: Avoid `latest` tag for base images to ensure reproducible builds.

7. **Set the `WORKDIR`**: Always use `WORKDIR` instead of proliferating instructions like `RUN cd … && do-something`.

8. **Use `COPY` instead of `ADD`**: Unless you explicitly need the extra functionality of `ADD`, use `COPY` for transparency.

9. **Use environment variables**: Especially for version numbers and paths, making the Dockerfile more flexible.

## Advanced Dockerfile Concepts

### Health Checks

You can use the `HEALTHCHECK` instruction to tell Docker how to test a container to check that it's still working.

```dockerfile
HEALTHCHECK --interval=30s --timeout=10s CMD curl -f http://localhost/ || exit 1
```

### Shell and Exec Forms

Many Dockerfile instructions can be specified in shell form or exec form:

- Shell form: `RUN apt-get install python3`
- Exec form: `RUN ["apt-get", "install", "python3"]`

The exec form is preferred as it's more explicit and avoids issues with shell string munging.

### BuildKit

BuildKit is a new backend for Docker builds that offers better performance, storage management, and features. You can enable it by setting an environment variable:

```bash
export DOCKER_BUILDKIT=1
```

## Conclusion

Dockerfiles are a powerful tool for creating reproducible, version-controlled Docker images. By mastering Dockerfile instructions and best practices, you can create efficient, secure, and portable applications. Remember that writing good Dockerfiles is an iterative process – continually refine your Dockerfiles as you learn more about your application's needs and Docker's capabilities.
