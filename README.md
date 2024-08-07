# Docker & Kubernetes (GKE & Minikube) Project

## Content ##
1. Flask app with Docker (Python)
2. Deploy Flask app on K8S locally (Minikube)
3. Deploy Flask app on Google Kubernetes Engine (GKE)


## Project 1 - Flask app with Docker ## 
1. Installation Python, Flask, Kubernetes, GCP SDK & Docker Desktop
2. Create Flask project directory
```bash
mkdir flask-app
cd flask-app
```
3. Create Python virtual environment
```bash
python3 -m venv venv
source venv\Scripts\activate
```
<img src="https://github.com/user-attachments/assets/2002fd92-7303-4d83-8945-06d8a2e9404b" width="200" />

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
5. Authenticate with Google
    - gcloud auth login
    - gcloud config set project [project ID]

6. Create Dockerfile
```dockerfile
FROM python:3.9-slim
WORKDIR /app
COPY . /app
RUN pip install flask
EXPOSE 5000
ENV FLASK_APP=app.py
CMD ["python", "app.py"]
```
7. Build Docker Image
```bash
docker build -t flask-docker-app .
```
8. Run Docker container
```bash
docker run -p 5000:5000 flask-docker-app
```
9. Testing on "localhost:5000" & you should see this

<img src="https://github.com/user-attachments/assets/82456aba-42a3-4934-90c3-de1d57df029c" width="300" />

## Project 2 - Flask app on Minikube locally ##
1. Start minikube - "minikube start"

<img src="https://github.com/user-attachments/assets/d2ac11f0-6333-46ff-88cf-ebd2625f4a49" width="600" />

3. Create deployment.yaml & service.yaml - (LINK[https://github.com/zukui1984/docker-k8s-project/blob/master/deplyoment.yaml])
```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```
4. Load the Docker image into Minikube "minikube image load flask-app"
5. Expose deployment
```bash
kubectl expose deployment flask-kube-demo --type=LoadBalancer --port=5000"
```
6. Run Minikube service URL 
```bash
minikube service flask-app --url
```

## Project 3 - Flask app on GKE ##
1. Enable Google Artifact Registry either from API Library or command-line (Cloud Shell)
2. Create docker repository in Artifact Registry
```bash
gcloud artifacts repositories create flask-app \
    --repository-format=docker \
    --location=us-central1 \
    --description="Hello Docker Container"
```
<img src="https://github.com/user-attachments/assets/bebf306b-9a39-4a54-906f-9defbddd8f86" width="700"/>

3. Push Docker Image
    - Configure Docker to authenticate request to Artifact Registry. "gcloud auth configure-docker us-central1-docker.pkg.dev"

<img src="https://github.com/user-attachments/assets/9c6fdefc-3ef7-4da7-be12-8c6882138294" width="600"/>
  
    - Tag Docker local Image. "docker tag flask-kube-demo us-central1-docker.pkg.dev/docker-k8s-431619/flask-app/flask-app"
    - Push Image to Artifact Registry. "docker push us-central1-docker.pkg.dev/docker-k8s-431619/flask-app/flask-app"
  
<img src="https://github.com/user-attachments/assets/7190900c-cda8-4e99-b5d2-f19225cb77b1" width="600"/>

4. Set up Google Artifact Registry
    - Create GKE cluster
   ```bash
   gcloud container clusters create flask-app \
   --num-nodes=3 \
   --region=us-central1
   ```
    - Get authentication credentials for the cluster
   ```bash
   gcloud container clusters get-credentials flask-app --region=us-central1
   ```
5. Deploy the Application to GKE
   - Create GKE Cluster
   ```bash
   gcloud container clusters create flask-app-cluster \
    --num-nodes=3 \
    --region=us-central1
   ```
   <img src="https://github.com/user-attachments/assets/88de0923-e518-4fd7-8244-aa3ce8e4a155" width="600"/> 
   
   - Get authentication credentials for the cluster
   ```bash
   gcloud container clusters get-credentials flask-app-cluster --region=us-central1
   ```
   - Update deployment.yaml (*Change the image!*)
   ```bash
     containers:
      - name: flask-app
        image: us-central1-docker.pkg.dev/docker-k8s-431619/flask-app/flask-app
        ports:
        - containerPort: 5000
   ```
   - Apply the deployment to GKE
   ```bash
   kubectl apply -f deployment.yaml
   ```
   
   - Expose the deployment
   ```bash
   kubectl expose deployment flask-app --type=LoadBalancer --port=5000
   ```
   
   - Check if everything running properly with "kubectl get pods" or "kubectl get deployments"
     
    <img src="https://github.com/user-attachments/assets/91085cf6-2210-4c1f-9aaf-b8fb42a4b210" width="600"/>
   
   - Get external IP of service
   ```bash
   kubectl get service flask-app
   ```
   
   ![image](https://github.com/user-attachments/assets/4fbef351-f9b9-4167-869b-9cd5e4b0423a)

 6. Delete Kubernetes if you dont use them anymore
    - kubectl delete deployments --all --all-namespaces
    - kubectl delete services --all --all-namespaces
    - kubectl delete pods --all --all-namespaces

 
