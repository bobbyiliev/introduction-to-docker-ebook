# Chương 8: Docker Compose

Docker Compose là một công cụ mạnh mẽ để định nghĩa và vận hành các ứng dụng Docker đa container (multi-container). Với Compose, bạn sử dụng một file YAML để cấu hình các services, networks và volumes cho ứng dụng của mình. Sau đó, chỉ với một câu lệnh duy nhất, bạn có thể tạo và khởi chạy tất cả các services từ cấu hình đó.

> **Lưu ý**: Docker Compose hiện đã được tích hợp vào Docker CLI. Câu lệnh mới là `docker compose` thay vì `docker-compose`. Chúng ta sẽ sử dụng câu lệnh mới này trong suốt chương này.

## Các lợi ích chính của Docker Compose

1. **Sự đơn giản (Simplicity)**: Định nghĩa toàn bộ cấu trúc ứng dụng (application stack) của bạn trong một file duy nhất.
2. **Tính tái lập (Reproducibility)**: Dễ dàng chia sẻ và quản lý phiên bản cấu hình ứng dụng.
3. **Khả năng mở rộng (Scalability)**: Các câu lệnh đơn giản để scale up hoặc scale down các services.
4. **Nhất quán môi trường (Environment Consistency)**: Đảm bảo các môi trường development, staging và production là giống hệt nhau.
5. **Cải thiện quy trình làm việc (Workflow Improvement)**: Compose có thể được sử dụng trong suốt chu kỳ phát triển từ kiểm thử (testing), staging cho đến production.

## File docker-compose.yml

File `docker-compose.yml` là thành phần cốt lõi của Docker Compose. Nó định nghĩa tất cả các thành phần và cấu hình của ứng dụng. Dưới đây là một ví dụ cơ bản:

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

Hãy cùng phân tích ví dụ này:

- `version`: Chỉ định phiên bản định dạng của file Compose.
- `services`: Định nghĩa các containers tạo nên ứng dụng của bạn.
- `web`: Một service dựa trên một image được build từ Dockerfile trong thư mục hiện tại.
- `redis`: Một service sử dụng image Redis công khai.

## Các khái niệm chính trong Docker Compose

1. **Services**: Các containers tạo nên ứng dụng của bạn.
2. **Networks**: Cách các services giao tiếp với nhau.
3. **Volumes**: Nơi các services lưu trữ và truy cập dữ liệu.

## Các lệnh Docker Compose cơ bản

- `docker compose up`: Tạo và khởi chạy các containers
  ```bash
  docker compose up -d  # Chạy ở chế độ detached mode
  ```

- `docker compose down`: Dừng và xóa các containers, networks, images và volumes
  ```bash
  docker compose down --volumes  # Xóa cả các volumes
  ```

- `docker compose ps`: Liệt kê các containers
- `docker compose logs`: Xem đầu ra (output) từ các containers
  ```bash
  docker compose logs -f web  # Theo dõi logs cho service web
  ```

## Các tính năng Docker Compose nâng cao

### 1. Biến môi trường (Environment Variables)

Bạn có thể sử dụng các file .env hoặc thiết lập chúng trực tiếp trong file compose:

```yaml
version: '3.8'
services:
  web:
    image: "webapp:${TAG}"
    environment:
      - DEBUG=1
```

### 2. Mở rộng Services (Extending Services)

Sử dụng `extends` để chia sẻ các cấu hình chung:

```yaml
version: '3.8'
services:
  web:
    extends:
      file: common-services.yml
      service: webapp
```

### 3. Kiểm tra sức khỏe (Healthchecks)

Đảm bảo các services đã sẵn sàng trước khi khởi chạy các services phụ thuộc:

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


## Ví dụ thực tế

### Ví dụ 1: WordPress với MySQL

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

Hãy cùng phân tích chi tiết:

1. **Version**:
   `version: '3.8'` chỉ định phiên bản của định dạng file Compose. Phiên bản 3.8 tương thích với Docker Engine 19.03.0+.

