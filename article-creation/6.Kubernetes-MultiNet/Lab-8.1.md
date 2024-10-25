# Lab 8.1: Simple Kubernetes Deployment, Scaling, Editing, Updating & Rolling Back Deployments

In this lab, we will cover the essentials of working with **Kubernetes Deployments**, which manage the desired state of application workloads. Deployments handle updates, scaling, and provide the ability to roll back to previous versions in case of failure.

By the end of this lab, you will:
1. Create a simple deployment.
2. Scale and edit a deployment.
3. Update a deployment and manage rollbacks.

This guide is designed for beginners, with examples and easy-to-follow instructions.

---

## Table of Contents
1. **Introduction to Kubernetes Deployments**
2. **Step-by-Step Instructions**
    1. Create a Simple Deployment
    2. Scaling a Deployment
    3. Editing a Deployment
    4. Updating and Rolling Back a Deployment
3. **Verification**
4. **Conclusion**

---

## Introduction to Kubernetes Deployments

### What is a Deployment?

A **Deployment** in Kubernetes provides declarative updates for pods and replica sets. With deployments, you can define how your application should run, how many replicas it should have, and how updates are applied. Deployments are an essential part of managing rolling updates and rollbacks in Kubernetes.

---

## Step-by-Step Instructions

### Step 1: Create a Simple Deployment

We will start by creating a simple deployment with **nginx**.

#### 1.1 Create a Deployment YAML File

Let’s create a YAML file named `simple-deployment.yaml` that defines a deployment with a single replica of an `nginx` container.

```bash
nano ~/simple-deployment.yaml
```

Add the following content:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: simple-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: simple-deployment
  template:
    metadata:
      labels:
        app: simple-deployment
    spec:
      containers:
      - name: simple-container
        image: nginx:latest
```

Explanation:
- **replicas**: Specifies the number of pod replicas (1 in this case).
- **nginx:latest**: The container image used by the deployment.

#### 1.2 Apply the Deployment

Create the deployment using:

```bash
kubectl apply -f ~/simple-deployment.yaml
```

Verify that the deployment and its pods are running:

```bash
kubectl get deployment
kubectl get pods
```

You can retrieve more detailed information about the deployment:

```bash
kubectl get deployment simple-deployment -o yaml
kubectl get deployment simple-deployment -o json
```

---

### Step 2: Scaling a Deployment

Next, we will scale the deployment to increase the number of replicas.

#### 2.1 Create a Deployment Using `kubectl`

Alternatively, you can generate a deployment configuration using `kubectl` and output the YAML:

```bash
kubectl create deployment simple-nginx --image=nginx:1.20.2 --dry-run=client --output=yaml > simple-deploy.yaml
kubectl create -f simple-deploy.yaml
```

#### 2.2 Scale the Deployment

To scale the `simple-nginx` deployment to 3 replicas, run:

```bash
kubectl scale deployment simple-nginx --replicas=3
```

Check the updated deployment status:

```bash
kubectl get deployment
kubectl get rs
kubectl get pods
```

#### 2.3 Describe the Deployment

You can also inspect the deployment details:

```bash
kubectl describe deployment simple-nginx
```

---

### Step 3: Editing a Deployment

We can modify a running deployment by editing the deployment file directly.

#### 3.1 Edit the Deployment

Let’s change the image version of the `nginx` container in the `simple-nginx` deployment to `nginx:1.22.0-alpine`.

Run the following command to edit the deployment:

```bash
kubectl edit deployment simple-nginx
```

Alternatively, you can use `nano` as the editor:

```bash
KUBE_EDITOR=nano kubectl edit deployment simple-nginx
```

Change the image in the spec to:

```yaml
    spec:
      containers:
      - image: nginx:1.22.0-alpine
        imagePullPolicy: IfNotPresent
        name: nginx
```

After saving, verify that the image has been updated:

```bash
kubectl describe deployment simple-nginx | grep Image:
```

---

### Step 4: Updating and Rolling Back a Deployment

#### 4.1 Create a New Deployment for Updates

We’ll now create a deployment with the name `update-nginx` and define it in a YAML file:

```bash
nano nginx-update.yaml
```

Add the following content:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: update-nginx
  labels:
    app: nginx
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.20.2
        ports:
        - containerPort: 80
```

Apply the deployment:

```bash
kubectl create -f nginx-update.yaml
```

#### 4.2 Update the Deployment

Now let’s update the deployment to use a newer version of the `nginx` image:

```bash
kubectl set image deployment update-nginx nginx=nginx:1.23.4-alpine
```

Annotate the deployment to track the change:

```bash
kubectl annotate deployment update-nginx kubernetes.io/change-cause="update nginx image from 1.20.2 to 1.23.4-alpine" --overwrite=true
```

Verify the updated deployment and pods:

```bash
kubectl get deployment
kubectl get pods
kubectl describe deployment update-nginx | grep Image:
```

#### 4.3 View Deployment History

Kubernetes keeps track of changes to deployments, allowing you to review past versions. To view the revision history:

```bash
kubectl rollout history deployment update-nginx
kubectl rollout history deployment update-nginx --revision=1
kubectl rollout history deployment update-nginx --revision=2
```

#### 4.4 Rollback the Deployment

If you need to roll back to a previous revision, you can do so with:

```bash
kubectl rollout undo deployment update-nginx --to-revision=1
```

Verify the rollback:

```bash
kubectl describe deployment update-nginx | grep Image:
```

---

## Verification

To ensure the lab was completed successfully:
1. **Simple Deployment**: You should have created a deployment and verified that the pods are running.
2. **Scaling**: You should have scaled the deployment to 3 replicas.
3. **Editing**: The image in the deployment should be updated to `nginx:1.22.0-alpine`.
4. **Update and Rollback**: You should be able to update the deployment and rollback to a previous version using the deployment history.

---

## Conclusion

In this lab, you learned how to:
1. Create and manage a **Kubernetes Deployment**.
2. Scale and edit a deployment in real-time.
3. Update a deployment with a new image and manage rollbacks using **Kubernetes Deployment History**.

Understanding Kubernetes Deployments is essential for managing application workloads, handling updates, and ensuring high availability. Congratulations on completing **Lab 8.1**! You’re now ready to use Kubernetes Deployments in real-world applications.