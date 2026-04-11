# Chương 15: Docker và Kiến trúc Microservices (Microservices Architecture)

Kiến trúc microservices là một cách tiếp cận để phát triển một ứng dụng dưới dạng một bộ các dịch vụ nhỏ, mỗi dịch vụ chạy trong process riêng của nó và giao tiếp bằng các cơ chế nhẹ (lightweight mechanisms). Công nghệ container hóa (containerization) của Docker là một sự lựa chọn tuyệt vời cho microservices, mang lại khả năng cô lập (isolation), tính di động (portability) và khả năng mở rộng (scalability).

## 1. Các nguyên tắc của Microservices

- Nguyên tắc trách nhiệm duy nhất (Single Responsibility Principle)
- Quản lý dữ liệu phi tập trung (Decentralized Data Management)
- Cô lập lỗi (Failure Isolation)
- Khả năng mở rộng (Scalability)
- Đa dạng về công nghệ (Technology Diversity)

## 2. Docker hóa Microservices (Dockerizing Microservices)

### Mẫu Dockerfile cho Microservice

```dockerfile
FROM node:14-alpine
WORKDIR /usr/src/app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["node", "server.js"]
```

### Build và Run

```bash
docker build -t my-microservice .
docker run -d -p 3000:3000 my-microservice
```

## 3. Giao tiếp giữa các dịch vụ (Inter-service Communication)

### REST API

```javascript
// Ví dụ Express.js
const express = require('express');
const app = express();

app.get('/api/data', (req, res) => {
  res.json({ message: 'Data from Microservice A' });
});

app.listen(3000, () => console.log('Microservice A listening on port 3000'));
```

### Hàng đợi tin nhắn (Message Queues)

Sử dụng RabbitMQ:

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

## 4. Khám phá dịch vụ (Service Discovery)

Sử dụng Consul:

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

Sử dụng NGINX làm một API Gateway:

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

## 6. Quản lý dữ liệu (Data Management)

### Cơ sở dữ liệu riêng cho mỗi dịch vụ (Database per Service)

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

## 7. Giám sát Microservices (Monitoring Microservices)

Sử dụng Prometheus và Grafana:

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

## 8. Mở rộng Microservices (Scaling Microservices)

Sử dụng Docker Swarm:

```bash
# Khởi tạo swarm
docker swarm init

# Triển khai stack
docker stack deploy -c docker-compose.yml myapp

# Mở rộng một service
docker service scale myapp_service-a=3
```

## 9. Kiểm thử Microservices (Testing Microservices)

### Kiểm thử đơn vị (Unit Testing)

```javascript
// Ví dụ Jest
test('API returns correct data', async () => {
  const response = await request(app).get('/api/data');
  expect(response.statusCode).toBe(200);
  expect(response.body).toHaveProperty('message');
});
```

### Kiểm thử tích hợp (Integration Testing)

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

## 10. Chiến lược Triển khai (Deployment Strategies)

### Triển khai Blue-Green (Blue-Green Deployment)

```bash
# Triển khai phiên bản mới (green)
docker service create --name myapp-green --replicas 2 myrepo/myapp:v2

# Chuyển traffic sang green
docker service update --network-add proxy-network myapp-green
docker service update --network-rm proxy-network myapp-blue

# Gỡ bỏ phiên bản cũ (blue)
docker service rm myapp-blue
```

## Kết luận

Docker cung cấp một nền tảng tuyệt vời để phát triển, triển khai và quản lý các microservices. Nó mang lại khả năng cô lập (isolation), tính di động (portability) và khả năng mở rộng (scalability) cần thiết mà kiến trúc microservices đòi hỏi. Bằng cách tận dụng các tính năng của Docker cùng với các công cụ và dịch vụ bổ trợ, bạn có thể xây dựng các ứng dụng dựa trên microservices mạnh mẽ, có khả năng mở rộng và dễ bảo trì.
