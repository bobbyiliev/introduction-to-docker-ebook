# Chapter 8: Docker Compose

Docker Compose is a powerful tool for defining and running multi-container Docker applications. With Compose, you use a YAML file to configure your application's services, networks, and volumes. Then, with a single command, you create and start all the services from your configuration.

> **Note**: Docker Compose is now integrated into Docker CLI. The new command is `docker compose` instead of `docker-compose`. We'll use the new command throughout this chapter.

## Key Benefits of Docker Compose

1. **Simplicity**: Define your entire application stack in a single file.
2. **Reproducibility**: Easily share and version control your application configuration.
3. **Scalability**: Simple commands to scale services up or down.
4. **Environment Consistency**: Ensure development, staging, and production environments are identical.
5. **Workflow Improvement**: Compose can be used throughout the development cycle for testing, staging, and production.

## The docker-compose.yml File

The `docker-compose.yml` file is the core of Docker Compose. It defines all the components and configurations of your application. Here's a basic example:

```yaml
version: '3.8'
services:
  web:
    build: .
    ports:
      - "5000:5000"
    volumes:
      - .:/code
    environment:
      FLASK_ENV: development
  redis:
    image: "redis:alpine"
```

Let's break down this example:

- `version`: Specifies the Compose file format version.
- `services`: Defines the containers that make up your app.
- `web`: A service based on an image built from the Dockerfile in the current directory.
- `redis`: A service using the public Redis image.

## Key Concepts in Docker Compose

1. **Services**: Containers that make up your application.
2. **Networks**: How your services communicate with each other.
3. **Volumes**: Where your services store and access data.

## Basic Docker Compose Commands

- `docker compose up`: Create and start containers
  ```bash
  docker compose up -d  # Run in detached mode
  ```

- `docker compose down`: Stop and remove containers, networks, images, and volumes
  ```bash
  docker compose down --volumes  # Also remove volumes
  ```

- `docker compose ps`: List containers
- `docker compose logs`: View output from containers
  ```bash
  docker compose logs -f web  # Follow logs for the web service
  ```

## Advanced Docker Compose Features

### 1. Environment Variables

You can use .env files or set them directly in the compose file:

```yaml
version: '3.8'
services:
  web:
    image: "webapp:${TAG}"
    environment:
      - DEBUG=1
```

### 2. Extending Services

Use `extends` to share common configurations:

```yaml
version: '3.8'
services:
  web:
    extends:
      file: common-services.yml
      service: webapp
```

### 3. Healthchecks

Ensure services are ready before starting dependent services:

```yaml
version: '3.8'
services:
  web:
    image: "webapp:latest"
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost"]
      interval: 1m30s
      timeout: 10s
      retries: 3
      start_period: 40s
```


## Practical Examples

### Example 1: WordPress with MySQL

```yaml
version: '3.8'
services:
  db:
    image: mysql:5.7
    volumes:
      - db_data:/var/lib/mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: somewordpress
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: wordpress

  wordpress:
    depends_on:
      - db
    image: wordpress:latest
    ports:
      - "8000:80"
    restart: always
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_USER: wordpress
      WORDPRESS_DB_PASSWORD: wordpress
      WORDPRESS_DB_NAME: wordpress

volumes:
  db_data: {}
```

Let's break this down in detail:

1. **Version**:
   `version: '3.8'` specifies the version of the Compose file format. Version 3.8 is compatible with Docker Engine 19.03.0+.

2. **Services**:
   We define two services: `db` and `wordpress`.

   a. **db service**:
   - `image: mysql:5.7`: Uses the official MySQL 5.7 image.
   - `volumes`: Creates a named volume `db_data` and mounts it to `/var/lib/mysql` in the container. This ensures that the database data persists even if the container is removed.
   - `restart: always`: Ensures that the container always restarts if it stops.
   - `environment`: Sets up the MySQL environment variables:
     - `MYSQL_ROOT_PASSWORD`: Sets the root password for MySQL.
     - `MYSQL_DATABASE`: Creates a database named "wordpress".
     - `MYSQL_USER` and `MYSQL_PASSWORD`: Creates a new user with the specified password.

   b. **wordpress service**:
   - `depends_on`: Ensures that the `db` service is started before the `wordpress` service.
   - `image: wordpress:latest`: Uses the latest official WordPress image.
   - `ports`: Maps port 8000 on the host to port 80 in the container, where WordPress runs.
   - `restart: always`: Ensures the container always restarts if it stops.
   - `environment`: Sets up WordPress environment variables:
     - `WORDPRESS_DB_HOST`: Specifies the database host. Note the use of `db:3306`, where `db` is the service name of our MySQL container.
     - `WORDPRESS_DB_USER`, `WORDPRESS_DB_PASSWORD`, `WORDPRESS_DB_NAME`: These match the MySQL settings we defined in the `db` service.

