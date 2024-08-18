# Chapter 15: Docker and Microservices Architecture

Microservices architecture is an approach to developing a single application as a suite of small services, each running in its own process and communicating with lightweight mechanisms. Docker's containerization technology is an excellent fit for microservices, providing isolation, portability, and scalability.

## 1. Principles of Microservices

- Single Responsibility Principle
- Decentralized Data Management
- Failure Isolation
- Scalability
- Technology Diversity

## 2. Dockerizing Microservices

### Sample Microservice Dockerfile

```dockerfile
FROM node:14-alpine
WORKDIR /usr/src/app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["node", "server.js"]
```

### Building and Running

```bash
docker build -t my-microservice .
docker run -d -p 3000:3000 my-microservice
```

## 3. Inter-service Communication

### REST API

```javascript
// Express.js example
const express = require('express');
const app = express();

app.get('/api/data', (req, res) => {
  res.json({ message: 'Data from Microservice A' });
});

app.listen(3000, () => console.log('Microservice A listening on port 3000'));
```

### Message Queues

Using RabbitMQ:

```dockerfile
# Dockerfile
FROM node:14-alpine
RUN npm install amqplib
COPY . .
CMD ["node", "consumer.js"]
```

```javascript
// consumer.js
const amqp = require('amqplib');

async function consume() {
  const connection = await amqp.connect('amqp://rabbitmq');
  const channel = await connection.createChannel();
  await channel.assertQueue('task_queue');
  
  channel.consume('task_queue', (msg) => {
    console.log("Received:", msg.content.toString());
    channel.ack(msg);
  });
}

consume();
```

## 4. Service Discovery

Using Consul:

```yaml
version: '3'
services:
  consul:
    image: consul:latest
    ports:
      - "8500:8500"
  
  service-a:
    build: ./service-a
    environment:
      - CONSUL_HTTP_ADDR=consul:8500

  service-b:
    build: ./service-b
    environment:
      - CONSUL_HTTP_ADDR=consul:8500
```

## 5. API Gateway

Using NGINX as an API Gateway:

```nginx
http {
    upstream service_a {
        server service-a:3000;
    }
    upstream service_b {
        server service-b:3000;
    }

    server {
        listen 80;

        location /api/service-a {
            proxy_pass http://service_a;
        }

        location /api/service-b {
            proxy_pass http://service_b;
        }
    }
}
```

## 6. Data Management

### Database per Service

```yaml
version: '3'
services:
  service-a:
    build: ./service-a
    depends_on:
      - db-a

  db-a:
    image: postgres:13
    environment:
      POSTGRES_DB: service_a_db
      POSTGRES_PASSWORD: password

  service-b:
    build: ./service-b
    depends_on:
      - db-b

  db-b:
    image: mysql:8
    environment:
      MYSQL_DATABASE: service_b_db
      MYSQL_ROOT_PASSWORD: password
```

## 7. Monitoring Microservices

Using Prometheus and Grafana:

```yaml
version: '3'
services:
  prometheus:
    image: prom/prometheus
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
    ports:
      - "9090:9090"

  grafana:
    image: grafana/grafana
    ports:
      - "3000:3000"
    depends_on:
      - prometheus
```

## 8. Scaling Microservices

Using Docker Swarm:

```bash
# Initialize swarm
docker swarm init

# Deploy stack
docker stack deploy -c docker-compose.yml myapp

# Scale a service
docker service scale myapp_service-a=3
```

## 9. Testing Microservices

### Unit Testing

```javascript
// Jest example
test('API returns correct data', async () => {
  const response = await request(app).get('/api/data');
  expect(response.statusCode).toBe(200);
  expect(response.body).toHaveProperty('message');
});
```

### Integration Testing

```yaml
version: '3'
services:
  app:
    build: .
    depends_on:
      - test-db
  
  test-db:
    image: postgres:13
    environment:
      POSTGRES_DB: test_db
      POSTGRES_PASSWORD: test_password

  test:
    build:
      context: .
      dockerfile: Dockerfile.test
    depends_on:
      - app
      - test-db
    command: ["npm", "run", "test"]
```

## 10. Deployment Strategies

### Blue-Green Deployment

```bash
# Deploy new version (green)
docker service create --name myapp-green --replicas 2 myrepo/myapp:v2

# Switch traffic to green
docker service update --network-add proxy-network myapp-green
docker service update --network-rm proxy-network myapp-blue

# Remove old version (blue)
docker service rm myapp-blue
```

## Conclusion

Docker provides an excellent platform for developing, deploying, and managing microservices. It offers the necessary isolation, portability, and scalability that microservices architecture demands. By leveraging Docker's features along with complementary tools and services, you can build robust, scalable, and maintainable microservices-based applications.
