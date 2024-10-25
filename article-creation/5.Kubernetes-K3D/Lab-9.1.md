# Lab 9.1: Creating Persistent Volumes (PV) and Persistent Volume Claims (PVC), and Deploying a Pod with Persistent Storage

In this lab, we will explore the concepts of **Persistent Volumes (PV)** and **Persistent Volume Claims (PVC)** in Kubernetes, and we’ll learn how to create a pod that uses a persistent volume for storage. This is essential for applications that need to retain data even if the pod is restarted or re-deployed.

By the end of this lab, you will:
1. Create a **Persistent Volume** and a **Persistent Volume Claim**.
2. Deploy a pod that uses persistent storage.
3. Verify that the pod is able to access the data stored in the persistent volume.

---

## Table of Contents
1. **Introduction to PV, PVC, and Persistent Storage**
2. **Step-by-Step Instructions**
    1. Create a Persistent Volume (PV)
    2. Create a Persistent Volume Claim (PVC)
    3. Create a Pod that Uses the Persistent Volume
3. **Verification**
4. **Conclusion**

---

## Introduction to PV, PVC, and Persistent Storage

### What is a Persistent Volume (PV)?

A **Persistent Volume (PV)** is a piece of storage in the cluster that has been provisioned by an administrator. It represents physical storage on the node (like a disk), and it exists independently of any pod, which means it can persist data even if the pod using it is deleted or re-created.

### What is a Persistent Volume Claim (PVC)?

A **Persistent Volume Claim (PVC)** is a request for storage by a user. It allows you to define the required storage size and access modes. Once a claim is made, Kubernetes tries to find a matching Persistent Volume to fulfill the claim.

---

## Step-by-Step Instructions

### Step 1: Create a Persistent Volume (PV)

We will first create a **Persistent Volume (PV)** that will provide 1 GiB of storage.

#### 1.1 Create a Persistent Volume YAML File

Let’s create a file called `create-pv-lab.yaml`:

```bash
nano create-pv-lab.yaml
```

Add the following content to define the Persistent Volume:

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: example-volume
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/data/example"
```

Explanation:
- **capacity**: The amount of storage available (1 GiB).
- **accessModes**: `ReadWriteOnce` means the volume can be mounted as read-write by a single node.
- **hostPath**: Specifies the path on the host (node) where the data will be stored.

#### 1.2 Apply the Persistent Volume

To create the Persistent Volume, apply the YAML file:

```bash
kubectl create -f create-pv-lab.yaml
```

Verify that the PV has been created:

```bash
kubectl get pv
```

You should see `example-volume` listed as the Persistent Volume.

---

### Step 2: Create a Persistent Volume Claim (PVC)

Now, we will create a **Persistent Volume Claim (PVC)** to request storage from the Persistent Volume we just created.

#### 2.1 Create a Persistent Volume Claim YAML File

Create a file called `create-pvc-lab.yaml`:

```bash
nano create-pvc-lab.yaml
```

Add the following content to define the Persistent Volume Claim:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: example-claim-volume
spec:
  storageClassName: ""
  volumeName: example-volume
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

Explanation:
- **volumeName**: This binds the claim to the specific volume (`example-volume`).
- **resources**: The amount of storage requested is 1 GiB.

#### 2.2 Apply the Persistent Volume Claim

To create the PVC, apply the YAML file:

```bash
kubectl create -f create-pvc-lab.yaml
```

Verify the PVC has been created:

```bash
kubectl get pvc
```

You should see `example-claim-volume` listed as a Persistent Volume Claim.

---

### Step 3: Create a Pod with Persistent Storage

Next, we will create a pod that uses the **Persistent Volume** through the **Persistent Volume Claim**.

#### 3.1 Prepare the Host Directory

We need to set up a directory on the host (node) to simulate persistent data storage.

```bash
sudo mkdir /data/lab92
echo 'Hello World, I am learning Kubernetes!' | sudo tee /data/lab92/index.html
```

This creates a simple HTML file that will be served by the pod’s web server.

#### 3.2 Create a Persistent Volume for the Pod

Create a file called `lab92-pv.yaml`:

```bash
nano ~/lab92-pv.yaml
```

Add the following content:

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-lab92
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /data/lab92
```

Apply the Persistent Volume:

```bash
kubectl apply -f ~/lab92-pv.yaml
```

#### 3.3 Create a Persistent Volume Claim for the Pod

Next, create a file called `lab92-pvc.yaml`:

```bash
nano ~/lab92-pvc.yaml
```

Add the following content:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-lab92
spec:
  storageClassName: ""
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

Apply the PVC:

```bash
kubectl apply -f ~/lab92-pvc.yaml
```

#### 3.4 Create a Pod Using the PVC

Now, we will create a pod that mounts the persistent volume. Create a file called `lab92-pod.yaml`:

```bash
nano ~/lab92-pod.yaml
```

Add the following content:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-lab92
spec:
  containers:
  - name: my-website
    image: nginx:latest
    volumeMounts:
    - name: storage
      mountPath: /usr/share/nginx/html
  volumes:
  - name: storage
    persistentVolumeClaim:
      claimName: pvc-lab92
```

Explanation:
- **volumeMounts**: Mounts the persistent storage at `/usr/share/nginx/html` (the directory where nginx serves files).
- **persistentVolumeClaim**: This refers to the claim we created (`pvc-lab92`).

Apply the pod configuration:

```bash
kubectl apply -f ~/lab92-pod.yaml
```

---

## Verification

To ensure everything is working, let’s verify the resources.

### Step 4: Check the PV, PVC, and Pod Status

#### 4.1 Check the Persistent Volumes and Claims

Check the status of the Persistent Volume and Persistent Volume Claim:

```bash
kubectl get pv
kubectl get pvc
```

Ensure that both the PV and PVC are **bound**, meaning they are successfully linked and ready for use.

#### 4.2 Check the Pod

Check the pod to ensure it is running:

```bash
kubectl get pods
```

The pod should be listed as `Running`.

#### 4.3 Test the Pod

We can verify that the pod is correctly serving the file from the persistent volume by using `kubectl exec`:

```bash
kubectl exec -it pod-lab92 -- curl localhost
```

The output should be:

```bash
Hello World, I am learning Kubernetes!
```

This confirms that the pod is successfully using the data stored in the persistent volume.

---

## Conclusion

In this lab, you learned how to:
1. Create a **Persistent Volume (PV)** and **Persistent Volume Claim (PVC)** in Kubernetes.
2. Create a pod that uses persistent storage to ensure that data is retained even if the pod is restarted.
3. Verify the functionality of the persistent storage by serving a file from the volume.

This lab demonstrates how Kubernetes can manage persistent data, a critical feature for stateful applications. Congratulations on completing **Lab 9.1**! You are now familiar with using Persistent Volumes in Kubernetes.