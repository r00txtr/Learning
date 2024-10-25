# Lab 1.3 - Quiz 1: Deploying a Pod to Kubernetes

In this lab, you will deploy a pod to a Kubernetes cluster. The task is to create a pod with specific parameters, deploy it to a specified node, and verify its correct deployment.

### Pod Specifications:

- **Pod Name**: `nginx-kuis1`
- **Container Name**: `nginx-container`
- **Image**: `nginx:1.25`
- **Node for Deployment**: `k3d-k3s-default-agent-1`
- **Pod Labels**:
  - `app=webserver`
  - `lab=kuis01`

---

## Steps to Deploy the Pod

### 1. Create the Pod Definition File

First, create a YAML file to define the pod. You can name this file `nginx-kuis1-pod.yaml`.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-kuis1
  labels:
    app: webserver
    lab: kuis01
spec:
  containers:
  - name: nginx-container
    image: nginx:1.25
  nodeName: k3d-k3s-default-agent-1
```

- **apiVersion**: Specifies the API version. For a pod, we use `v1`.
- **kind**: Indicates the kind of Kubernetes object, in this case, a `Pod`.
- **metadata**: Contains the name and labels for the pod.
  - **name**: `nginx-kuis1`, the name of the pod.
  - **labels**: Adds labels `app=webserver` and `lab=kuis01`.
- **spec**: Contains the specification for the pod.
  - **containers**: Defines the container inside the pod.
    - **name**: `nginx-container`, the name of the container.
    - **image**: Specifies the image version `nginx:1.25`.
  - **nodeName**: Specifies the node `k3d-k3s-default-agent-1` where the pod will be deployed.

### 2. Deploy the Pod to the Kubernetes Cluster

Use the `kubectl apply` command to deploy the pod to the Kubernetes cluster.

```bash
kubectl apply -f nginx-kuis1-pod.yaml
```

This command tells Kubernetes to apply the configuration specified in the `nginx-kuis1-pod.yaml` file and deploy the pod.

### 3. Verify the Pod Deployment

After deploying the pod, check if it has been created successfully and is running on the specified node.

```bash
kubectl get pods -o wide
```

The output should display the pod `nginx-kuis1` with the assigned node as `k3d-k3s-default-agent-1`. The status should be **Running**, indicating the pod is successfully deployed.

### 4. Inspect the Pod Details

To view detailed information about the pod, including its labels and node, run the following command:

```bash
kubectl describe pod nginx-kuis1
```

This command provides details such as the pod’s labels, node, container image, and any events related to its deployment.

---

## Conclusion

In this lab, you have successfully created and deployed a pod to a specific node in the Kubernetes cluster. The pod was created using the following specifications:
- Pod Name: `nginx-kuis1`
- Container Name: `nginx-container`
- Image: `nginx:1.25`
- Node: `k3d-k3s-default-agent-1`
- Labels: `app=webserver`, `lab=kuis01`

By following these steps, you can verify the correct deployment and operation of the pod in the Kubernetes environment.