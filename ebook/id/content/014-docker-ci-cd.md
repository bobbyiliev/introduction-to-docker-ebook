# Bab 14: Docker dalam Pipeline CI/CD

Mengintegrasikan Docker ke dalam pipeline Continuous Integration dan Continuous Deployment (CI/CD) dapat secara signifikan memperlancar proses pengembangan, pengujian, dan deployment. Bab ini membahas cara efektif menggunakan Docker dalam workflow CI/CD.

## 1. Docker dalam Continuous Integration

### Build dan Testing Otomatis

Gunakan Docker untuk membuat lingkungan yang konsisten dalam membangun dan menguji aplikasi Anda:

```yaml
# Contoh .gitlab-ci.yml
build_and_test:
  image: docker:latest
  services:
    - docker:dind
  script:
    - docker build -t myapp:${CI_COMMIT_SHA} .
    - docker run myapp:${CI_COMMIT_SHA} npm test
```

### Pengujian Paralel

Manfaatkan Docker untuk menjalankan pengujian secara paralel:

```yaml
# Contoh GitHub Actions
jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node-version: [12.x, 14.x, 16.x]
    steps:
    - uses: actions/checkout@v2
    - name: Test dengan Node.js ${{ matrix.node-version }}
      run: |
        docker build -t myapp:${{ matrix.node-version }} --build-arg NODE_VERSION=${{ matrix.node-version }} .
        docker run myapp:${{ matrix.node-version }} npm test
```

## 2. Docker dalam Continuous Deployment

### Push ke Docker Registry

Setelah pengujian berhasil, push image Docker Anda ke registry:

```yaml
# Contoh pipeline Jenkins
pipeline {
    agent any
    stages {
        stage('Build and Push') {
            steps {
                script {
                    docker.withRegistry('https://registry.example.com', 'credentials-id') {
                        def customImage = docker.build("my-image:${env.BUILD_ID}")
                        customImage.push()
                    }
                }
            }
        }
    }
}
```

### Deploy dengan Docker Swarm atau Kubernetes

Gunakan Docker Swarm atau Kubernetes untuk orkestrasi deployment:

```yaml
# Deploy Docker Swarm di GitLab CI
deploy:
  stage: deploy
  script:
    - docker stack deploy -c docker-compose.yml myapp
```

Untuk Kubernetes:

```yaml
# Deploy Kubernetes di CircleCI
deployment:
  kubectl:
    command: |
      kubectl set image deployment/myapp myapp=myrepo/myapp:${CIRCLE_SHA1}
```

## 3. Docker Compose dalam CI/CD

Gunakan Docker Compose untuk mengelola aplikasi multi-container di pipeline CI/CD Anda:

```yaml
# Contoh Travis CI
services:
  - docker

before_install:
  - docker-compose up -d
  - docker-compose exec -T app npm install

script:
  - docker-compose exec -T app npm test

after_success:
  - docker-compose down
```

## 4. Pemindaian Keamanan

Integrasikan pemindaian keamanan ke pipeline Anda:

```yaml
# GitLab CI dengan Trivy scanner
scan:
  image: aquasec/trivy:latest
  script:
    - trivy image myapp:${CI_COMMIT_SHA}
```

## 5. Pengujian Performa

Masukkan pengujian performa menggunakan Docker:

```yaml
# Pipeline Jenkins dengan Apache JMeter
stage('Performance Tests') {
    steps {
        sh 'docker run -v ${WORKSPACE}:/jmeter apache/jmeter -n -t test-plan.jmx -l results.jtl'
        perfReport 'results.jtl'
    }
}
```

## 6. Konfigurasi Spesifik Lingkungan

Gunakan environment variable dan build argument Docker untuk konfigurasi spesifik lingkungan:

```dockerfile
ARG CONFIG_FILE=default.conf
COPY config/${CONFIG_FILE} /app/config.conf
```

Di pipeline CI/CD Anda:

```yaml
build:
  script:
    - docker build --build-arg CONFIG_FILE=${ENV}.conf -t myapp:${CI_COMMIT_SHA} .
```

## 7. Caching di CI/CD

Optimalkan waktu build dengan caching layer Docker:

```yaml
# Contoh GitHub Actions dengan caching
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - name: Cache Docker layers
      uses: actions/cache@v2
      with:
        path: /tmp/.buildx-cache
        key: ${{ runner.os }}-buildx-${{ github.sha }}
        restore-keys: |
          ${{ runner.os }}-buildx-
    - name: Build and push
      uses: docker/build-push-action@v2
      with:
        push: true
        tags: user/app:latest
        cache-from: type=local,src=/tmp/.buildx-cache
        cache-to: type=local,dest=/tmp/.buildx-cache
```

## 8. Blue-Green Deployment dengan Docker

Implementasikan blue-green deployment menggunakan Docker:

```bash
# Script untuk blue-green deployment
#!/bin/bash
docker service update --image myrepo/myapp:${NEW_VERSION} myapp_blue
docker service scale myapp_blue=2 myapp_green=0
```

## 9. Monitoring dan Logging di CI/CD

Integrasikan solusi monitoring dan logging:

```yaml
# Docker Compose dengan ELK stack
version: '3'
services:
  app:
    image: myapp:latest
    logging:
      driver: "json-file"
      options:
        max-size: "200k"
        max-file: "10"
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.10.0
  logstash:
    image: docker.elastic.co/logstash/logstash:7.10.0
  kibana:
    image: docker.elastic.co/kibana/kibana:7.10.0
```

## Kesimpulan

Mengintegrasikan Docker ke pipeline CI/CD Anda dapat sangat meningkatkan proses pengembangan dan deployment. Docker memberikan konsistensi lintas lingkungan, meningkatkan efisiensi pengujian, dan memperlancar deployment. Dengan memanfaatkan Docker dalam workflow CI/CD, Anda dapat mencapai pengiriman perangkat lunak yang lebih cepat dan andal.
