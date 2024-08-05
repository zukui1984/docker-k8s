# Docker & Kubernetes Project

## Content ##
1. Flask app with Docker (Python)
2. Deploy Flask app on K8S locally (Minikube)
3. Deploy Flask app on Google Kubernetes Engine (GKE)


## Project 1 - Flask app with Docker ## 
1. Installation Python, Flask & Docker Desktop
2. Create Flask project directory
```bash
mkdir flask-docker-app
cd flask-docker-app
```
3. Create Python virtual environment
```bash
python -m venv venv
source venv\Scripts\activate
```
<img src="https://github.com/user-attachments/assets/5e69cab8-5cc9-4a57-9aa0-9c9e97e8b2ed" width="200" />

4. Create Flask application
```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello, Docker!'

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```
5. Create Dockerfile
```dockerfile
FROM python:3.9-slim
WORKDIR /app
COPY . /app
RUN pip install flask
EXPOSE 5000
ENV FLASK_APP=app.py
CMD ["python", "app.py"]
```
6. Build Docker Image
```bash
docker build -t flask-docker-app .
```
7. Run Docker container
```bash
docker run -p 5000:5000 flask-docker-app
```
8. Testing on "localhost:5000" & you should see this

<img src="https://github.com/user-attachments/assets/82456aba-42a3-4934-90c3-de1d57df029c" width="300" />

9.  
   


## Project 2 - Flask app on Minikube locally ##


## Project 3 - Flask app on GKE ##
