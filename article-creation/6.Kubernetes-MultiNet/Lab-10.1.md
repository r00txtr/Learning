# Lab 10.1: Using Service ClusterIP, NodePort, Deploying Kubernetes Dashboard, and Multi-Tier Application

In this lab, we will explore how to use **ClusterIP** and **NodePort** services in Kubernetes, deploy the **Kubernetes Dashboard** for cluster management, and deploy a multi-tier application with a web interface and database. These are essential skills for managing Kubernetes clusters and applications.

By the end of this lab, you will:
1. Deploy an application using ClusterIP and NodePort services.
2. Deploy and access the Kubernetes Dashboard.
3. Deploy a multi-tier application with a database and web front-end.

---

## Table of Contents
1. **Introduction to Kubernetes Services and Deployments**
2. **Step-by-Step Instructions**
    1. Deploying an Application with ClusterIP
    2. Deploying an Application with NodePort
    3. Deploying the Kubernetes Dashboard
    4. Deploying a Multi-Tier Application
3. **Conclusion**

---

## Introduction to Kubernetes Services and Deployments

### What is a ClusterIP Service?
A **ClusterIP** service is the default Kubernetes service type. It provides internal access within the cluster and is not accessible from outside the cluster.

### What is a NodePort Service?
A **NodePort** service exposes the service on a static port on each node’s IP. It makes the service accessible from outside the cluster by using `<NodeIP>:<NodePort>`.

### What is the Kubernetes Dashboard?
The **Kubernetes Dashboard** is a web-based UI for managing Kubernetes clusters. It allows you to monitor and manage resources, view logs, and even troubleshoot issues.

---

## Step-by-Step Instructions

### Step 1: Deploying an Application with ClusterIP

#### 1.1 Create the HTTPd Deployment YAML File

We will deploy an **HTTPd** application with 3 replicas and expose it internally using a **ClusterIP** service.

Create the deployment file `dep-httpd-clusterip.yaml`:

```bash
nano dep-httpd-clusterip.yaml
```

Add the following content:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: my-httpd
  name: my-httpd
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-httpd
  template:
    metadata:
      labels:
        app: my-httpd
    spec:
      containers:
      - image: httpd:latest
        name: httpd
        ports:
        - containerPort: 80
```

#### 1.2 Apply the Deployment

Create the deployment:

```bash
kubectl create -f dep-httpd-clusterip.yaml
```

Verify the deployment:

```bash
kubectl get pods
kubectl get deployment
```

#### 1.3 Create the ClusterIP Service

Now, create the service file `svc-httpd-clusterip.yaml` to expose the HTTPd deployment:

```bash
nano svc-httpd-clusterip.yaml
```

Add the following content:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: httpd-clusterip
  labels:
    run: httpd-clusterip
spec:
  ports:
  - port: 80
    protocol: TCP
  selector:
    app: my-httpd
```

#### 1.4 Apply the ClusterIP Service

Create the service:

```bash
kubectl create -f svc-httpd-clusterip.yaml
```

Verify the service:

```bash
kubectl get svc
kubectl describe svc httpd-clusterip
```

#### 1.5 Test the ClusterIP Service

You can test the service from within the cluster using a curl pod:

```bash
kubectl run -i --tty --rm testing --image=curlimages/curl --restart=Never -- curl http://httpd-clusterip
```

---

### Step 2: Deploying an Application with NodePort

#### 2.1 Create the Nginx Deployment YAML File

We will now deploy **nginx** with 3 replicas and expose it using a **NodePort** service.

Create the deployment file `dep-nginx-nodeport.yaml`:

```bash
nano ~/dep-nginx-nodeport.yaml
```

Add the following content:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: my-nginx
  name: my-nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-nginx
  template:
    metadata:
      labels:
        app: my-nginx
    spec:
      containers:
      - image: nginx:latest
        name: nginx
        ports:
        - containerPort: 80
```

#### 2.2 Apply the Deployment

Create the deployment:

```bash
kubectl apply -f ~/dep-nginx-nodeport.yaml
```

#### 2.3 Create the NodePort Service

Now create the service file `svc-nginx-nodeport.yaml`:

```bash
nano ~/svc-nginx-nodeport.yaml
```

Add the following content:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-nodeport
spec:
  selector:
    app: my-nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: NodePort
```

