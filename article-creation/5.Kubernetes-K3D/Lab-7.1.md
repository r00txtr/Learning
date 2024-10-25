# Lab 7.1: Using DaemonSets, ReplicaSets, and Configuring Role-Based Access Control (RBAC) in Kubernetes

In this lab, we will explore **DaemonSets** and **ReplicaSets**, two critical Kubernetes controllers for managing workloads, and we’ll also configure **Role-Based Access Control (RBAC)** to manage permissions in the cluster. This lab provides a step-by-step guide to help you understand and apply these concepts.

By the end of this lab, you will:
1. Create and manage DaemonSets and ReplicaSets.
2. Configure access control using RBAC in Kubernetes.

---

## Table of Contents
1. **Introduction to DaemonSets, ReplicaSets, and RBAC**
2. **Step-by-Step Instructions**
    1. Creating a DaemonSet
    2. Creating a ReplicaSet
    3. Configuring RBAC
3. **Verification**
4. **Conclusion**

---

## Introduction to DaemonSets, ReplicaSets, and RBAC

### What is a DaemonSet?
A **DaemonSet** ensures that a copy of a specific pod runs on all (or selected) nodes in the Kubernetes cluster. They are typically used for deploying system-level workloads, such as monitoring agents or logging services, across all nodes.

### What is a ReplicaSet?
A **ReplicaSet** ensures that a specified number of pod replicas are running at any given time. It’s useful for maintaining the desired state of an application, providing high availability, and ensuring that if one pod fails, another is started automatically.

### What is Role-Based Access Control (RBAC)?
**RBAC** allows you to control who can access and perform actions on Kubernetes resources. By using **Roles**, **ClusterRoles**, and **RoleBindings**, you can define specific permissions for users or service accounts.

---

## Step-by-Step Instructions

### Step 1: Creating a DaemonSet

#### 1.1 Create a DaemonSet YAML File

We’ll create a **DaemonSet** that deploys the **nginx** container on all nodes in the cluster.

Create a file called `ds-nginx.yaml`:

```bash
nano ds-nginx.yaml
```

Add the following content to the file:

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: nginx-ds
  labels:
    k8s-app: DaemonSets-nginx
spec:
  selector:
    matchLabels:
      k8s-app: DaemonSets-nginx
  template:
    metadata:
      labels:
        k8s-app: DaemonSets-nginx
    spec:
      containers:
      - name: nginx
        image: nginx
        resources:
          limits:
            memory: 200Mi
          requests:
            memory: 200Mi
```

Explanation:
- **DaemonSet**: Ensures that each node runs an **nginx** container.
- **Resources**: We’ve set memory limits and requests for the container to 200Mi.

#### 1.2 Apply the DaemonSet

To create the DaemonSet, run:

```bash
kubectl create -f ds-nginx.yaml
```

Verify the DaemonSet is running:

```bash
kubectl get ds
```

To see the nodes that are running the DaemonSet:

```bash
kubectl get nodes
```

To get more details about the DaemonSet:

```bash
kubectl describe ds nginx-ds
```

---

### Step 2: Creating a ReplicaSet

#### 2.1 Create a ReplicaSet YAML File

Next, we’ll create a **ReplicaSet** that deploys three **httpd** containers.

Create a file called `rs-httpd.yaml`:

```bash
nano rs-httpd.yaml
```

Add the following content:

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: httpd-rs
  labels:
    app: Replicahttpd
spec:
  replicas: 3
  selector:
    matchLabels:
      app: Replicahttpd
  template:
    metadata:
      labels:
        app: Replicahttpd
    spec:
      containers:
      - name: httpd
        image: httpd:2.2.34
        ports:
        - containerPort: 80
```

Explanation:
- **ReplicaSet**: Ensures three replicas of the **httpd** container are running.
- **replicas**: We specify `3` replicas.
- **httpd:2.2.34**: The container image for Apache HTTP server.

#### 2.2 Apply the ReplicaSet

Create the ReplicaSet by running:

```bash
kubectl create -f rs-httpd.yaml
```

Check the ReplicaSet and its pods:

```bash
kubectl get rs
kubectl get pods
```

For more details about the ReplicaSet:

```bash
kubectl describe rs httpd-rs
```

---

### Step 3: Configuring RBAC

#### 3.1 Create a Service Account

We’ll create a new service account called `lab7-user` to manage access control.

```bash
kubectl create sa lab7-user
```

#### 3.2 Create a ClusterRole for Deployment and DaemonSet Creation

We’ll define a **ClusterRole** that allows the user to create **Deployments**, **ReplicaSets**, and **DaemonSets**.

Create a file called `~/lab73-rbac.yaml`:

```bash
vim ~/lab73-rbac.yaml
```

Add the following content:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: role-deployment
rules:
- apiGroups:
  - apps
  resources:
  - deployments
  - replicasets
  - daemonsets
  verbs:
  - create
```

Apply the ClusterRole:

```bash
kubectl apply -f ~/lab73-rbac.yaml
```

#### 3.3 Bind the ClusterRole to the Service Account

Now, we will bind the **ClusterRole** to the `lab7-user` service account using a **RoleBinding**.

Create a file called `~/lab73-rolebinding.yaml`:

```bash
vim ~/lab73-rolebinding.yaml
```

Add the following content:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: rolebinding-lab7-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: role-deployment
subjects:
- kind: ServiceAccount
  name: lab7-user
  namespace: default
```

Apply the RoleBinding:

```bash
kubectl apply -f ~/lab73-rolebinding.yaml
```

#### 3.4 Verify RBAC Permissions

You can check whether the `lab7-user` service account has the permissions to create resources like **Deployments**, **ReplicaSets**, and **DaemonSets** by running:

```bash
kubectl auth can-i create deployment --as system:serviceaccount:default:lab7-user
kubectl auth can-i create replicasets --as system:serviceaccount:default:lab7-user
kubectl auth can-i create daemonset --as system:serviceaccount:default:lab7-user
```

To check whether the user can delete these resources:

```bash
kubectl auth can-i delete deployment --as system:serviceaccount:default:lab7-user
kubectl auth can-i delete replicasets --as system:serviceaccount:default:lab7-user
kubectl auth can-i delete daemonset --as system:serviceaccount:default:lab7-user
```

---

## Verification

To ensure the lab was completed successfully, verify the following:
1. **DaemonSets**: The `nginx-ds` DaemonSet is running on all nodes.
2. **ReplicaSets**: The `httpd-rs` ReplicaSet is running three replicas of the `httpd` container.
3. **RBAC**: The `lab7-user` service account can create and manage deployments, ReplicaSets, and DaemonSets.

---

## Conclusion

In this lab, you learned how to:
1. Create and manage **DaemonSets** to run system-level workloads across all nodes.
2. Use **ReplicaSets** to maintain a specific number of pod replicas.
3. Configure **RBAC** in Kubernetes, giving specific permissions to users or service accounts.

By understanding DaemonSets, ReplicaSets, and RBAC, you can effectively manage workloads and control access in a Kubernetes cluster. Congratulations on completing **Lab 7.1**!