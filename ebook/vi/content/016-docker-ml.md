# Chương 16: Docker cho Khoa học dữ liệu (Data Science) và Học máy (Machine Learning)

Docker đã trở thành một công cụ thiết yếu trong hệ sinh thái khoa học dữ liệu (data science) và học máy (machine learning), mang lại tính tái lập (reproducibility), tính di động (portability) và khả năng mở rộng (scalability) cho các quy trình xử lý dữ liệu phức tạp và huấn luyện mô hình.

## 1. Thiết lập môi trường Khoa học dữ liệu

### Jupyter Notebook với Docker

```dockerfile
FROM python:3.8
RUN pip install jupyter pandas numpy matplotlib scikit-learn
WORKDIR /notebooks
EXPOSE 8888
CMD ["jupyter", "notebook", "--ip='*'", "--port=8888", "--no-browser", "--allow-root"]
```

Khởi chạy container:

```bash
docker run -p 8888:8888 -v $(pwd):/notebooks my-datascience-notebook
```

## 2. Quản lý phụ thuộc với Docker

### Sử dụng conda trong Docker

```dockerfile
FROM continuumio/miniconda3
COPY environment.yml .
RUN conda env create -f environment.yml
SHELL ["conda", "run", "-n", "myenv", "/bin/bash", "-c"]
```

## 3. Hỗ trợ GPU cho Học máy

### Sử dụng NVIDIA Docker

```dockerfile
FROM nvidia/cuda:11.0-base
RUN pip install tensorflow-gpu
COPY train.py .
CMD ["python", "train.py"]
```

Chạy với hỗ trợ GPU:

```bash
docker run --gpus all my-gpu-ml-container
```

## 4. Huấn luyện phân tán với Docker Swarm

```yaml
version: '3'
services:
  trainer:
    image: my-ml-image
    deploy:
      replicas: 4
    command: ["python", "distributed_train.py"]
```

## 5. MLOps với Docker

### Phục vụ mô hình (Model Serving) với Flask

```python
from flask import Flask, request, jsonify
import pickle

app = Flask(__name__)
model = pickle.load(open('model.pkl', 'rb'))

@app.route('/predict', methods=['POST'])
def predict():
    data = request.json
    prediction = model.predict([data['features']])
    return jsonify({'prediction': prediction.tolist()})

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

Dockerfile cho việc phục vụ (serving):

```dockerfile
FROM python:3.8
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY app.py .
COPY model.pkl .
EXPOSE 5000
CMD ["python", "app.py"]
```

## 6. Đường ống dữ liệu (Data Pipeline) với Apache Airflow

```yaml
version: '3'
services:
  webserver:
    image: apache/airflow
    ports:
      - "8080:8080"
    volumes:
      - ./dags:/opt/airflow/dags
    command: webserver
  scheduler:
    image: apache/airflow
    volumes:
      - ./dags:/opt/airflow/dags
    command: scheduler
```

## 7. Nghiên cứu có tính tái lập (Reproducible Research) với Docker

```dockerfile
FROM rocker/rstudio
RUN R -e "install.packages(c('ggplot2', 'dplyr'))"
COPY analysis.R .
CMD ["R", "-e", "source('analysis.R')"]
```

## 8. Xử lý dữ liệu lớn (Big Data Processing) với Docker

### Spark Cluster

```yaml
version: '3'
services:
  spark-master:
    image: bitnami/spark:3
    environment:
      - SPARK_MODE=master
    ports:
      - "8080:8080"
  spark-worker:
    image: bitnami/spark:3
    environment:
      - SPARK_MODE=worker
      - SPARK_MASTER_URL=spark://spark-master:7077
    depends_on:
      - spark-master
```

## 9. Học máy tự động (Automated Machine Learning - AutoML) với Docker

```dockerfile
FROM python:3.8
RUN pip install auto-sklearn
COPY automl_script.py .
CMD ["python", "automl_script.py"]
```

## 10. Tinh chỉnh siêu tham số (Hyperparameter Tuning) ở quy mô lớn

Sử dụng Optuna với Docker Swarm:

```yaml
version: '3'
services:
  optuna-worker:
    image: my-optuna-image
    deploy:
      replicas: 10
    command: ["python", "optimize.py"]
  optuna-dashboard:
    image: optuna/optuna-dashboard
    ports:
      - "8080:8080"
```

## Kết luận

Docker cung cấp những công cụ mạnh mẽ để tạo ra các môi trường có tính tái lập (reproducible), có khả năng mở rộng (scalable) và tính di động (portable) cho các quy trình khoa học dữ liệu (data science) và học máy (machine learning). Bằng cách tận dụng các khả năng của Docker, các nhà khoa học dữ liệu và kỹ sư ML có thể tập trung vào những nhiệm vụ cốt lõi của họ trong khi đảm bảo công việc của mình có thể dễ dàng chia sẻ và triển khai.
