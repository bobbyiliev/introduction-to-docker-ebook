# Chapter 3: Working with Docker Containers

Docker containers are lightweight, standalone, and executable packages that include everything needed to run a piece of software, including the code, runtime, system tools, libraries, and settings. In this chapter, we'll explore how to work with Docker containers effectively.

## Running Your First Container

Let's start by running a simple container:

```bash
docker run hello-world
```

This command does the following:
1. Checks for the `hello-world` image locally
2. If not found, pulls the image from Docker Hub
3. Creates a container from the image
4. Runs the container, which prints a hello message
5. Exits the container

## Basic Docker Commands

Here are some essential Docker commands for working with containers:

### Listing Containers

To see all running containers:
```bash
docker ps
```

To see all containers (including stopped ones):
```bash
docker ps -a
```

### Starting and Stopping Containers

To stop a running container:
```bash
docker stop <container_id_or_name>
```

To start a stopped container:
```bash
docker start <container_id_or_name>
```

To restart a container:
```bash
docker restart <container_id_or_name>
```

### Removing Containers

To remove a stopped container:
```bash
docker rm <container_id_or_name>
```

To force remove a running container:
```bash
docker rm -f <container_id_or_name>
```

## Running Containers in Different Modes

### Detached Mode

Run a container in the background:
```bash
docker run -d <image_name>
```

### Interactive Mode

Run a container and interact with it:
```bash
docker run -it <image_name> /bin/bash
```

## Port Mapping

To map a container's port to the host:
```bash
docker run -p <host_port>:<container_port> <image_name>
```

Example:
```bash
docker run -d -p 80:80 nginx
```

## Working with Container Logs

View container logs:
```bash
docker logs <container_id_or_name>
```

Follow container logs in real-time:
```bash
docker logs -f <container_id_or_name>
```

## Executing Commands in Running Containers

To execute a command in a running container:
```bash
docker exec -it <container_id_or_name> <command>
```

Example:
```bash
docker exec -it my_container /bin/bash
```

## Practical Example: Running an Apache Container

Let's run an Apache web server container:

1. Pull the image:
   ```bash
   docker pull httpd
   ```

2. Run the container:
   ```bash
   docker run -d --name my-apache -p 8080:80 httpd
   ```

3. Verify it's running:
   ```bash
   docker ps
   ```

4. Access the default page by opening a web browser and navigating to `http://localhost:8080`

5. Modify the default page:
   ```bash
   docker exec -it my-apache /bin/bash
   echo "<h1>Hello from my Apache container!</h1>" > /usr/local/apache2/htdocs/index.html
   exit
   ```

6. Refresh your browser to see the changes

## Container Resource Management

### Limiting Memory

Run a container with a memory limit:
```bash
docker run -d --memory=512m <image_name>
```

### Limiting CPU

Run a container with CPU limit:
```bash
docker run -d --cpus=0.5 <image_name>
```

## Container Networking

### Listing Networks

```bash
docker network ls
```

### Creating a Network

```bash
docker network create my_network
```

### Connecting a Container to a Network

```bash
docker run -d --network my_network --name my_container <image_name>
```

## Data Persistence with Volumes

### Creating a Volume

```bash
docker volume create my_volume
```

### Running a Container with a Volume

```bash
docker run -d -v my_volume:/path/in/container <image_name>
```

## Container Health Checks

Docker provides built-in health checking capabilities. You can define a health check in your Dockerfile:

```dockerfile
HEALTHCHECK --interval=30s --timeout=10s --start-period=5s --retries=3 \
  CMD curl -f http://localhost/ || exit 1
```

## Cleaning Up

Remove all stopped containers:
```bash
docker container prune
```

Remove all unused resources (containers, networks, images):
```bash
docker system prune
```

## Conclusion

Working with Docker containers involves a range of operations from basic running and stopping to more advanced topics like resource management and networking. As you become more comfortable with these operations, you'll be able to leverage Docker's full potential in your development and deployment workflows.

Remember, containers are designed to be ephemeral. Always store important data in volumes or use appropriate persistence mechanisms for your applications.
