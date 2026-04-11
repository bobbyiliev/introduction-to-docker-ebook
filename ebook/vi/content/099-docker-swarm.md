# Docker Swarm mode là gì?

Theo tài liệu chính thức của **Docker**, một swarm là một nhóm các máy đang chạy **Docker** và được kết nối lại thành một cụm (cluster). Nếu bạn đang vận hành một **Docker swarm**, các câu lệnh của bạn sẽ được thực thi trên một cluster bởi một swarm manager. Các máy trong một swarm có thể là máy vật lý hoặc máy ảo. Sau khi gia nhập một swarm, chúng được gọi là các nodes. Tôi sẽ thực hiện một bản demo nhanh sớm trên tài khoản **DigitalOcean** của mình!

**Docker Swarm** bao gồm các **manager nodes** và **worker nodes**.

Các manager nodes phân phối các tác vụ (tasks) cho các worker nodes, và ngược lại, các worker nodes chỉ thực thi các tác vụ đó. Để đạt được tính sẵn sàng cao (High Availability), khuyến nghị nên có **3** hoặc **5** manager nodes.

## Docker Services

Để triển khai một application image khi Docker Engine đang ở chế độ swarm mode, bạn phải tạo một service. Một service là một nhóm các containers có cùng `image:tag`. Services giúp việc mở rộng (scale) ứng dụng của bạn trở nên đơn giản.

Để có được các **Docker services**, trước tiên bạn phải chuẩn bị sẵn **Docker swarm** và các nodes.

