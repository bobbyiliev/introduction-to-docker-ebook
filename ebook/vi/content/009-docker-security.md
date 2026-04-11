# Chương 9: Các quy tắc bảo mật Docker tốt nhất (Security Best Practices)

Bảo mật là một khía cạnh cực kỳ quan trọng khi làm việc với Docker, đặc biệt là trong các môi trường production (production environments). Chương này sẽ đề cập đến các quy tắc bảo mật thiết yếu để giúp bạn xây dựng và duy trì các môi trường Docker an toàn.

## 1. Duy trì cập nhật Docker

Luôn luôn sử dụng phiên bản mới nhất của Docker để được hưởng lợi từ các bản vá bảo mật (security patches) gần đây nhất.

```bash
sudo apt-get update
sudo apt-get upgrade docker-ce
```

## 2. Sử dụng các Official Images

Bất cứ khi nào có thể, hãy sử dụng các official images từ Docker Hub hoặc các nguồn tin cậy. Các images này được cập nhật thường xuyên và được quét (scanned) để tìm các lỗ hổng (vulnerabilities).

```yaml
version: '3.8'
services:
  web:
    image: nginx:latest  # Official Nginx image
```

## 3. Quét Images để tìm lỗ hổng (Scan Images for Vulnerabilities)

Sử dụng các công cụ như Docker Scout hoặc Trivy để quét các images của bạn tìm các lỗ hổng đã biết.

```bash
docker scout cve <image_name>
```

## 4. Giới hạn tài nguyên Container (Limit Container Resources)

Ngăn chặn các cuộc tấn công từ chối dịch vụ (Denial of Service) bằng cách giới hạn tài nguyên của container:

```yaml
version: '3.8'
services:
  web:
    image: nginx:latest
    deploy:
      resources:
        limits:
          cpus: '0.50'
          memory: 50M
```

## 5. Sử dụng người dùng Non-Root (Use Non-Root Users)

Chạy các containers bằng người dùng non-root để hạn chế tác động tiềm tàng nếu container bị xâm nhập:

```dockerfile
FROM node:14
RUN groupadd -r myapp && useradd -r -g myapp myuser
USER myuser
```

## 6. Sử dụng Quản lý Bí mật (Secret Management)

Đối với các dữ liệu nhạy cảm như mật khẩu và API keys, hãy sử dụng Docker secrets:

```bash
echo "mysecretpassword" | docker secret create db_password -
```

Sau đó trong file docker-compose.yml của bạn:

```yaml
version: '3.8'
services:
  db:
    image: mysql
    secrets:
      - db_password
secrets:
  db_password:
    external: true
```

## 7. Kích hoạt Content Trust

Ký (Sign) và xác thực (verify) các image tags:

```bash
export DOCKER_CONTENT_TRUST=1
docker push myrepo/myimage:latest
```

## 8. Sử dụng các Container Chỉ đọc (Read-Only Containers)

Khi có thể, hãy chạy các containers ở chế độ chỉ đọc (read-only mode):

```yaml
version: '3.8'
services:
  web:
    image: nginx
    read_only: true
    tmpfs:
      - /tmp
      - /var/cache/nginx
```

## 9. Thực hiện Phân đoạn Mạng (Network Segmentation)

Sử dụng Docker networks để cô lập (isolate) các containers:

```yaml
version: '3.8'
services:
  frontend:
    networks:
      - frontend
  backend:
    networks:
      - backend
networks:
  frontend:
  backend:
```

## 10. Kiểm tra Bảo mật định kỳ (Regular Security Audits)

Thường xuyên kiểm tra môi trường Docker của bạn bằng các công cụ như Docker Bench for Security:

```bash
docker run -it --net host --pid host --userns host --cap-add audit_control \
    -e DOCKER_CONTENT_TRUST=$DOCKER_CONTENT_TRUST \
    -v /var/lib:/var/lib \
    -v /var/run/docker.sock:/var/run/docker.sock \
    -v /usr/lib/systemd:/usr/lib/systemd \
    -v /etc:/etc --label docker_bench_security \
    docker/docker-bench-security
```

## 11. Sử dụng Security-Enhanced Linux (SELinux) hoặc AppArmor

Những công cụ này cung cấp thêm một lớp bảo mật bổ sung. Hãy đảm bảo chúng được kích hoạt và cấu hình đúng cách trên host system của bạn.

## 12. Thực hiện Ghi nhật ký và Giám sát (Logging and Monitoring)

Sử dụng khả năng ghi nhật ký (logging) của Docker và cân nhắc tích hợp với các công cụ giám sát (monitoring) bên ngoài:

```yaml
version: '3.8'
services:
  web:
    image: nginx
    logging:
      driver: "json-file"
      options:
        max-size: "200k"
        max-file: "10"
```

## Kết luận

Việc áp dụng các quy tắc bảo mật tốt nhất này sẽ cải thiện đáng kể vị thế bảo mật cho các môi trường Docker của bạn. Hãy nhớ rằng, bảo mật là một quy trình liên tục, và điều quan trọng là phải luôn cập nhật thông tin về các mối đe dọa bảo mật mới nhất cũng như các tính năng bảo mật của Docker.
