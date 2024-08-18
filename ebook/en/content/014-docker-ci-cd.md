# Chapter 14: Docker in CI/CD Pipelines

Integrating Docker into Continuous Integration and Continuous Deployment (CI/CD) pipelines can significantly streamline the development, testing, and deployment processes. This chapter explores how to effectively use Docker in CI/CD workflows.

## 1. Docker in Continuous Integration

### Automated Building and Testing

Use Docker to create consistent environments for building and testing your application:

```yaml
# .gitlab-ci.yml example
build_and_test:
  image: docker:latest
  services:
    - docker:dind
  script:
    - docker build -t myapp:${CI_COMMIT_SHA} .
    - docker run myapp:${CI_COMMIT_SHA} npm test
```

### Parallel Testing

Leverage Docker to run tests in parallel:

```yaml
# GitHub Actions example
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

## 2. Docker in Continuous Deployment

### Pushing to Docker Registry

After successful tests, push your Docker image to a registry:

```yaml
# Jenkins pipeline example
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

### Deploying with Docker Swarm or Kubernetes

Use Docker Swarm or Kubernetes for orchestrating deployments:

```yaml
# Docker Swarm deployment in GitLab CI
deploy:
  stage: deploy
  script:
    - docker stack deploy -c docker-compose.yml myapp
```

For Kubernetes:

```yaml
# Kubernetes deployment in CircleCI
deployment:
  kubectl:
    command: |
      kubectl set image deployment/myapp myapp=myrepo/myapp:${CIRCLE_SHA1}
```

## 3. Docker Compose in CI/CD

Use Docker Compose to manage multi-container applications in your CI/CD pipeline:

```yaml
# Travis CI example
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

## 4. Security Scanning

Integrate security scanning into your pipeline:

```yaml
# GitLab CI with Trivy scanner
scan:
  image: aquasec/trivy:latest
  script:
    - trivy image myapp:${CI_COMMIT_SHA}
```

## 5. Performance Testing

Incorporate performance testing using Docker:

```yaml
# Jenkins pipeline with Apache JMeter
stage('Performance Tests') {
    steps {
        sh 'docker run -v ${WORKSPACE}:/jmeter apache/jmeter -n -t test-plan.jmx -l results.jtl'
        perfReport 'results.jtl'
    }
}
```

## 6. Environment-Specific Configurations

Use Docker's environment variables and build arguments for environment-specific configurations:

```dockerfile
ARG CONFIG_FILE=default.conf
COPY config/${CONFIG_FILE} /app/config.conf
```

In your CI/CD pipeline:

```yaml
build:
  script:
    - docker build --build-arg CONFIG_FILE=${ENV}.conf -t myapp:${CI_COMMIT_SHA} .
```

## 7. Caching in CI/CD

Optimize build times by caching Docker layers:

```yaml
# GitHub Actions example with caching
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

## 8. Blue-Green Deployments with Docker

Implement blue-green deployments using Docker:

```bash
# Script for blue-green deployment
#!/bin/bash
docker service update --image myrepo/myapp:${NEW_VERSION} myapp_blue
docker service scale myapp_blue=2 myapp_green=0
```

## 9. Monitoring and Logging in CI/CD

Integrate monitoring and logging solutions:

```yaml
# Docker Compose with ELK stack
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

## Conclusion

Integrating Docker into your CI/CD pipeline can greatly enhance your development and deployment processes. It provides consistency across environments, improves testing efficiency, and streamlines deployments. By leveraging Docker in your CI/CD workflows, you can achieve faster, more reliable software delivery.