![](https://cdn.devdojo.com/posts/images/May2020/services-diagram.jpg)

## Xây dựng một Swarm

Tôi sẽ thực hiện một bản demo rất nhanh về cách xây dựng một **Docker swarm với 3 managers và 3 workers**.

Để làm điều đó, tôi sẽ triển khai 6 droplets trên DigitalOcean:

![](https://cdn.devdojo.com/posts/images/May2020/docker-swarm.png)

Sau khi bạn đã chuẩn bị sẵn sàng, hãy **install docker** giống như chúng ta đã làm trong phần **[Introduction to Docker Part 1](https://devdojo.com/tutorials/introduction-to-docker-part-1)** và sau đó chỉ cần làm theo các bước tại đây:

### Bước 1

Khởi tạo docker swarm trên manager node đầu tiên của bạn:

```
docker swarm init --advertise-addr your_dorplet_ip_here
```

### Bước 2

Sau đó, để lấy câu lệnh mà bạn cần dùng để kết nối các managers còn lại, chỉ cần chạy lệnh sau:

```
docker swarm join-token manager
```

> Lưu ý: Lệnh này sẽ cung cấp cho bạn chính xác câu lệnh mà bạn cần chạy trên các swarm manager nodes còn lại. Ví dụ:

![](https://cdn.devdojo.com/posts/images/May2020/docker-swarm-join-managers-bobby-iliev.png)

### Bước 3

Để lấy câu lệnh mà bạn cần dùng để kết nối các workers, chỉ cần chạy:

```
docker swarm join-token worker
```

Câu lệnh cho workers sẽ khá giống với câu lệnh để kết nối managers nhưng token sẽ hơi khác một chút.

Đầu ra (output) mà bạn nhận được khi kết nối một manager sẽ trông như thế này:

![](https://cdn.devdojo.com/posts/images/May2020/docker-join-manager.png)

### Bước 4

Sau khi bạn đã có các câu lệnh kết nối, hãy **ssh tới các nodes còn lại và kết nối chúng** dưới dạng workers và managers tương ứng.

# Quản lý cluster

Sau khi bạn đã chạy các câu lệnh kết nối trên tất cả các workers và managers của mình, để lấy một số thông tin về trạng thái cluster, bạn có thể sử dụng các lệnh sau:

* Để liệt kê tất cả các nodes hiện có, hãy chạy:

```
docker node ls
```

> Lưu ý: Câu lệnh này chỉ có thể được chạy từ một **swarm manager**! Kết quả:

![](https://cdn.devdojo.com/posts/images/May2020/docker-node-ls.png)

* Để lấy thông tin về trạng thái hiện tại, hãy chạy:

```
docker info
```

Kết quả:

![](https://cdn.devdojo.com/posts/images/May2020/docker-info-bobby-iliev.png)

## Nâng cấp một worker lên manager (Promote)

Để nâng cấp một worker lên thành một manager, hãy chạy lệnh sau từ **một** trong các manager nodes của bạn:

```
docker node promote node_id_here
```

Cũng cần lưu ý rằng mỗi manager cũng đóng vai trò như một worker, vì vậy từ kết quả của lệnh docker info, bạn sẽ thấy 6 workers và 3 manager nodes.

## Sử dụng Services

Để tạo một service, bạn cần sử dụng câu lệnh sau:

```
docker service create --name bobby-web -p 80:80 --replicas 5 bobbyiliev/php-apache
```

Lưu ý rằng tôi đã có sẵn image bobbyiliev/php-apache được push lên Docker hub như đã mô tả trong các bài viết trước.

Để lấy danh sách các services của bạn, hãy chạy:

```
docker service ls
```

Kết quả:

![](https://cdn.devdojo.com/posts/images/May2020/docker-create-service.png)

Sau đó, để lấy danh sách các containers đang chạy, bạn cần sử dụng câu lệnh sau:

```
docker service ps name_of_your_service_here
```

Kết quả:

![](https://cdn.devdojo.com/posts/images/May2020/docker-service-ps.png)

Sau đó, bạn có thể truy cập vào địa chỉ IP của bất kỳ node nào và bạn sẽ có thể thấy service đó! Về cơ bản, chúng ta có thể truy cập bất kỳ node nào trong swarm và vẫn sẽ kết nối được tới service.

## Mở rộng một service (Scaling)

Chúng ta có thể thử tắt một trong các nodes và xem cách swarm tự động khởi chạy một process mới trên một node khác để khớp với trạng thái mong muốn là 5 replicas.

Để làm điều đó, hãy truy cập vào bảng điều khiển (control panel) của **DigitalOcean** và nhấn nút tắt nguồn cho một trong những Droplets của bạn. Sau đó quay lại terminal và chạy:

```
docker service ps name_of_your_service_here
```

Kết quả:

![](https://cdn.devdojo.com/posts/images/May2020/docker-replicas.png)

Trong ảnh chụp màn hình ở trên, bạn có thể thấy cách tôi đã tắt droplet tên là worker-2 và cách replica bobby-web.2 được khởi động lại ngay lập tức trên một node khác tên là worker-01 để khớp với trạng thái mong muốn là 5 replicas.

Để thêm nhiều replicas hơn, hãy chạy:

```
docker service scale name_of_your_service_here=7
```

Kết quả:

![](https://cdn.devdojo.com/posts/images/May2020/docker-more-replicas.png)

Lệnh này sẽ tự động khởi chạy thêm 2 containers, bạn có thể kiểm tra điều này bằng lệnh docker service ps:

```
docker service ps name_of_your_service_here
```

Sau đó, hãy thử bắt đầu lại node mà chúng ta đã tắt và kiểm tra xem nó có nhận bất kỳ tác vụ nào không?

**Mẹo**: Việc thêm các nodes mới vào cluster không tự động phân phối lại các tác vụ đang chạy.

## Xóa một service

Để xóa một service, tất cả những gì bạn cần làm là chạy câu lệnh sau:

```
docker service rm name_of_your_service
```

Kết quả:

![](https://cdn.devdojo.com/posts/images/May2020/docker-delete-service.png)

## Sử dụng Stack Deploy

Docker Swarm cũng hỗ trợ triển khai stack (stack deployment) sử dụng file Docker Compose.
Để sử dụng stack deploy, trước tiên chúng ta cần tạo một file Docker Compose cho ứng dụng của mình.

Để phục vụ mục đích học tập, chúng ta có thể tạo một file Docker Compose mẫu như dưới đây:
```yaml
services:
  web:
    image: nginx:latest
    ports:
      - "8080:80"
    deploy:
      replicas: 3
      restart_policy:
        condition: on-failure
      update_config:
        parallelism: 2
        delay: 10s
      placement:
        constraints:
          - "node.role == worker"
```

Ở đây:
* `replicas` biểu thị số lượng replicas sẽ được tạo cho bản deployment.
* `node.role == worker` định nghĩa rằng chúng ta chỉ muốn triển khai ứng dụng trên các worker nodes.

Lưu file này với tên docker-compose.yml

Bây giờ, để triển khai stack, hãy chạy lệnh sau:

```
docker stack deploy -c docker-compose.yml your_stack_name
```

Để liệt kê tất cả các stacks đã triển khai, sử dụng:

```
docker stack ls
```

Để xem các services đang chạy dưới một stack cụ thể, chạy:

```
docker stack ps your_stack_name
```

Để xóa một stack, thực thi lệnh:

```
docker stack rm your_stack_name
```

Bây giờ bạn đã biết cách khởi tạo và mở rộng một cụm docker swarm! Để biết thêm thông tin, hãy đảm bảo đọc qua tài liệu chính thức của Docker [tại đây](https://docs.docker.com/engine/swarm/).

## Kiểm tra kiến thức về Docker Swarm

Sau khi đọc bài viết này, hãy nhớ kiểm tra kiến thức của bạn với bản **[Docker Swarm Quiz](https://quizapi.io/predefined-quizzes/common-docker-swarm-interview-questions)** tại đây:

[https://quizapi.io/predefined-quizzes/common-docker-swarm-interview-questions](https://quizapi.io/predefined-quizzes/common-docker-swarm-interview-questions)
