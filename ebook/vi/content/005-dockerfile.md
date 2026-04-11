# Chương 5: Dockerfile là gì?

Một Dockerfile là một tài liệu văn bản chứa một loạt các chỉ dẫn (instructions) và đối số (arguments). Các chỉ dẫn này được sử dụng để tạo một Docker image một cách tự động. Về cơ bản, nó là một script gồm các lệnh liên tiếp mà Docker sẽ chạy để lắp ráp một image, giúp tự động hóa quy trình tạo image.

## Cấu trúc của một Dockerfile (Anatomy of a Dockerfile)

Một Dockerfile thường bao gồm các thành phần sau:

1. Khai báo base image
2. Thông tin Metadata và label
3. Thiết lập môi trường (Environment setup)
4. Các thao tác với file và thư mục
5. Cài đặt các phụ thuộc (Dependencies)
6. Sao chép mã nguồn ứng dụng
7. Chỉ định lệnh thực thi

Hãy đi sâu vào từng thành phần này và các chỉ dẫn được sử dụng để triển khai chúng.

## Các chỉ dẫn trong Dockerfile (Dockerfile Instructions)

### FROM

Chỉ dẫn `FROM` khởi tạo một build stage mới và thiết lập base image cho các chỉ dẫn tiếp theo.

```dockerfile
FROM ubuntu:20.04
```

Chỉ dẫn này thường là chỉ dẫn đầu tiên trong một Dockerfile. Có thể có nhiều chỉ dẫn `FROM` trong một Dockerfile duy nhất để thực hiện multi-stage builds.

### LABEL

`LABEL` thêm metadata vào một image dưới dạng cặp key-value.

```dockerfile
LABEL version="1.0" maintainer="john@example.com" description="This is a sample Docker image"
```

Labels hữu ích cho việc tổ chức image, thông tin bản quyền, chú thích và các metadata khác.

### ENV

`ENV` thiết lập các biến môi trường (environment variables) trong image.

```dockerfile
ENV APP_HOME=/app NODE_ENV=production
```

Các biến này vẫn tồn tại khi một container được chạy từ image kết quả.

### WORKDIR

`WORKDIR` thiết lập thư mục làm việc (working directory) cho bất kỳ chỉ dẫn `RUN`, `CMD`, `ENTRYPOINT`, `COPY`, và `ADD` nào tiếp theo.

```dockerfile
WORKDIR /app
```

Nếu thư mục không tồn tại, nó sẽ được tạo mới.

### COPY và ADD

Cả hai chỉ dẫn `COPY` và `ADD` đều sao chép các tệp từ host vào trong image.

```dockerfile
COPY package.json .
ADD https://example.com/big.tar.xz /usr/src/things/
```

`COPY` thường được ưu tiên hơn vì tính đơn giản của nó. `ADD` có một số tính năng bổ sung như giải nén tệp tar (tar extraction) và hỗ trợ URL từ xa (remote URL support), nhưng những tính năng này có thể làm cho hành vi build ít dễ dự đoán hơn.

### RUN

`RUN` thực thi các lệnh trong một layer mới trên lớp trên cùng của image hiện tại và commit kết quả.

```dockerfile
RUN apt-get update && apt-get install -y nodejs
```

Cách tốt nhất là chuỗi các lệnh (chain commands) bằng `&&` và thực hiện dọn dẹp trong cùng một chỉ dẫn `RUN` để giữ cho các layers nhỏ gọn.

### CMD

`CMD` cung cấp các giá trị mặc định cho một container đang thực thi. Chỉ có thể có một chỉ dẫn `CMD` duy nhất trong một Dockerfile.

```dockerfile
CMD ["node", "app.js"]
```

`CMD` có thể bị ghi đè (overridden) tại runtime.

### ENTRYPOINT

`ENTRYPOINT` cấu hình một container sẽ chạy như một tệp thực thi (executable).

```dockerfile
ENTRYPOINT ["nginx", "-g", "daemon off;"]
```

`ENTRYPOINT` thường được sử dụng kết hợp với `CMD`, trong đó `ENTRYPOINT` định nghĩa tệp thực thi và `CMD` cung cấp các đối số mặc định.

### EXPOSE

`EXPOSE` thông báo cho Docker rằng container sẽ lắng nghe trên các cổng mạng cụ thể tại runtime.

```dockerfile
EXPOSE 80 443
```

