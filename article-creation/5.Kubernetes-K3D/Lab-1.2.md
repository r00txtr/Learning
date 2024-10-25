# Lab 1.2: Creating, Editing, and Deleting Pods, and Using Labels in Kubernetes

In this lab, we will walk through the basics of working with **Pods** in Kubernetes, which are the smallest deployable units in Kubernetes. We will cover how to create a simple pod, edit an existing pod, delete a pod, and use labels to categorize and filter pods.

By the end of this lab, you will:
1. Create a simple pod from a YAML file.
2. Edit a pod to update its configuration.
3. Delete a pod using different methods.
4. Create pods with labels and use those labels to filter and manage pods.

Let’s dive in!

---

## Table of Contents
1. **Introduction to Pods**
2. **Step-by-Step Instructions**
    1. Create a Simple Pod
    2. Edit an Existing Pod
    3. Delete a Pod
    4. Create Pods with Labels
    5. Filter Pods Using Labels
3. **Verification**
4. **Conclusion**

---

## Introduction to Pods

A **Pod** in Kubernetes is a group of one or more containers, with shared storage/network, and a specification for how to run the containers. In most cases, each pod runs a single container, but in some scenarios, you might want a pod to run multiple containers. Pods are the basic building block in Kubernetes, and learning how to create, edit, delete, and label them is an essential skill.

---

## Step-by-Step Instructions

### Step 1: Create a Simple Pod

We will start by creating a simple pod using a YAML configuration file.

#### 1.1 Create a Pod YAML File

Let’s create a YAML file named `simple-pods.yaml` to define our pod:

```bash
nano simple-pods.yaml
```

Add the following content to the file:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: simple-pod
spec:
  containers:
  - name: simple-pod
    image: nginx:1.20.2
    ports:
    - containerPort: 80
```

Explanation:
- **apiVersion**: The Kubernetes API version to use (v1 for core resources like Pods).
- **kind**: The type of Kubernetes object (Pod).
- **metadata**: Includes information like the pod’s name (`simple-pod`).
- **spec**: The pod’s specification, which includes the containers inside it. In this case, we have a single container using the `nginx:1.20.2` image and exposing port 80.

#### 1.2 Create the Pod

Use the following command to create the pod from the YAML file:

```bash
kubectl create -f simple-pods.yaml
```

Verify that the pod is running:

```bash
kubectl get pods
```

You should see `simple-pod` listed in the output. You can also retrieve detailed information about the pod:

```bash
kubectl get pod simple-pod -o yaml
kubectl get pod simple-pod -o json
```

---

### Step 2: Edit an Existing Pod

Next, let’s edit the pod to change the container’s image from `nginx:1.20.2` to `httpd:latest` (Apache).

#### 2.1 Edit the Pod

Use the `kubectl edit` command to modify the pod configuration:

```bash
kubectl edit pod simple-pod
```

The editor will open the YAML definition of the pod. Look for the **image** field under the **spec** section and change the value from `nginx:1.20.2` to `httpd:latest`:

```yaml
spec:
  containers:
  - image: httpd:latest
    imagePullPolicy: IfNotPresent
    name: simple-pod
    ports:
    - containerPort: 80
```

Save and close the file to apply the changes.

#### 2.2 Verify the Changes

You can check the logs of the pod to see if the new container is running:

```bash
kubectl logs simple-pod
```

Additionally, you can execute a command inside the pod to inspect the web server’s default page:

```bash
kubectl exec -it simple-pod -- cat htdocs/index.html
```

---

### Step 3: Delete a Pod

There are multiple ways to delete a pod. Let’s go through them.

#### 3.1 Delete the Pod Using `kubectl delete pod`

To delete the pod directly by its name:

```bash
kubectl delete pod simple-pod
```

#### 3.2 Delete the Pod Using the YAML File

You can also delete the pod by referencing the YAML file you used to create it:

```bash
kubectl delete -f simple-pods.yaml
```

---

### Step 4: Create Pods with Labels

Labels are key-value pairs attached to Kubernetes objects, including pods. Labels can be used to organize and select subsets of objects.

#### 4.1 Create a YAML File with Multiple Pods and Labels

Create a file named `label-pod.yaml`:

```bash
vim ~/label-pod.yaml
```

Add the following content:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod-1
  labels:
    app: web
    environment: inter
spec:
  containers:
  - name: nginx-container
    image: nginx:latest
---
apiVersion: v1
kind: Pod
metadata:
  name: apache-pod-2
  labels:
    app: web
    environment: local
spec:
  containers:
  - name: apache-container
    image: httpd:latest
---
apiVersion: v1
kind: Pod
metadata:
  name: db-pod-1
  labels:
    app: database
    environment: inter
spec:
  containers:
  - name: db-container
    image: aa8y/postgres-dataset:latest
```

Explanation:
- We define three pods: `nginx-pod-1`, `apache-pod-2`, and `db-pod-1`.
- Each pod has labels: `app` and `environment`. These labels help categorize the pods (e.g., for selecting based on environment: `inter` or `local`).

#### 4.2 Apply the YAML File

Create the pods by applying the YAML file:

```bash
kubectl apply -f ~/label-pod.yaml
```

Check the pods and their labels:

```bash
kubectl get pods --show-labels
```

---

### Step 5: Filter Pods Using Labels

You can filter pods based on their labels. For example, to list only the pods that are running in the `inter` environment:

```bash
kubectl get pods -l environment=inter
```

You can also change the label of an existing pod. For example, to change the `nginx-pod-1` environment label from `inter` to `local`:

```bash
kubectl label pod nginx-pod-1 environment=local --overwrite
```

Then, list the pods in the `local` environment:

```bash
kubectl get pods -l environment=local
```

---

## Verification

To ensure that the lab was completed successfully, verify the following:
1. **Simple Pod**: The pod should be created, edited, and deleted successfully.
2. **Labeled Pods**: You should be able to filter and modify pods based on their labels.

---

## Conclusion

In this lab, you learned how to:
1. Create a simple pod using YAML.
2. Edit an existing pod and update its image.
3. Delete a pod using `kubectl`.
4. Create pods with labels and use labels to filter and manage pods.

Understanding how to manage pods and work with labels is essential for effective Kubernetes management. Congratulations on completing **Lab 1.2**! Now you can confidently create, edit, delete, and label Kubernetes pods for your future projects.