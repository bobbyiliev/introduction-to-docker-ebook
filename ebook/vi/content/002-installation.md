# Chương 2: Cài đặt Docker

Cài đặt Docker là bước đầu tiên trong hành trình khám phá containerization của bạn. Chương này sẽ hướng dẫn bạn quy trình cài đặt Docker trên các operating systems khác nhau, cách xử lý các sự cố thường gặp và xác minh cài đặt của bạn.

## Các phiên bản Docker (Docker Editions)

Trước khi bắt đầu, điều quan trọng là phải hiểu các phiên bản Docker khác nhau hiện có:

1. **Docker Engine - Community**: Nền tảng Docker miễn phí, open-source phù hợp cho các developer và các đội ngũ nhỏ.
2. **Docker Engine - Enterprise**: Được thiết kế cho các đội ngũ IT và phát triển doanh nghiệp đang xây dựng, vận hành và chạy các ứng dụng kinh doanh quan trọng ở quy mô lớn.
3. **Docker Desktop**: Một ứng dụng dễ cài đặt cho môi trường Mac hoặc Windows bao gồm Docker Engine, Docker CLI client, Docker Compose, Docker Content Trust, Kubernetes, và Credential Helper.

Đối với hầu hết người dùng, Docker Engine - Community hoặc Docker Desktop là đã đủ dùng.

## Cài đặt Docker trên Linux

Docker chạy native trên Linux, khiến nó trở thành nền tảng lý tưởng cho các Docker containers. Có hai phương pháp chính để cài đặt Docker trên Linux: sử dụng convenience script hoặc cài đặt thủ công cho các distributions cụ thể.

### Phương pháp 1: Sử dụng Docker Installation Script (Khuyên dùng để thiết lập nhanh)

Docker cung cấp một script thuận tiện giúp tự động phát hiện Linux distribution của bạn và cài đặt Docker cho bạn. Phương pháp này nhanh chóng và hoạt động trên nhiều Linux distributions:

1. Chạy lệnh sau để tải xuống và thực thi Docker installation script:
   ```
   wget -qO- https://get.docker.com | sh
   ```

2. Sau khi quá trình cài đặt hoàn tất, hãy khởi động Docker service:
   ```
   sudo systemctl start docker
   ```

3. Cho phép Docker tự động khởi chạy khi boot:
   ```
   sudo systemctl enable docker
   ```

Phương pháp này lý tưởng cho việc thiết lập nhanh và các môi trường thử nghiệm. Tuy nhiên, đối với các production environments, bạn có thể cân nhắc phương pháp cài đặt thủ công để có nhiều quyền kiểm soát hơn đối với quy trình.

### Phương pháp 2: Cài đặt thủ công cho các Distributions cụ thể

Để kiểm soát tốt hơn quy trình cài đặt hoặc nếu bạn muốn làm theo các bước cụ thể của từng distribution, bạn có thể cài đặt Docker thủ công. Dưới đây là hướng dẫn cho các Linux distributions phổ biến:

Docker chạy native trên Linux, khiến nó trở thành nền tảng lý tưởng cho các Docker containers. Dưới đây là cách cài đặt Docker trên các Linux distributions phổ biến:

### Ubuntu

1. Cập nhật package index của bạn:
   ```
   sudo apt-get update
   ```

2. Cài đặt các gói tiền đề (prerequisites):
   ```
   sudo apt-get install apt-transport-https ca-certificates curl software-properties-common
   ```

3. Thêm GPG key chính thức của Docker:
   ```
   curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
   ```

4. Thiết lập stable repository:
   ```
   sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
   ```

5. Cập nhật lại package index:
   ```
   sudo apt-get update
   ```

6. Cài đặt Docker:
   ```
   sudo apt-get install docker-ce docker-ce-cli containerd.io
   ```

### CentOS

1. Cài đặt các gói cần thiết:
   ```
   sudo yum install -y yum-utils device-mapper-persistent-data lvm2
   ```

2. Thêm Docker repository:
   ```
   sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
   ```

3. Cài đặt Docker:
   ```
   sudo yum install docker-ce docker-ce-cli containerd.io
   ```

4. Khởi động và cho phép Docker tự động chạy:
   ```
   sudo systemctl start docker
   sudo systemctl enable docker
   ```

### Các Linux Distributions khác

Đối với các Linux distributions khác, vui lòng tham khảo tài liệu chính thức của Docker: https://docs.docker.com/engine/install/

## Cài đặt Docker trên macOS

Đối với macOS, cách dễ nhất để cài đặt Docker là sử dụng Docker Desktop:

1. Tải xuống Docker Desktop cho Mac từ trang web chính thức của Docker: https://www.docker.com/products/docker-desktop

