# Bab 16: Docker untuk Data Science dan Machine Learning

Docker telah menjadi alat penting dalam ekosistem data science dan machine learning, menyediakan reproducibility, portabilitas, dan skalabilitas untuk workflow pemrosesan data dan pelatihan model yang kompleks.

## 1. Menyiapkan Lingkungan Data Science

### Jupyter Notebook dengan Docker

```dockerfile
FROM python:3.8
RUN pip install jupyter pandas numpy matplotlib scikit-learn
WORKDIR /notebooks
EXPOSE 8888
CMD ["jupyter", "notebook", "--ip='*'", "--port=8888", "--no-browser", "--allow-root"]
```

Menjalankan container:

```bash
docker run -p 8888:8888 -v $(pwd):/notebooks my-datascience-notebook
```

## 2. Manajemen Dependensi dengan Docker

### Menggunakan conda di Docker

```dockerfile
FROM continuumio/miniconda3
COPY environment.yml .
RUN conda env create -f environment.yml
SHELL ["conda", "run", "-n", "myenv", "/bin/bash", "-c"]
```

## 3. Dukungan GPU untuk Machine Learning

### Menggunakan NVIDIA Docker

```dockerfile
FROM nvidia/cuda:11.0-base
RUN pip install tensorflow-gpu
COPY train.py .
CMD ["python", "train.py"]
```

Menjalankan dengan dukungan GPU:

```bash
docker run --gpus all my-gpu-ml-container
```

## 4. Pelatihan Terdistribusi dengan Docker Swarm

```yaml
version: '3'
services:
  trainer:
    image: my-ml-image
    deploy:
      replicas: 4
    command: ["python", "distributed_train.py"]
```

## 5. MLOps dengan Docker

### Model Serving dengan Flask

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

Dockerfile untuk serving:

```dockerfile
FROM python:3.8
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY app.py .
COPY model.pkl .
EXPOSE 5000
CMD ["python", "app.py"]
```

## 6. Data Pipeline dengan Apache Airflow

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

## 7. Riset Reproducible dengan Docker

```dockerfile
FROM rocker/rstudio
RUN R -e "install.packages(c('ggplot2', 'dplyr'))"
COPY analysis.R .
CMD ["R", "-e", "source('analysis.R')"]
```

## 8. Pemrosesan Big Data dengan Docker

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

## 9. Automated Machine Learning (AutoML) dengan Docker

```dockerfile
FROM python:3.8
RUN pip install auto-sklearn
COPY automl_script.py .
CMD ["python", "automl_script.py"]
```

## 10. Hyperparameter Tuning dalam Skala Besar

Menggunakan Optuna dengan Docker Swarm:

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

## Kesimpulan

Docker menyediakan alat yang kuat untuk menciptakan lingkungan yang reproducible, scalable, dan portable untuk workflow data science dan machine learning. Dengan memanfaatkan kemampuan Docker, data scientist dan ML engineer dapat fokus pada tugas inti mereka sambil memastikan pekerjaan mereka mudah dibagikan dan dideploy.
