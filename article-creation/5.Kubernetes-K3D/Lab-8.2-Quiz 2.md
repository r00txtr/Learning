# Lab 8.2: Quiz 2 - Creating a Deployment with 3 Replicas in Kubernetes

In this lab, we will help a user create a **Kubernetes Deployment** for a personal website using the **nginx** image. The user wants to ensure high availability by configuring the deployment with **3 replicas**. This means that Kubernetes will run 3 instances (or pods) of the website to ensure that even if one or more pods fail, the website remains available.

By the end of this lab, you will:
1. Create a Kubernetes deployment named **personal-website**.
2. Use the **nginx** image to serve the website.
3. Ensure the deployment has **3 replicas** to provide high availability.

---

## Table of Contents
1. **Introduction to Kubernetes Deployments and Replicas**
2. **Step-by-Step Instructions**
    1. Create the Deployment YAML File
    2. Apply the Deployment
    3. Verify the Deployment
3. **Conclusion**

---

## Introduction to Kubernetes Deployments and Replicas

### What is a Deployment?

A **Deployment** in Kubernetes helps you manage a set of identical pods running your application. Deployments are used for scaling, updating, and managing the desired state of your applications. With deployments, you can ensure your application is always running as expected, even if some pods fail.

### What are Replicas?

Replicas ensure that a specific number of pods are running at all times. In this case, we will configure the deployment to maintain **3 replicas** of the website, meaning there will always be 3 instances of the website running in the cluster. If one of the pods goes down, Kubernetes will automatically start a new pod to maintain the desired number of replicas.

---

## Step-by-Step Instructions

### Step 1: Create the Deployment YAML File

To create a deployment that meets the user’s requirements, we need to define it in a YAML configuration file.

#### 1.1 Create the Deployment YAML

Let’s create a new file called `personal-website-deployment.yaml`:

```bash
nano personal-website-deployment.yaml
```

In the file, we will define the deployment using the **nginx:latest** image and set the **replicas** to 3.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: personal-website
spec:
  replicas: 3
  selector:
    matchLabels:
      app: personal-website
  template:
    metadata:
      labels:
        app: personal-website
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
```

Explanation:
- **replicas: 3**: This specifies that we want 3 replicas (pods) of the `nginx` container.
- **nginx:latest**: The image used to serve the website is the latest version of nginx.
- **containerPort: 80**: This tells Kubernetes to expose port 80 from the nginx container, which is the default HTTP port.

---

### Step 2: Apply the Deployment

#### 2.1 Apply the Deployment to the Cluster

Once the YAML file is ready, we can create the deployment by applying it to the Kubernetes cluster:

```bash
kubectl apply -f personal-website-deployment.yaml
```

This command will instruct Kubernetes to create the deployment as specified in the YAML file.

---

### Step 3: Verify the Deployment

#### 3.1 Check the Deployment

To verify that the deployment is running and the desired number of replicas is being maintained, you can run the following command:

```bash
kubectl get deployment personal-website
```

The output should look something like this:

```bash
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
personal-website   3/3     3            3           2m
```

- **READY 3/3**: This indicates that all 3 replicas (pods) are running successfully.

#### 3.2 Check the Pods

You can also check the individual pods that are part of the deployment:

```bash
kubectl get pods
```

You should see 3 pods with unique names, each running the `nginx` container.

#### 3.3 Describe the Deployment

For more detailed information about the deployment, including its status and events, use the following command:

```bash
kubectl describe deployment personal-website
```

This command provides a detailed description of the deployment, including information about the replicas, image, and any issues or events.

---

## Conclusion

In this lab, you successfully created a **Kubernetes Deployment** for a personal website using the **nginx** image with **3 replicas**. This ensures that the website is highly available, even if one or more pods fail.

Here’s what you accomplished:
1. Created a deployment for a website using the **nginx** image.
2. Configured the deployment with **3 replicas** to ensure high availability.
3. Verified that the deployment and its pods are running correctly.

This simple deployment is a critical first step in understanding how to ensure high availability in Kubernetes. Congratulations on completing **Lab 8.2**!