2. Nhấp đúp vào file `.dmg` đã tải xuống và kéo biểu tượng Docker vào Applications folder của bạn.

3. Mở Docker từ Applications folder.

4. Làm theo các hướng dẫn trên màn hình để hoàn tất quá trình cài đặt.

## Cài đặt Docker trên Windows

Đối với các phiên bản Windows 10 Pro, Enterprise, hoặc Education, bạn có thể cài đặt Docker Desktop:

1. Tải xuống Docker Desktop cho Windows từ trang web chính thức của Docker: https://www.docker.com/products/docker-desktop

2. Nhấp đúp vào bộ cài đặt để chạy nó.

3. Làm theo installation wizard để hoàn tất quá trình cài đặt.

4. Sau khi cài đặt, Docker Desktop sẽ tự động khởi động.

Đối với Windows 10 Home hoặc các phiên bản Windows cũ hơn, bạn có thể sử dụng Docker Toolbox, công cụ này sử dụng Oracle VirtualBox để chạy Docker:

1. Tải xuống Docker Toolbox từ: https://github.com/docker/toolbox/releases

2. Chạy bộ cài đặt và làm theo installation wizard.

3. Sau khi cài đặt, sử dụng Docker Quickstart Terminal để tương tác với Docker.

## Các bước sau khi cài đặt (Post-Installation Steps)

Sau khi cài đặt Docker, có một vài bước bạn nên thực hiện:

1. Xác minh cài đặt:
   ```
   docker version
   docker run hello-world
   ```

2. Cấu hình Docker để khởi động cùng hệ thống (chỉ dành cho Linux):
   ```
   sudo systemctl enable docker
   ```

3. Thêm người dùng của bạn vào docker group để chạy các Docker commands mà không cần sudo (chỉ dành cho Linux):
   ```
   sudo usermod -aG docker $USER
   ```
   Lưu ý: Bạn sẽ cần đăng xuất và đăng nhập lại để thay đổi này có hiệu lực.

## Docker Desktop so với Docker Engine

Điều quan trọng là phải hiểu sự khác biệt giữa Docker Desktop và Docker Engine:

- **Docker Desktop** là một ứng dụng thân thiện với người dùng bao gồm Docker Engine, Docker CLI client, Docker Compose, Docker Content Trust, Kubernetes, và Credential Helper. Nó được thiết kế để dễ dàng cài đặt và sử dụng trên Mac và Windows.

- **Docker Engine** là lõi Docker runtime có sẵn cho các hệ thống Linux. Nó không đi kèm với các công cụ bổ sung có trong Docker Desktop nhưng có thể được cài đặt riêng biệt cùng với chúng.

## Xử lý các sự cố cài đặt thường gặp

1. **Permission denied (Bị từ chối quyền truy cập)**: 
   Nếu bạn gặp lỗi "permission denied", hãy đảm bảo rằng bạn đã thêm người dùng của mình vào docker group hoặc đang sử dụng sudo.

2. **Docker daemon không chạy**: 
   Trên Linux, hãy thử khởi động Docker service: `sudo systemctl start docker`

3. **Xung đột với VirtualBox (trên Windows)**: 
   Đảm bảo Hyper-V đã được bật cho Docker Desktop, hoặc sử dụng Docker Toolbox nếu bạn cần tiếp tục sử dụng VirtualBox.

4. **Không đủ tài nguyên hệ thống**: 
   Docker Desktop yêu cầu ít nhất 4GB RAM. Hãy tăng dung lượng RAM được phân bổ cho hệ thống hoặc virtual machine của bạn nếu cần thiết.

## Cập nhật Docker

Để cập nhật Docker:

- Trên Linux, sử dụng package manager của bạn (ví dụ: `apt-get upgrade docker-ce` trên Ubuntu)
- Trên Mac và Windows, Docker Desktop sẽ tự động thông báo cho bạn về các bản cập nhật

## Gỡ cài đặt Docker

Nếu bạn cần gỡ cài đặt Docker:

- Trên Linux, sử dụng package manager của bạn (ví dụ: `sudo apt-get purge docker-ce docker-ce-cli containerd.io` trên Ubuntu)
- Trên Mac, xóa Docker Desktop khỏi Applications folder
- Trên Windows, gỡ cài đặt Docker Desktop từ Control Panel

## Kết luận

Cài đặt Docker thường là một quy trình đơn giản, nhưng nó có thể thay đổi tùy thuộc vào operating system của bạn. Luôn tham khảo tài liệu chính thức của Docker để biết hướng dẫn cài đặt cập nhật nhất cho hệ thống cụ thể của bạn. Với Docker đã được cài đặt thành công, giờ đây bạn đã sẵn sàng để bắt đầu khám phá thế giới của containerization!
