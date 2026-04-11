# Chương 14: Docker trong các đường ống CI/CD (CI/CD Pipelines)

Việc tích hợp Docker vào các đường ống Tích hợp liên tục và Triển khai liên tục (CI/CD Pipelines) có thể tối ưu hóa đáng kể quy trình phát triển, kiểm thử và triển khai. Chương này khám phá cách sử dụng Docker hiệu quả trong các quy trình làm việc (workflows) CI/CD.

## 1. Docker trong Tích hợp liên tục (Continuous Integration)

### Tự động hóa việc Build và Test

Sử dụng Docker để tạo ra các môi trường nhất quán cho việc build và test ứng dụng của bạn:

```yaml
# Ví dụ .gitlab-ci.yml
build_and_test:
  image: docker:latest
  services:
    - docker:dind
  script:
    - docker build -t myapp:${CI_COMMIT_SHA} .
    - docker run myapp:${CI_COMMIT_SHA} npm test
```

### Kiểm thử song song (Parallel Testing)

Tận dụng Docker để chạy các bài test song song:

```yaml
# Ví dụ GitHub Actions
jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node-version: [12.x, 14.x, 16.x]
    steps:
    - uses: actions/checkout@v2
    - name: Test with Node.js ${{ matrix.node-version }}
      run: |
        docker build -t myapp:${{ matrix.node-version }} --build-arg NODE_VERSION=${{ matrix.node-version }} .
        docker run myapp:${{ matrix.node-version }} npm test
```

## 2. Docker trong Triển khai liên tục (Continuous Deployment)

### Push lên Docker Registry

Sau khi các bài test thành công, hãy push Docker image của bạn lên một registry:

```yaml
# Ví dụ Jenkins pipeline
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

### Triển khai với Docker Swarm hoặc Kubernetes

Sử dụng Docker Swarm hoặc Kubernetes để điều phối (orchestrating) việc triển khai:

```yaml
# Triển khai Docker Swarm trong GitLab CI
deploy:
  stage: deploy
  script:
    - docker stack deploy -c docker-compose.yml myapp
```

Đối với Kubernetes:

```yaml
# Triển khai Kubernetes trong CircleCI
deployment:
  kubectl:
    command: |
      kubectl set image deployment/myapp myapp=myrepo/myapp:${CIRCLE_SHA1}
```

## 3. Docker Compose trong CI/CD

Sử dụng Docker Compose để quản lý các ứng dụng đa container (multi-container) trong đường ống CI/CD của bạn:

```yaml
# Ví dụ Travis CI
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

## 4. Quét bảo mật (Security Scanning)

Tích hợp việc quét bảo mật vào đường ống của bạn:

```yaml
# GitLab CI với công cụ quét Trivy
scan:
  image: aquasec/trivy:latest
  script:
    - trivy image myapp:${CI_COMMIT_SHA}
```

## 5. Kiểm thử hiệu năng (Performance Testing)

Kết hợp việc kiểm thử hiệu năng sử dụng Docker:

```yaml
# Jenkins pipeline với Apache JMeter
stage('Performance Tests') {
    steps {
        sh 'docker run -v ${WORKSPACE}:/jmeter apache/jmeter -n -t test-plan.jmx -l results.jtl'
        perfReport 'results.jtl'
    }
}
```

## 6. Các cấu hình đặc thù theo môi trường (Environment-Specific Configurations)

Sử dụng các biến môi trường (environment variables) và các tham số build (build arguments) của Docker cho các cấu hình đặc thù theo từng môi trường:

```dockerfile
ARG CONFIG_FILE=default.conf
COPY config/${CONFIG_FILE} /app/config.conf
```

Trong đường ống CI/CD của bạn:

```yaml
build:
  script:
    - docker build --build-arg CONFIG_FILE=${ENV}.conf -t myapp:${CI_COMMIT_SHA} .
```

## 7. Caching trong CI/CD

Tối ưu hóa thời gian build bằng cách cache các lớp (layers) của Docker:

```yaml
# Ví dụ GitHub Actions với cơ chế caching
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

## 8. Triển khai Blue-Green (Blue-Green Deployments) với Docker

Thực hiện triển khai Blue-Green sử dụng Docker:

```bash
# Script cho triển khai blue-green
#!/bin/bash
docker service update --image myrepo/myapp:${NEW_VERSION} myapp_blue
docker service scale myapp_blue=2 myapp_green=0
```

## 9. Giám sát và Ghi nhật ký (Monitoring and Logging) trong CI/CD

Tích hợp các giải pháp giám sát và ghi nhật ký:

```yaml
# Docker Compose với ELK stack
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

## Kết luận

Việc tích hợp Docker vào đường ống CI/CD có thể cải thiện đáng kể quy trình phát triển và triển khai của bạn. Nó mang lại sự nhất quán giữa các môi trường, nâng cao hiệu quả kiểm thử và tối ưu hóa việc triển khai. Bằng cách tận dụng Docker trong các quy trình CI/CD, bạn có thể đạt được tốc độ phân phối phần mềm nhanh hơn và đáng tin cậy hơn.
