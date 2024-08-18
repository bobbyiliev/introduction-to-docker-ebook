# Chapter 16: Docker for Data Science and Machine Learning

Docker has become an essential tool in the data science and machine learning ecosystem, providing reproducibility, portability, and scalability for complex data processing and model training workflows.

## 1. Setting Up a Data Science Environment

### Jupyter Notebook with Docker

```dockerfile
FROM python:3.8
RUN pip install jupyter pandas numpy matplotlib scikit-learn
WORKDIR /notebooks
EXPOSE 8888
CMD ["jupyter", "notebook", "--ip='*'", "--port=8888", "--no-browser", "--allow-root"]
```

Running the container:

```bash
docker run -p 8888:8888 -v $(pwd):/notebooks my-datascience-notebook
```

## 2. Managing Dependencies with Docker

### Using conda in Docker

```dockerfile
FROM continuumio/miniconda3
COPY environment.yml .
RUN conda env create -f environment.yml
SHELL ["conda", "run", "-n", "myenv", "/bin/bash", "-c"]
```

## 3. GPU Support for Machine Learning

### Using NVIDIA Docker

```dockerfile
FROM nvidia/cuda:11.0-base
RUN pip install tensorflow-gpu
COPY train.py .
CMD ["python", "train.py"]
```

Running with GPU support:

```bash
docker run --gpus all my-gpu-ml-container
```

## 4. Distributed Training with Docker Swarm

```yaml
version: '3'
services:
  trainer:
    image: my-ml-image
    deploy:
      replicas: 4
    command: ["python", "distributed_train.py"]
```

## 5. MLOps with Docker

### Model Serving with Flask

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

Dockerfile for serving:

```dockerfile
FROM python:3.8
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY app.py .
COPY model.pkl .
EXPOSE 5000
CMD ["python", "app.py"]
```

## 6. Data Pipeline with Apache Airflow

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

## 7. Reproducible Research with Docker

```dockerfile
FROM rocker/rstudio
RUN R -e "install.packages(c('ggplot2', 'dplyr'))"
COPY analysis.R .
CMD ["R", "-e", "source('analysis.R')"]
```

## 8. Big Data Processing with Docker

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

## 9. Automated Machine Learning (AutoML) with Docker

```dockerfile
FROM python:3.8
RUN pip install auto-sklearn
COPY automl_script.py .
CMD ["python", "automl_script.py"]
```

## 10. Hyperparameter Tuning at Scale

Using Optuna with Docker Swarm:

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

## Conclusion

Docker provides powerful tools for creating reproducible, scalable, and portable environments for data science and machine learning workflows. By leveraging Docker's capabilities, data scientists and ML engineers can focus on their core tasks while ensuring their work is easily shareable and deployable.
