# Bab 15: Docker dan Arsitektur Microservices

Arsitektur microservices adalah pendekatan untuk mengembangkan satu aplikasi sebagai kumpulan layanan kecil, masing-masing berjalan dalam prosesnya sendiri dan berkomunikasi dengan mekanisme ringan. Teknologi containerisasi Docker sangat cocok untuk microservices, memberikan isolasi, portabilitas, dan skalabilitas.

## 1. Prinsip Microservices

- Prinsip Tanggung Jawab Tunggal
- Manajemen Data Terdesentralisasi
- Isolasi Kegagalan
- Skalabilitas
- Keragaman Teknologi

## 2. Dockerisasi Microservices

### Contoh Dockerfile Microservice

```dockerfile
FROM node:14-alpine
WORKDIR /usr/src/app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["node", "server.js"]
```

### Build dan Menjalankan

```bash
docker build -t my-microservice .
docker run -d -p 3000:3000 my-microservice
```

## 3. Komunikasi Antar Layanan

### REST API

```javascript
// Contoh Express.js
const express = require('express');
const app = express();

app.get('/api/data', (req, res) => {
  res.json({ message: 'Data dari Microservice A' });
});

app.listen(3000, () => console.log('Microservice A berjalan di port 3000'));
```

### Message Queue

Menggunakan RabbitMQ:

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
    console.log("Diterima:", msg.content.toString());
    channel.ack(msg);
  });
}

consume();
```

## 4. Service Discovery

Menggunakan Consul:

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

Menggunakan NGINX sebagai API Gateway:

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

## 6. Manajemen Data

### Database per Layanan

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

Menggunakan Prometheus dan Grafana:

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

## 8. Skalabilitas Microservices

Menggunakan Docker Swarm:

```bash
# Inisialisasi swarm
docker swarm init

# Deploy stack
docker stack deploy -c docker-compose.yml myapp

# Skala sebuah service
docker service scale myapp_service-a=3
```

## 9. Pengujian Microservices

### Unit Testing

```javascript
// Contoh Jest
test('API mengembalikan data yang benar', async () => {
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

## 10. Strategi Deployment

### Blue-Green Deployment

```bash
# Deploy versi baru (green)
docker service create --name myapp-green --replicas 2 myrepo/myapp:v2

# Alihkan trafik ke green
docker service update --network-add proxy-network myapp-green
docker service update --network-rm proxy-network myapp-blue

# Hapus versi lama (blue)
docker service rm myapp-blue
```

## Kesimpulan

Docker menyediakan platform yang sangat baik untuk mengembangkan, mendistribusikan, dan mengelola microservices. Docker menawarkan isolasi, portabilitas, dan skalabilitas yang dibutuhkan arsitektur microservices. Dengan memanfaatkan fitur Docker dan alat pendukung lainnya, Anda dapat membangun aplikasi berbasis microservices yang robust, skalabel, dan mudah dipelihara.