2. **Services**:
   Chúng ta định nghĩa hai services: `db` và `wordpress`.

   a. **db service**:
   - `image: mysql:5.7`: Sử dụng image MySQL 5.7 chính thức.
   - `volumes`: Tạo một named volume `db_data` và gắn kết nó vào `/var/lib/mysql` trong container. Điều này đảm bảo dữ liệu database vẫn tồn tại ngay cả khi container bị xóa.
   - `restart: always`: Đảm bảo container luôn khởi động lại nếu nó bị dừng.
   - `environment`: Thiết lập các biến môi trường MySQL:
     - `MYSQL_ROOT_PASSWORD`: Thiết lập mật khẩu root (root password) cho MySQL.
     - `MYSQL_DATABASE`: Tạo một database tên là "wordpress".
     - `MYSQL_USER` và `MYSQL_PASSWORD`: Tạo một người dùng mới với mật khẩu được chỉ định.

   b. **wordpress service**:
   - `depends_on`: Đảm bảo rằng service `db` được khởi chạy trước service `wordpress`.
   - `image: wordpress:latest`: Sử dụng image WordPress mới nhất chính thức.
   - `ports`: Ánh xạ cổng 8000 trên host tới cổng 80 trong container, nơi WordPress chạy.
   - `restart: always`: Đảm bảo container luôn khởi động lại nếu bị dừng.
   - `environment`: Thiết lập các biến môi trường WordPress:
     - `WORDPRESS_DB_HOST`: Chỉ định database host. Lưu ý việc sử dụng `db:3306`, trong đó `db` là tên service của container MySQL.
     - `WORDPRESS_DB_USER`, `WORDPRESS_DB_PASSWORD`, `WORDPRESS_DB_NAME`: Những giá trị này khớp với các thiết lập MySQL đã định nghĩa trong service `db`.

3. **Volumes**:
   `db_data: {}`: Lệnh này tạo ra một named volume do Docker quản lý. Nó được sử dụng để lưu trữ bền vững dữ liệu MySQL.

Để chạy thiết lập này:

1. Lưu nội dung YAML trên vào file tên là `docker-compose.yml`.
2. Trong cùng thư mục đó, chạy lệnh `docker compose up -d`.
3. Khi các containers đang chạy, bạn có thể truy cập WordPress bằng cách điều hướng tới `http://localhost:8000` trên trình duyệt web.

Thiết lập này cung cấp một môi trường WordPress hoàn chỉnh với database MySQL, tất cả đã được cấu hình và sẵn sàng sử dụng. Việc sử dụng các biến môi trường và volumes đảm bảo rằng thiết lập vừa linh hoạt vừa bền vững.

### Ví dụ 2: Ứng dụng Flask với Redis và Nginx

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

Hãy cùng phân tích:

1. **Version**: 
   Như trước, chúng ta sử dụng phiên bản 3.8 của định dạng file Compose.

2. **Services**:
   Chúng ta định nghĩa ba services: `flask`, `redis`, và `nginx`.

   a. **flask service**:
   - `build: ./flask`: Chỉ dẫn Docker build một image sử dụng Dockerfile trong thư mục `./flask`.
   - `environment`: Thiết lập `FLASK_ENV=development`, giúp kích hoạt chế độ debug (debug mode) trong Flask.
   - `volumes`: Gắn kết thư mục `./flask` ở máy cục bộ tới `/code` trong container. Điều này hữu ích cho việc phát triển vì nó cho phép bạn thay đổi code mà không cần rebuild container.

   b. **redis service**:
   - `image: "redis:alpine"`: Sử dụng image Redis chính thức dựa trên Alpine Linux, vốn rất nhẹ.

   c. **nginx service**:
   - `image: "nginx:alpine"`: Sử dụng image Nginx chính thức dựa trên Alpine Linux.
   - `volumes`: Gắn kết file `nginx.conf` cục bộ tới `/etc/nginx/nginx.conf` trong container. Cờ `:ro` giúp file này chỉ có quyền đọc (read-only).
   - `ports`: Ánh xạ cổng 80 trên host tới cổng 80 trong container.
   - `depends_on`: Đảm bảo service `flask` được khởi chạy trước Nginx.