3. **Volumes**:
   `db_data: {}`: This creates a named volume that Docker manages. It's used to persist the MySQL data.

To run this setup:

1. Save the above YAML in a file named `docker-compose.yml`.
2. In the same directory, run `docker compose up -d`.
3. Once the containers are running, you can access WordPress by navigating to `http://localhost:8000` in your web browser.

This setup provides a complete WordPress environment with a MySQL database, all configured and ready to use. The use of environment variables and volumes ensures that the setup is both flexible and persistent.

### Example 2: Flask App with Redis and Nginx

```yaml
version: '3.8'
services:
  flask:
    build: ./flask
    environment:
      - FLASK_ENV=development
    volumes:
      - ./flask:/code

  redis:
    image: "redis:alpine"

  nginx:
    image: "nginx:alpine"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf:ro
    ports:
      - "80:80"
    depends_on:
      - flask

networks:
  frontend:
  backend:

volumes:
  db-data:
```

Let's break this down:

1. **Version**: 
   As before, we're using version 3.8 of the Compose file format.

2. **Services**:
   We define three services: `flask`, `redis`, and `nginx`.

   a. **flask service**:
   - `build: ./flask`: This tells Docker to build an image using the Dockerfile in the `./flask` directory.
   - `environment`: Sets `FLASK_ENV=development`, which enables debug mode in Flask.
   - `volumes`: Mounts the local `./flask` directory to `/code` in the container. This is useful for development as it allows you to make changes to your code without rebuilding the container.

   b. **redis service**:
   - `image: "redis:alpine"`: Uses the official Redis image based on Alpine Linux, which is lightweight.

   c. **nginx service**:
   - `image: "nginx:alpine"`: Uses the official Nginx image based on Alpine Linux.
   - `volumes`: Mounts a local `nginx.conf` file to `/etc/nginx/nginx.conf` in the container. The `:ro` flag makes it read-only.
   - `ports`: Maps port 80 on the host to port 80 in the container.
   - `depends_on`: Ensures that the `flask` service is started before Nginx.

3. **Networks**:
   We define two networks: `frontend` and `backend`. This allows us to isolate our services. For example, we could put Nginx and Flask on the frontend network, and Flask and Redis on the backend network.

4. **Volumes**:
   `db-data`: This creates a named volume. Although it's not used in this configuration, it's available if we need persistent storage, perhaps for a database service we might add later.

To use this setup:

1. You need a Flask application in a directory named `flask`, with a Dockerfile to build it.
2. You need an `nginx.conf` file in the same directory as your `docker-compose.yml`.
3. Run `docker compose up -d` to start the services.

This configuration sets up a Flask application server, with Redis available for caching or as a message broker, and Nginx as a reverse proxy. The Flask code is mounted as a volume, allowing for easy development. Nginx handles incoming requests and forwards them to the Flask application.

The use of Alpine-based images for Redis and Nginx helps to keep the overall image size small, which is beneficial for deployment and scaling.

This setup is particularly useful for developing and testing a Flask application in an environment that closely mimics production, with a proper web server (Nginx) in front of the application server (Flask) and a caching/messaging system (Redis) available.

## Best Practices for Docker Compose

1. Use version control for your docker-compose.yml file.
2. Keep development, staging, and production environments as similar as possible.
3. Use build arguments and environment variables for flexibility.
4. Leverage healthchecks to ensure service dependencies are met.
5. Use `.env` files for environment-specific variables.
6. Optimize your images to keep them small and efficient.
7. Use docker-compose.override.yml for local development settings.

## Scaling Services

Docker Compose can scale services with a single command:

```bash
docker compose up -d --scale web=3
```

This command would start 3 instances of the `web` service.

## Networking in Docker Compose

By default, Compose sets up a single network for your app. Each container for a service joins the default network and is both reachable by other containers on that network, and discoverable by them at a hostname identical to the container name.

You can also specify custom networks:

```yaml
version: '3.8'
services:
  web:
    networks:
      - frontend
      - backend
  db:
    networks:
      - backend

networks:
  frontend:
  backend:
```

## Volumes in Docker Compose

Compose also lets you create named volumes that can be reused across multiple services:

```yaml
version: '3.8'
services:
  db:
    image: postgres
    volumes:
      - data:/var/lib/postgresql/data

volumes:
  data:
```

## Conclusion

Docker Compose simplifies the process of managing multi-container applications, making it an essential tool for developers working with Docker. By mastering Docker Compose, you can streamline your development workflow, ensure consistency across different environments, and easily manage complex applications with multiple interconnected services.

Remember to always use the latest `docker compose` command instead of the older `docker-compose`, as it's now integrated directly into Docker CLI and offers improved functionality and performance.
