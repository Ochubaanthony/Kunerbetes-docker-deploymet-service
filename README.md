# Kunerbetes-docker-deploymet-service
deployment of kubernetes with the docker image for the kubernetes deployment.yml and service.yml

MAKE SURE YOUR Docker Desktop, Minikube are Running

**Clone the GitHub**
git clone https://github.com/iam-veeramalla/Docker-Zero-to-Hero.git
cd Docker-Zero-to-Hero/examples
cd python-web-app


**Create a Dockerfile**
nano Dockerfile.yml


FROM ubuntu:20.04

ENV DEBIAN_FRONTEND=noninteractive

WORKDIR /app

COPY requirements.txt /app/
COPY devops /app/devops/

RUN apt-get update && \
    apt-get install -y python3 python3-pip python3-venv && \
    pip3 install --upgrade pip && \
    pip3 install -r requirements.txt

WORKDIR /app/devops

EXPOSE 8000

ENTRYPOINT ["python3"]
CMD ["manage.py", "runserver", "0.0.0.0:8000"]



**RUN**
docker build -t tonymore/sample-python-app-demo:v1 .

**PUSH**
docker push tonymore/sample-python-app-demo:v1




**Create Deployment.yml**
nano deployment.yml

Deployment.yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample-python-app-demo
  labels:
    app: sample-python-app-demo
spec:
  replicas: 2
  selector:
    matchLabels:
      app: sample-python-app-demo
  template:
    metadata:
      labels:
        app: sample-python-app-demo
    spec:
      containers:
      - name: python-app-demo
        image: tonymore/sample-python-app-demo:v1
        ports:
        - containerPort: 8000



**RUN**
kubectl apply -f deployment.yml 
kubectl get deploy
kubectl get pods
kubectl delete pod python-sample-app-6455f896c6-pwjx4
kubectl get pods -o wide                (TAKE NOTE OF THE IP BEFORE YOU DELETE THE POD)
kubectl get pods
kubectl get pods -o wide                 (THE IP HAS CHANGE WHEN EVER IT CREATE A REPLICATE AND CHANGE THE IP)

**VALIDATION OF THE IP**
minikube ssh
curl -L http://10.244.0.48:8000/demo
This IP can not be access outside because by default pods come with a Cluster IP 


**Create Service.yml**
nano service.yml

apiVersion: v1
kind: Service
metadata:
  name: python-django-service-app
spec:
  type: NodePort
  selector:
    app: sample-python-app-demo
  ports:
    - port: 80
      targetPort: 8000
      nodePort: 30007


**EDIT**
Name, type, selector app, targetPort from the Dockerfile, nodePort of your choice range

**NOTE** 
MAKE SURE COPY THE CORRECT APP NAME FROM Deployment.yml and paste on Service.yml to enable both communicate


**RUN** 
kubectl apply -f service.yml 
kubectl get svc


**VALIDATION ON THE NEW IP GENERATED FOR THE NODEPORT** 
minikube ssh
curl -L http://10.104.24.162:80/demo

minikube  ip.      192.168.58.2
curl -L http://192.168.58.2:30007/demo
minikube get svc
minikube service python-django-service-app






