# Kubernetes-Microservice-Flask-Application
Deploy  microservices  in Kubernetes cluster using Kubeadm, Flask &amp; MongoDb (services)

This is a microservice application built using Flask and deployed on Kubernetes. It is designed to demonstrate how to build and deploy microservices on a Kubernetes cluster.

## Table of content 
- [Installation](#installation)
- [Task](#Task)
- [Contributing](#contributing)
- [License](#license)


  ## Installation
    Step 1: Cloning the Source Code of the Project
  
    `git clone https://github.com/LondheShubham153/microservices-k8s.git`
  
    Step 2: Build the Dockerfile
    Create a Dockerfile in the root directory of your project with the following content:
    
    ```
    FROM python:alpine3.7
    COPY . /app
    WORKDIR /app
    RUN pip install -r requirements.txt
    ENV PORT 5000
    EXPOSE 5000
    ENTRYPOINT [ "python" ]
    ```
    Next, build a Docker Image from this Dockerfile.
    
    `docker build . -t your_username/microservicespythonapp:latest`
    
    Step 3: Push the Docker Image to Docker Hub
    
    ```
    docker login
    docker push your_username/microservicespythonapp:latest
    ```
    Step 4: Creating a Persistent Volume Manifest for K8s.
    
    Create a file named mongo-pv.yml with the following content:
  
    ```
     apiVersion: v1
    kind: PersistentVolume
    metadata:
    name: mongo-pv
    namespace: microservices-k8s
    spec:
    capacity:
      storage: 256Mi
    volumeMode: Filesystem
    accessModes:
      - ReadWriteOnce
    hostPath:
      path: /home/nid/mongo-dv
    ```
    Step 5: Creating a Persistent Volume Claim (PVC) Manifest in K8s
    
    Create a file named mongo-pvc.yml with the following content:
  
    ```
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
    name: mongo-pvc
    namespace: microservices-k8s
    spec:
    accessModes:
      - ReadWriteOnce
    resources:
      requests:
        storage: 256Mi
    ```
    Step 6: Create a Deployment Manifest File of MongoDB
  
  ```
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: mongo-deployment
    namespace: microservices-k8s
  spec:
    replicas: 1
    selector:
      matchLabels:
        app: mongo
    template:
      metadata:
        labels:
          app: mongo
      spec:
        containers:
          - name: mongo
            image: mongo:latest
            ports:
              - containerPort: 27017
            volumeMounts:
              - name: mongo-storage
                mountPath: /data/db
        volumes:
          - name: mongo-storage
            persistentVolumeClaim:
              claimName: mongo-pvc
  ```
  This deploys a single MongoDB container with the necessary configurations, including a PersistentVolumeClaim named `mongo-pvc`.
  
  Step 6: Create a Service Manifest File of MongoDB
  
  ```
  apiVersion: v1
  kind: Service
  metadata:
    name: mongo-service
  spec:
    selector:
      app: mongo
    ports:
      - protocol: TCP
        port: 27017
        targetPort: 27017
    selector: mongo
  ```
  This creates a NodePort service named `mongo-svc` for the MongoDB deployment.
  
  Step 7: Create the Deployment Manifest File of the Flask App
  
  Create a file named `taskmaster.yml` with the following content:
  
  ```
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: taskmaster
    namespace: microservices-k8s
    labels:
      app: taskmaster
  spec:
    replicas: 1
    selector:
      matchLabels:
        app: taskmaster
    template:
      metadata:
        name: taskmaster
        namespace: microservices-k8s
        labels:
          app: taskmaster
      spec:
        containers:
          - name: taskmaster
            image: simbaa815/microservicespythonapp:latest
            ports:
              - containerPort: 5000
            imagePullPolicy: Always
  ```
  Step 8: Create the Service Manifest File of the Flask App
  
  Create a file named `flask-app-svc.yml` with the following content:
  ```
  apiVersion: v1
  kind: Service
  metadata:
    name: taskmaster-svc
    namespace: microservices-k8s
  spec:
    selector:
      app: taskmaster
    ports:
      - protocol: TCP
        port: 80
        targetPort: 5000
        nodePort: 30007
    type: NodePort
  ```
  
## Task 
    1. Setup kudenetes cluster using kudeADM.
    2. Deploy the taskmaster Micro-Services.
    3. Explose the Services to Postman.
    4. Integrate Persistent Volumes and Persistent Volumes Clain.
    5. Deploy MongoDB.
    6. Integrate Micro-Services/ Troubleshoot.

- Create 2 AWS EC2 with the naming 'Master' and 'Worker'.
- Follow the `kubernet config.sh` to setup the pre-requisite.
- Create a Kubernetes deployment and service by running the following command:
  - `kubectl apply -f kubernetes.yaml`
  - `kubectl apply -f mongo.yml`
  - `kubectl apply -f taskmaster.yml`
- Verify that the deployment and service have been created successfully by running the following command:
  - `kubectl get deployments,services`.
  - 
  - 
- 

  
