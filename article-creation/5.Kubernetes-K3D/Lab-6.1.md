# Lab 6.1: Working with Namespaces and Kubeconfig in Kubernetes

In this lab, we will learn how to manage **Namespaces** and **Kubeconfig** files in Kubernetes. Namespaces allow you to divide cluster resources between multiple users or groups. Kubeconfig files store configuration details used by `kubectl` to interact with Kubernetes clusters.

By the end of this lab, you will:
1. Create and manage namespaces in Kubernetes.
2. Set up and use different configurations with Kubeconfig.
3. Apply secrets to securely manage access to your Kubernetes cluster.

---

## Table of Contents
1. **Introduction to Namespaces and Kubeconfig**
2. **Step-by-Step Instructions**
    1. Create and Manage Namespaces
    2. Create Pods and Services in Different Namespaces
    3. Work with Kubeconfig and Secrets
3. **Verification**
4. **Conclusion**

---

## Introduction to Namespaces and Kubeconfig

### What are Namespaces?
Namespaces in Kubernetes allow you to organize and manage resources more effectively within a cluster. They provide a way to divide cluster resources between different projects, teams, or environments (like dev, staging, and production) and help avoid name collisions for resources like pods and services.

### What is Kubeconfig?
A Kubeconfig file is a YAML file that stores cluster access information such as cluster URLs, user credentials, and namespaces. It is used by `kubectl` to determine which cluster to interact with. By switching between different Kubeconfig files, you can manage multiple clusters or users with ease.

---

## Step-by-Step Instructions

### Step 1: Create and Manage Namespaces

#### 1.1 Create a New Namespace

Let’s start by creating a new namespace called **ops-namespace**:

```bash
kubectl create namespace ops-namespace
```

You can verify that the namespace was created by running:

```bash
kubectl get namespace
```

The output will show a list of all the namespaces in your cluster, including `ops-namespace`.

---

### Step 2: Create Pods and Services in Different Namespaces

#### 2.1 Create a Pod and Service in the ops-namespace

We will now create a pod and service inside the `ops-namespace`. First, create a YAML file called `pod-service-ops.yaml`:

```bash
nano pod-service-ops.yaml
```

Add the following content to define a pod and a service in the `ops-namespace`:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-ops
  namespace: ops-namespace
  labels:
    app: ops
spec:
  containers:
  - name: httpd
    image: httpd:latest
    ports:
      - containerPort: 80
        name: httpd-web-svc
---
apiVersion: v1
kind: Service
metadata:
  name: service-ops
  namespace: ops-namespace
spec:
  selector:
    app: ops
  ports:
  - protocol: TCP
    port: 80
    targetPort: httpd-web-svc
```

Explanation:
- **Pod**: The pod runs an HTTP server (`httpd`) with port 80 exposed.
- **Service**: The service routes traffic to the `httpd` container in the pod.

Create the pod and service using the following command:

```bash
kubectl create -f pod-service-ops.yaml
```

#### 2.2 Verify the Pod and Service

To check if the pod and service are running in the `ops-namespace`, use:

```bash
kubectl get pod -n ops-namespace
kubectl get service -n ops-namespace
```

#### 2.3 Create a Pod and Service in another Namespace

Let’s create another namespace called **academy-namespace**:

```bash
kubectl create namespace academy-namespace
```

Now, create a new YAML file called `pod-service-academy.yaml`:

```bash
nano pod-service-academy.yaml
```

Add the following content to define another pod and service in the `academy-namespace`:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-academy
  namespace: academy-namespace
  labels:
    app: academy
spec:
  containers:
  - name: nginx
    image: nginx:latest
    ports:
      - containerPort: 80
        name: nginx-web-svc
---
apiVersion: v1
kind: Service
metadata:
  name: service-academy
  namespace: academy-namespace
spec:
  selector:
    app: academy
  ports:
  - protocol: TCP
    port: 80
    targetPort: nginx-web-svc
```

Create the pod and service using:

```bash
kubectl create -f pod-service-academy.yaml
```

Verify the resources in the `academy-namespace`:

```bash
kubectl get pod -n academy-namespace
kubectl get service -n academy-namespace
```

---

### Step 3: Work with Kubeconfig and Secrets

In this section, we’ll set up a custom Kubeconfig and use it with a pod. 

#### 3.1 Copy the Kubeconfig File

First, create a directory called `lab62` and copy the Kubeconfig file to it:

```bash
mkdir lab62 && cd lab62
cp ~/.kube/config ~/lab62/config
```

#### 3.2 Create a Secret with the Kubeconfig

Next, we will create a Kubernetes secret to store the Kubeconfig file. Run the following command to create a secret:

```bash
kubectl apply -f - <<EOF
apiVersion: v1
kind: Secret
metadata:
  name: default-token
  annotations:
    kubernetes.io/service-account.name: default
type: kubernetes.io/service-account-token
EOF
```

#### 3.3 Set the API Server and Token

Retrieve the API server address and token:

```bash
APISERVER=$(kubectl config view --minify | grep server | cut -f 2- -d ":" | tr -d " ")
TOKEN=$(kubectl describe secret default-token | grep -E '^token' | cut -f2 -d':' | tr -d " ")
IPSERVER=$(curl -s $APISERVER/api --header "Authorization: Bearer $TOKEN" --insecure | jq -r .serverAddressByClientCIDRs[].serverAddress)
```

Edit the Kubeconfig file to update the API server URL:

```bash
sed -i "s#https://.*#https://$IPSERVER#g" ~/lab62/config
```

Now, create a Kubernetes secret to store the Kubeconfig file:

```bash
kubectl create secret generic kubeconfig-secret --from-file=kubeconfig=/home/student/lab62/config
```

#### 3.4 Create a Pod with Kubeconfig

We will now create a pod that uses the Kubeconfig file. Create a YAML file named `pod.yaml`:

```bash
nano ~/lab62/pod.yaml
```

Add the following content:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-kubeconfig
spec:
  containers:
  - name: my-container
    image: bitnami/kubectl
    command: ["sleep", "3600"]
    env:
      - name: KUBECONFIG
        value: /kube/config/kubeconfig
    volumeMounts:
      - name: kubeconfig-volume
        mountPath: /kube/config
  volumes:
  - name: kubeconfig-volume
    secret:
      secretName: kubeconfig-secret
```

Apply the YAML file:

```bash
kubectl apply -f ~/lab62/pod.yaml
```

#### 3.5 Verify the Pod and Secret

To verify the pod is running, use:

```bash
kubectl get pod
```

Check if the secret is created:

```bash
kubectl get secret
```

You can also interact with the pod by executing a shell inside it:

```bash
kubectl exec -it pod-with-kubeconfig -- bash
```

---

## Verification

To verify your progress, check that:
1. **Namespaces**: You have successfully created and listed namespaces.
2. **Pods and Services**: You have created and accessed pods and services in both `ops-namespace` and `academy-namespace`.
3. **Kubeconfig**: You created a custom Kubeconfig file, stored it as a secret, and accessed it from a running pod.

---

## Conclusion

In this lab, you learned how to:
1. Work with **namespaces** in Kubernetes to organize resources.
2. Create **pods and services** in different namespaces.
3. Manage **Kubeconfig** files and securely access them using **secrets**.

This knowledge will help you manage Kubernetes resources more effectively, especially in multi-team or multi-project environments. Congratulations on completing **Lab 6.1**!