Lệnh này thực tế không xuất bản (publish) cổng; nó hoạt động như tài liệu hướng dẫn giữa người build image và người chạy container.

### VOLUME

`VOLUME` tạo một điểm gắn kết (mount point) và đánh dấu nó là nơi chứa các volumes được gắn bên ngoài từ host gốc (native host) hoặc các containers khác.

```dockerfile
VOLUME /data
```

Điều này hữu ích cho bất kỳ phần nào có thể thay đổi (mutable) và/hoặc người dùng có thể bảo trì (user-serviceable) trong image của bạn.

### ARG

`ARG` định nghĩa một biến mà người dùng có thể truyền vào tại thời điểm build (build-time) cho builder bằng lệnh `docker build`.

```dockerfile
ARG VERSION=latest
```

Điều này cho phép việc build image linh hoạt hơn.

## Các quy tắc tốt nhất khi viết Dockerfile (Best Practices)

1. **Sử dụng multi-stage builds**: Điều này giúp tạo ra các image cuối cùng nhỏ gọn hơn bằng cách tách biệt các phụ thuộc lúc build (build-time dependencies) khỏi các phụ thuộc lúc chạy (runtime dependencies).

```dockerfile
FROM node:14 AS build
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=build /app/dist /usr/share/nginx/html
```

2. **Tối thiểu hóa số lượng layers**: Kết hợp các lệnh khi có thể để giảm số lượng layers và kích thước image.

3. **Tận dụng build cache**: Sắp xếp các chỉ dẫn từ ít thay đổi nhất đến thường xuyên thay đổi nhất để tối đa hóa việc sử dụng build cache.

4. **Sử dụng `.dockerignore`**: Loại bỏ các tệp không liên quan đến việc build, tương tự như `.gitignore`.

5. **Không cài đặt các gói không cần thiết**: Giữ cho image tinh gọn và bảo mật bằng cách chỉ cài đặt những gì cần thiết.

6. **Sử dụng các tags cụ thể**: Tránh sử dụng tag `latest` cho các base images để đảm bảo việc build có tính tái lập (reproducible).

7. **Thiết lập `WORKDIR`**: Luôn sử dụng `WORKDIR` thay vì lạm dụng các chỉ dẫn như `RUN cd … && do-something`.

8. **Sử dụng `COPY` thay vì `ADD`**: Trừ khi bạn thực sự cần các tính năng bổ sung của `ADD`, hãy sử dụng `COPY` để đảm bảo tính minh bạch.

9. **Sử dụng biến môi trường**: Đặc biệt là đối với các số phiên bản và đường dẫn, giúp Dockerfile linh hoạt hơn.

## Các khái niệm Dockerfile nâng cao

### Health Checks

Bạn có thể sử dụng chỉ dẫn `HEALTHCHECK` để cho Docker biết cách kiểm tra một container để xác nhận rằng nó vẫn đang hoạt động tốt.

```dockerfile
HEALTHCHECK --interval=30s --timeout=10s CMD curl -f http://localhost/ || exit 1
```

### Dạng Shell và dạng Exec (Shell and Exec Forms)

Nhiều chỉ dẫn Dockerfile có thể được chỉ định ở dạng shell (shell form) hoặc dạng exec (exec form):

- Shell form: `RUN apt-get install python3`
- Exec form: `RUN ["apt-get", "install", "python3"]`

Dạng exec được ưu tiên hơn vì nó rõ ràng hơn và tránh được các vấn đề với việc biến đổi chuỗi shell (shell string munging).

### BuildKit

BuildKit là một backend mới cho việc build Docker, mang lại hiệu suất tốt hơn, quản lý lưu trữ và các tính năng ưu việt hơn. Bạn có thể kích hoạt nó bằng cách thiết lập một biến môi trường:

```bash
export DOCKER_BUILDKIT=1
```

## Kết luận

Dockerfiles là một công cụ mạnh mẽ để tạo ra các Docker images có tính tái lập và được quản lý phiên bản. Bằng cách làm chủ các chỉ dẫn và quy tắc tốt nhất trong Dockerfile, bạn có thể tạo ra các ứng dụng hiệu quả, bảo mật và linh hoạt. Hãy nhớ rằng viết Dockerfile tốt là một quy trình lặp đi lặp lại – hãy liên tục cải thiện Dockerfile của bạn khi bạn hiểu rõ hơn về nhu cầu của ứng dụng và các khả năng của Docker.
