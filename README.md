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

To stop it(if you   dont want to delete the data):  minikube stop 
To delete: minikuve delete
To start: minikube start
minikube status
Companies useinfrastructure like : AWS EKS 
"just info:
Pod name structure:
rag-app (deployment name) + 7c6d9f8b4 (ReplicaSet ID) + xxxxx (unique pod ID)
When Kubernetes creates a replacement pod, it will have a different random suffix!
Pod vs Deployment:
- Pod: Ephemeral (temporary). When deleted, it's gone forever. - Deployment: Persistent intent. It says "I always want 1 pod running" and ensures that's true. "

Stage and Commit:
Initialize Git Repository(Initializes a new Git repository in your project folder, allowing you to track changes to your code):  git init
create .gitignore
stage all files:  git add .
commit:  git commit -m "Initial commit: set up local RAG API project"

Create GitHub Repository:
Go to github.com and sign in
Click + → New repository
Name it nextwork-rag-api
Choose Public visibility
Don't initialize with README
Click Create repository

Push to GitHub:
Add remote: git remote add origin https://github.com/YOUR_USERNAME/nextwork-rag-api.git
Push: git push -u origin main (or master if that's your branch)


Create Semantic Tests:
create semantic_test.py
pip install requests

Run Tests:
Make sure your API is running: uvicorn app:app --host 127.0.0.1 --port 8000
In another terminal, run: python semantic_test.py
The test should pass!

Experiment: Break and Fix
Remove "orchestration" from k8s.txt
Rebuild embeddings: python embed.py
Restart your API
Run the test again - it should fail!

"
- LLM outputs are non-deterministic - the same input can produce different outputs. This makes automated testing unreliable!
- Let's solve this problem by implementing mock LLM mode - a way to test retrieval quality without relying on LLM generation. This will make your tests deterministic and enable reliable automation! "

ADD mock mode to app.py:
What does this do?
Checks the USE_MOCK_LLM environment variable. If set to "1", returns retrieved context directly (deterministic). Otherwise, uses the real LLM.

Test Mock Mode:
Start your API in mock mode:  USE_MOCK_LLM=1 uvicorn app:app --reload
python semantic_test.py

Commit and Push:
Stage: git add app.py
Commit: git commit -m "Add mock LLM mode for deterministic testing"
Push: git push

"GitHub looks for automation workflows in .github/workflows/ by convention.

create:  .github/workflows/ci.yml

💡 What does this workflow do?
Automatically tests your RAG system whenever you push changes to key files. It rebuilds embeddings, starts the API in mock mode, and runs semantic tests."

Commit and Push:
Stage: git add .github/workflows/ci.yml semantic_test.py
Commit: git commit -m "Add GitHub Actions CI workflow"
Push: git push








git troubleshoots:
1. "git rev-parse --show-toplevel will print the root folder of the repository Git is using for your current directory"

pwd
git rev-parse --show-toplevel 
ls -la

2. "That shows the remote repository URLs configured for this repo. If nothing appears, then it is not connected to any GitHub repo yet; if a URL appears, that is the GitHub repository this local repo will push to" 

git remote -v

3. 
git status --ignored
git ls-files | grep venv

4. 
git rm -r --cached venv
git commit -m "Stop tracking venv"