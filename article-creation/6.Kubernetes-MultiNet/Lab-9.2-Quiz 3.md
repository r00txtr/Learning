# Lab 9.3: Quiz 3 - Creating Persistent Volumes, PVCs, and Pods in Kubernetes

In this lab, we will create a **Persistent Volume (PV)**, a **Persistent Volume Claim (PVC)**, and a **pod** that uses the PVC for persistent storage. By the end of this lab, we will verify that the pod can serve the contents of a file stored in the persistent volume.

We will:
1. Create an `index.html` file with a message.
2. Create a **Persistent Volume (PV)**.
3. Create a **Persistent Volume Claim (PVC)**.
4. Create a **pod** using the **nginx** image and the PVC.
5. Verify that the pod is serving the correct content.

---

## Table of Contents
1. **Introduction to PV, PVC, and Pods**
2. **Step-by-Step Instructions**
    1. Create the `index.html` File
    2. Create the Persistent Volume (PV)
    3. Create the Persistent Volume Claim (PVC)
    4. Create a Pod that Uses the PVC
    5. Verify the Setup
3. **Conclusion**

---

## Introduction to PV, PVC, and Pods

In Kubernetes, **Persistent Volumes (PV)** provide storage that exists independently of pods, meaning data is retained even if a pod is deleted or recreated. **Persistent Volume Claims (PVC)** are how users request storage from the available Persistent Volumes. A **pod** can mount a PVC to store and serve files.

In this lab, we will create a pod running an **nginx** container that serves a custom `index.html` file from a persistent volume.

---

## Step-by-Step Instructions

### Step 1: Create the `index.html` File

First, let’s create a simple `index.html` file that will be served by the **nginx** pod.

#### 1.1 Prepare the Directory and File

Create the directory `/data/quiz-1` on your host machine and add the `index.html` file with the following content:

```bash
sudo mkdir -p /data/quiz-1
echo 'Halo Dunia dan Saya sedang belajar k8s' | sudo tee /data/quiz-1/index.html
```

Explanation:
- This creates the `/data/quiz-1` directory.
- The `echo` command writes the message **"Halo Dunia dan Saya sedang belajar k8s"** into the `index.html` file.

---

### Step 2: Create the Persistent Volume (PV)

Next, we will create a **Persistent Volume (PV)** to provide storage for our pod.

#### 2.1 Create the Persistent Volume YAML File

Create a file called `quiz-1-pv.yaml`:

```bash
nano quiz-1-pv.yaml
```

Add the following content to define the **Persistent Volume**:

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: quiz-1-pv
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /data/quiz-1
```

Explanation:
- **capacity**: The volume has 1Gi of storage.
- **accessModes**: `ReadWriteOnce` means the volume can be mounted as read-write by a single node.
- **hostPath**: Specifies the host directory (`/data/quiz-1`) where the data will be stored.

#### 2.2 Apply the Persistent Volume

To create the **Persistent Volume**, run the following command:

```bash
kubectl apply -f quiz-1-pv.yaml
```

Verify the Persistent Volume has been created:

```bash
kubectl get pv
```

---

### Step 3: Create the Persistent Volume Claim (PVC)

Now, we will create a **Persistent Volume Claim (PVC)** to request storage from the Persistent Volume we just created.

#### 3.1 Create the Persistent Volume Claim YAML File

Create a file called `quiz-1-pvc.yaml`:

```bash
nano quiz-1-pvc.yaml
```

Add the following content to define the **Persistent Volume Claim**:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: quiz-1-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

Explanation:
- **accessModes**: The PVC requests `ReadWriteOnce` access.
- **storage**: The PVC requests 1Gi of storage.

#### 3.2 Apply the Persistent Volume Claim

To create the PVC, run the following command:

```bash
kubectl apply -f quiz-1-pvc.yaml
```

Verify the PVC has been created and bound to the PV:

```bash
kubectl get pvc
```

---

### Step 4: Create a Pod that Uses the PVC

We will now create a pod that uses the **nginx** image and mounts the **Persistent Volume Claim** at the path `/usr/share/nginx/html` (where nginx serves files).

#### 4.1 Create the Pod YAML File

Create a file called `nginx-quiz-1-pod.yaml`:

```bash
nano nginx-quiz-1-pod.yaml
```

Add the following content:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-quiz-1
spec:
  containers:
  - name: nginx
    image: nginx:mainline-alpine
    volumeMounts:
    - name: storage
      mountPath: /usr/share/nginx/html
  volumes:
  - name: storage
    persistentVolumeClaim:
      claimName: quiz-1-pvc
```

Explanation:
- **image: nginx:mainline-alpine**: The pod uses the latest mainline version of the nginx container.
- **mountPath: /usr/share/nginx/html**: The PVC is mounted at this path, which is where nginx serves static files.
- **persistentVolumeClaim**: The pod uses the **quiz-1-pvc** for its storage.

#### 4.2 Apply the Pod Configuration

Create the pod by applying the YAML file:

```bash
kubectl apply -f nginx-quiz-1-pod.yaml
```

Verify the pod is running:

```bash
kubectl get pods
```

---

### Step 5: Verify the Setup

#### 5.1 Test the Pod

Now that the pod is running, we can verify that nginx is serving the custom `index.html` file from the persistent volume.

Use `kubectl exec` to access the pod and run the `curl` command:

```bash
kubectl exec -it nginx-quiz-1 -- curl localhost
```

The output should display:

```bash
Halo Dunia dan Saya sedang belajar k8s
```

This confirms that the pod is correctly serving the content from the persistent volume.

---

## Conclusion

In this lab, you successfully created:
1. A **Persistent Volume (PV)** that stores data on the host.
2. A **Persistent Volume Claim (PVC)** to request storage from the PV.
3. A **pod** that uses the PVC to serve content using the **nginx** container.

By using **Persistent Volumes** and **Persistent Volume Claims**, you learned how to persist data in Kubernetes, ensuring that your application’s data remains available even if pods are restarted or deleted. Congratulations on completing **Lab 9.3**!