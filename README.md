# RAG-API_Kubernetes

app.py -  FastAPI application
embed.py - the script that creates embeddings
k8s.txt - your knowledge base document

start venv: source venv/bin/activate
uvicorn --version
check ollama is running :  curl http://localhost:11434
Verify the required packages are installed: pip list | grep -E "fastapi|uvicorn|chromadb|ollama"

start API:   uvicorn app:app --host 127.0.0.1 --port 8000

docker --version
test docker:  docker run hello-world

create this Dockerfile - A text file with instructions for building a Docker image. It specifies the base image, copies your code, installs dependencies, and defines how to run your app.

- open docker to activate it 
TO check if docker is running(in terminal, not docker) :  docker run hello-world
Build docker image:  docker build -t rag-app .
verify :  docker images | grep rag-app

start container: docker run -p 8000:8000 rag-app
test containerized API:  curl -X POST "http://127.0.0.1:8000/query" -G --data-urlencode "q=What is Kubernetes?"

Install Minikube:  brew install minikube
minikube version
brew install kubectl
kubectl version --client
kubectl config view

minikube start
verify the cluster:  kubectl get nodes
again check:  docker images | grep rag-app

Switch to Minikube's Docker environment:  eval $(minikube docker-env)
cd ~/Desktop/nextwork-rag-api
Build your image in Minikube:  docker build -t rag-app .
verify image in minkube:  docker images | grep rag-app

Create Kubernetes Deployment:
add code to this file - Create a file called deployment.yaml.
kubectl apply -f deployment.yaml
check deployment status:  kubectl get deployments
check pod status:  kubectl get pods

Create Kubernetes Service:
Create a file called service.yaml
kubectl apply -f service.yaml
kubectl get services

Access Your API Using NodePort:
Get the URL to access your API:  minikube service rag-app-service --url
Keep this terminal window open (it's creating a tunnel).

Test Your API:
Open a new terminal window.
curl -X POST "your-api-url/query" -G --data-urlencode "q=What is Kubernetes?"
curl -X POST "http://127.0.0.1:54503/query" -G --data-urlencode "q=What is Kubernetes?"

Deleting pod and healing:
kubectl get pods
in new terminal: kubectl get pods --watch
In old terminal:  kubectl delete pod rag-app-deployment-86b57f8f77-nfpr2

Test That Your API Stayed Available:
minikube service rag-app-service --url
Test:  curl -X POST "http://127.0.0.1:54832/query" -G --data-urlencode "q=What is Kubernetes?"

"just info:
Pod name structure:
rag-app (deployment name) + 7c6d9f8b4 (ReplicaSet ID) + xxxxx (unique pod ID)
When Kubernetes creates a replacement pod, it will have a different random suffix!
Pod vs Deployment:
- Pod: Ephemeral (temporary). When deleted, it's gone forever. - Deployment: Persistent intent. It says "I always want 1 pod running" and ensures that's true. "

Stage and Commit:
stage all files:  git add .






open new terminal and test: curl -X POST "http://127.0.0.1:8000/query" -G --data-urlencode "q=What is Kubernetes?"

