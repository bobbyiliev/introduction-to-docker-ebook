# Chương 1: Giới thiệu về Docker

## Docker là gì?

Docker là một nền tảng open-source giúp tự động hóa việc deployment, scaling, và quản lý các ứng dụng bằng cách sử dụng công nghệ containerization. Nó cho phép các developer đóng gói các ứng dụng và các dependencies của chúng vào các đơn vị tiêu chuẩn hóa được gọi là containers, có thể chạy nhất quán trên các môi trường khác nhau.

### Các khái niệm chính:

1. **Containerization**: Một dạng virtualization nhẹ giúp đóng gói các ứng dụng và các dependencies của chúng lại với nhau.
2. **Docker Engine**: Runtime cho phép bạn build và chạy các containers.
3. **Docker Image**: Một template chỉ đọc (read-only) được sử dụng để tạo các containers.
4. **Docker Container**: Một thực thể có thể chạy được (runnable instance) của một Docker image.
5. **Docker Hub**: Một registry dựa trên cloud để lưu trữ và chia sẻ các Docker images.

## Tại sao nên sử dụng Docker?

Docker mang lại nhiều lợi ích cho các developer và các đội ngũ operations:

1. **Nhất quán (Consistency)**: Đảm bảo các ứng dụng chạy giống hệt nhau trong các môi trường development, testing, và production.
2. **Cô lập (Isolation)**: Các containers được cô lập với nhau và với host system, giúp cải thiện security và giảm thiểu xung đột.
3. **Linh hoạt (Portability)**: Các containers có thể chạy trên bất kỳ hệ thống nào hỗ trợ Docker, bất kể cơ sở hạ tầng bên dưới là gì.
4. **Hiệu quả (Efficiency)**: Các containers chia sẻ OS kernel của host system, giúp chúng nhẹ hơn so với các virtual machines truyền thống.
5. **Khả năng mở rộng (Scalability)**: Dễ dàng scale các ứng dụng theo chiều ngang (horizontally) bằng cách chạy nhiều containers.
6. **Quản lý phiên bản (Version Control)**: Các Docker images có thể được đánh phiên bản, cho phép dễ dàng rollbacks và cập nhật.

## Kiến trúc của Docker (Docker Architecture)

Docker sử dụng kiến trúc client-server:

1. **Docker Client**: Cách chính mà người dùng tương tác với Docker thông qua command line interface (CLI).
2. **Docker Host**: Máy đang chạy Docker daemon (dockerd).
3. **Docker Daemon**: Quản lý các Docker objects như images, containers, networks, và volumes.
4. **Docker Registry**: Lưu trữ các Docker images (ví dụ: Docker Hub).

Dưới đây là sơ đồ đơn giản hóa về Docker architecture:

```
┌─────────────┐     ┌─────────────────────────────────────┐
│ Docker CLI  │     │            Docker Host              │
│ (docker)    │◄───►│  ┌────────────┐      ┌───────────┐  │
└─────────────┘     │  │   Docker   │      │ Containers│  │
                    │  │   Daemon   │◄────►│    and    │  │
                    │  │  (dockerd) │      │  Images   │  │
                    │  └────────────┘      └───────────┘  │
                    └─────────────────────────────────────┘
                                ▲
                                │
                                ▼
                    ┌─────────────────────┐
                    │   Docker Registry   │
                    │    (Docker Hub)     │
                    └─────────────────────┘
```

## Containers và Virtual Machines

Mặc dù cả containers và virtual machines (VMs) đều được sử dụng để cô lập ứng dụng, chúng khác nhau ở một số khía cạnh chính:

| Khía cạnh (Aspect) | Containers                            | Virtual Machines (VMs)              |
|--------------------|---------------------------------------|-------------------------------------|
| OS                 | Chia sẻ host OS kernel                | Chạy full OS và kernel              |
| Sử dụng tài nguyên | Nhẹ, overhead tối thiểu               | Sử dụng tài nguyên cao hơn          |
| Thời gian Boot     | Giây                                  | Phút                                |
| Cô lập (Isolation) | Cô lập ở mức Process (Process-level)  | Cô lập hoàn toàn                    |
| Linh hoạt          | Linh hoạt cao trên các OS khác nhau   | Ít linh hoạt hơn, phụ thuộc vào OS  |
| Hiệu suất          | Hiệu suất gần như native              | Hiệu suất bị ảnh hưởng nhẹ          |
| Lưu trữ            | Thường nhỏ hơn (MBs)                  | Lớn hơn (GBs)                       |

## Quy trình làm việc cơ bản của Docker (Basic Docker Workflow)

1. **Build**: Tạo một Dockerfile để định nghĩa ứng dụng và các dependencies của nó.
2. **Ship**: Đưa Docker image của bạn lên một registry như Docker Hub.
3. **Run**: Kéo (Pull) image về và chạy nó như một container trên bất kỳ host nào đã cài đặt Docker.

Dưới đây là một ví dụ đơn giản về quy trình này:

```bash
# Build một image
docker build -t myapp:v1 .

# Ship image lên Docker Hub
docker push username/myapp:v1

# Run container
docker run -d -p 8080:80 username/myapp:v1
```

## Các thành phần của Docker (Docker Components)

1. **Dockerfile**: Một file văn bản chứa các chỉ dẫn để build một Docker image.
2. **Docker Compose**: Một công cụ để định nghĩa và chạy các ứng dụng Docker multi-container.
3. **Docker Swarm**: Giải pháp native clustering và orchestration của Docker.
4. **Docker Network**: Hỗ trợ giao tiếp giữa các Docker containers.
5. **Docker Volume**: Cung cấp persistent storage cho dữ liệu của container.

## Các trường hợp sử dụng Docker (Use Cases)

1. **Kiến trúc Microservices (Microservices Architecture)**: Triển khai và scale các dịch vụ riêng lẻ một cách độc lập.
2. **Continuous Integration/Continuous Deployment (CI/CD)**: Tối ưu hóa quy trình phát triển và triển khai.
3. **Môi trường Phát triển (Development Environments)**: Tạo các môi trường phát triển nhất quán giữa các đội ngũ.
4. **Cô lập Ứng dụng (Application Isolation)**: Chạy nhiều phiên bản của một ứng dụng trên cùng một host.
5. **Di chuyển Ứng dụng Cũ (Legacy Application Migration)**: Container hóa các ứng dụng cũ để dễ dàng quản lý và triển khai hơn.

## Kết luận

Docker đã cách mạng hóa cách các ứng dụng được phát triển, chuyển giao và vận hành. Bằng cách cung cấp một cách tiêu chuẩn hóa để đóng gói và triển khai ứng dụng, Docker giải quyết nhiều thách thức trong phát triển và vận hành phần mềm hiện đại. Khi chúng ta tiếp tục đi sâu vào cuốn sách này, chúng ta sẽ tìm hiểu kỹ hơn từng khía cạnh của Docker, giúp bạn có kiến thức và kỹ năng để tận dụng công nghệ mạnh mẽ này một cách hiệu quả.