#### 2.4 Apply the NodePort Service

Create the service:

```bash
kubectl apply -f ~/svc-nginx-nodeport.yaml
```

Verify the service:

```bash
kubectl get svc
```

#### 2.5 Access the NodePort Service

To access the service, find the **InternalIP** of the node and the **NodePort**:

```bash
kubectl describe node $(kubectl get node | grep server-0 | awk '{print $1}') | grep InternalIP
kubectl get svc | grep nginx-nodeport | cut -d ':' -f 2 | cut -d '/' -f 1
```

Once you have the InternalIP and NodePort, you can access the nginx service:

```bash
curl http://<InternalIP>:<NodePort>
```

---

### Step 3: Deploying the Kubernetes Dashboard

#### 3.1 Clone the Dashboard Repository

To deploy the Kubernetes Dashboard, clone the **Kubernetes-WebUI** repository:

```bash
git clone https://github.com/arturafriansyah/Kubernetes-WebUI.git
```

#### 3.2 Apply the Dashboard Files

Navigate to the cloned directory and apply the configuration:

```bash
kubectl apply -f ~/Kubernetes-WebUI/.
```

Explanation of files:
- **kubernetes-dashboard-deployment.yml**: Deploys the Kubernetes dashboard.
- **service-account-admin.yml**: Creates a service account for accessing the dashboard.
- **rbac-clusterRoleBinding-admin.yml**: Binds the service account to the dashboard using RBAC permissions.
- **get-dash.sh**: A script to retrieve the dashboard URL and token credentials.

#### 3.3 Access the Dashboard

To access the dashboard, you’ll need to tunnel the connection. Run the tunneling command provided in the lab or use `kubectl proxy`.

Retrieve the access token by running the `get-dash.sh` script:

```bash
./get-dash.sh
```

Use the token to log in to the dashboard.

---

### Step 4: Deploying a Multi-Tier Application

#### 4.1 Deploy the Database

First, create the database deployment by downloading and applying the `rsvp-db.yaml` file:

```bash
wget https://raw.githubusercontent.com/academynusa/bta/main/rsvp-db.yaml
kubectl create -f rsvp-db.yaml
```

Verify the deployment:

```bash
kubectl get deployments
```

Next, create a service for the database:

```bash
nano rsvp-db-service.yaml
```

Add the following content:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: mongodb
  labels:
    app: rsvpdb
spec:
  ports:
  - port: 27017
    protocol: TCP
  selector:
    appdb: rsvpdb
```

Create the service:

```bash
kubectl create -f rsvp-db-service.yaml
```

Verify the service:

```bash
kubectl get svc
```

#### 4.2 Deploy the Web Application

Next, create the web application deployment:

```bash
wget https://raw.githubusercontent.com/academynusa/bta/main/rsvp-web.yaml
kubectl create -f rsvp-web.yaml
```

Verify the deployment:

```bash
kubectl get deployment
```

Create a NodePort service for the web application:

```bash
nano rsvp-web-service.yaml
```

Add the following content:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: rsvp
  labels:
    apps: rsvp
spec:
  type: NodePort
  ports:
  - port: 80
    targetPort: web-port
    nodePort: 30036
    protocol: TCP
  selector:
    app: rsvp
```

Create the service:

```bash
kubectl create -f rsvp-web-service.yaml
```

Verify the services:

```bash
kubectl get services
```

#### 4.3 Scale the Web Application

Scale the web application to 3 replicas:

```bash
kubectl scale --replicas=3 -f rsvp-web.yaml
kubectl get deployments
```

#### 4.4 Access the Web Application

To access the web application, retrieve the

 **InternalIP** and visit the following URL in your browser:

```bash
http://<InternalIP>:30036
```

You can find the **InternalIP** using:

```bash
kubectl describe nodes $(kubectl get nodes | grep server-0 | awk '{print $1}') | grep InternalIP
```

---

## Conclusion

In this lab, you successfully:
1. Deployed applications using **ClusterIP** and **NodePort** services.
2. Deployed and accessed the **Kubernetes Dashboard**.
3. Deployed a multi-tier application with a web front-end and database.

You now have the skills to expose applications within a Kubernetes cluster using services and manage your cluster using the Kubernetes Dashboard. Congratulations on completing **Lab 10.1**!