3. **Networks**:
   Chúng ta định nghĩa hai networks: `frontend` và `backend`. Điều này cho phép chúng ta cô lập các services. Ví dụ, chúng ta có thể đặt Nginx và Flask vào network frontend, còn Flask và Redis vào network backend.

4. **Volumes**:
   `db-data`: Lệnh này tạo ra một named volume. Mặc dù nó không được dùng trong cấu hình này, nó luôn sẵn sàng nếu chúng ta cần lưu trữ bền vững, chẳng hạn cho một service database mà chúng ta có thể thêm vào sau này.

Để sử dụng thiết lập này:

1. Bạn cần một ứng dụng Flask trong thư mục tên là `flask`, với một Dockerfile để build nó.
2. Bạn cần một file `nginx.conf` trong cùng thư mục với file `docker-compose.yml`.
3. Chạy `docker compose up -d` để khởi chạy các services.

Cấu hình này thiết lập một server ứng dụng Flask, với Redis có sẵn để làm cache hoặc message broker, và Nginx đóng vai trò reverse proxy. Code của Flask được gắn kết dưới dạng volume, thuận tiện cho việc phát triển. Nginx xử lý các yêu cầu (requests) đến và chuyển tiếp (forwards) chúng tới ứng dụng Flask.

Việc sử dụng các images Alpine-based cho Redis và Nginx giúp giữ cho kích thước tổng thể của image nhỏ gọn, điều này rất có lợi cho việc triển khai (deployment) và mở rộng (scaling).

Thiết lập này đặc biệt hữu ích cho việc phát triển và kiểm thử một ứng dụng Flask trong môi trường mô phỏng gần giống với production, với một web server thực thụ (Nginx) đứng trước server ứng dụng (Flask) và một hệ thống caching/messaging (Redis) có sẵn.

## Các quy tắc tốt nhất cho Docker Compose (Best Practices)

1. Sử dụng quản lý phiên bản (version control) cho file docker-compose.yml của bạn.
2. Giữ cho các môi trường development, staging và production giống nhau nhất có thể.
3. Sử dụng build arguments và các biến môi trường để tăng tính linh hoạt.
4. Tận dụng healthchecks để đảm bảo các phụ thuộc của service được đáp ứng.
5. Sử dụng các file `.env` cho các biến đặc thù theo từng môi trường.
6. Tối ưu hóa các images để giữ chúng nhỏ gọn và hiệu quả.
7. Sử dụng docker-compose.override.yml cho các thiết lập phát triển tại máy cục bộ.

## Mở rộng các Services (Scaling Services)

Docker Compose có thể scale các services chỉ với một câu lệnh duy nhất:

```bash
docker compose up -d --scale web=3
```

Câu lệnh này sẽ khởi chạy 3 thực thể (instances) của service `web`.

## Networking trong Docker Compose

Theo mặc định, Compose thiết lập một network duy nhất cho ứng dụng của bạn. Mỗi container cho một service sẽ gia nhập vào network mặc định đó, chúng có thể liên lạc được với các containers khác trên network đó, và có thể được tìm thấy bởi các container khác thông qua một hostname trùng với tên container.

Bạn cũng có thể chỉ định các networks tùy chỉnh:

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

## Volumes trong Docker Compose

Compose cũng cho phép bạn tạo các named volumes có thể được tái sử dụng giữa nhiều services:

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

## Kết luận

Docker Compose đơn giản hóa quá trình quản lý các ứng dụng đa container (multi-container), trở thành một công cụ thiết yếu cho các lập trình viên làm việc với Docker. Bằng cách làm chủ Docker Compose, bạn có thể tối ưu hóa quy trình phát triển, đảm bảo tính nhất quán trên các môi trường khác nhau và dễ dàng quản lý các ứng dụng phức tạp với nhiều services kết nối với nhau.

Hãy nhớ luôn sử dụng câu lệnh `docker compose` mới nhất thay vì `docker-compose` cũ, vì hiện tại nó đã được tích hợp trực tiếp vào Docker CLI và mang lại hiệu năng cũng như chức năng cải tiến hơn.
