# Lab 1.1: Configuring a Kubernetes Cluster Using K3D

In this lab, we will learn how to configure a Kubernetes cluster using **K3D**, a lightweight wrapper around **K3s**, designed for running Kubernetes clusters in Docker. K3D simplifies setting up Kubernetes clusters on your local machine, making it an excellent tool for development, testing, and learning purposes.

By the end of this lab, you will:
1. Set up Docker on your machine.
2. Install K3D to create a Kubernetes cluster.
3. Use `kubectl` to interact with the cluster.
4. Verify the setup by checking cluster nodes and pods.

This guide is written in a beginner-friendly tone, with examples and clear explanations to help you understand each step of the process.

---

## Table of Contents
1. **Introduction to K3D**
2. **Step-by-Step Instructions**
    1. Install Docker on Ubuntu
    2. Install K3D
    3. Create a Kubernetes Cluster
    4. Install and Configure `kubectl`
3. **Verification**
4. **Conclusion**

---

## Introduction to K3D

K3D is a lightweight tool that runs Kubernetes clusters inside Docker. It is perfect for developers who want a quick and easy way to spin up Kubernetes clusters locally without consuming too many system resources. By using K3D, you can have a Kubernetes cluster up and running in just a few minutes.

---

## Step-by-Step Instructions

### Step 1: Install Docker on Ubuntu

Before installing K3D, we need to set up Docker, as K3D uses Docker containers to run Kubernetes clusters.

#### 1.1 Add Docker's GPG Key and Repository

First, we need to add Docker’s GPG key and set up its official repository:

```bash
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
```

Next, add Docker’s official repository:

```bash
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

#### 1.2 Install Docker

Now, update your package index and install Docker and its related components:

```bash
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin docker-compose -y
```

Once installed, give the current user permission to manage Docker:

```bash
sudo chown $USER /var/run/docker.sock
sudo usermod -aG docker $USER
```

Restart Docker to apply these changes:

```bash
sudo systemctl restart docker
```

Docker is now set up and ready to be used. You can verify it by running:

```bash
docker --version
```

---

### Step 2: Install K3D

Now that Docker is installed, we can move on to installing K3D.

#### 2.1 Download and Install K3D

To install K3D, run the following command to download and execute the installation script:

```bash
wget -q -O - https://raw.githubusercontent.com/k3d-io/k3d/main/install.sh | bash
```

After the installation is complete, you can verify the installation by checking the K3D version:

```bash
k3d version
```

---

### Step 3: Create a Kubernetes Cluster

With K3D installed, you are ready to create your Kubernetes cluster.

#### 3.1 Create a Cluster with K3D

We will create a Kubernetes cluster with **1 server** and **3 agent nodes**. We will also mount a local directory (`/data`) as a persistent volume inside the cluster:

```bash
sudo mkdir /data
k3d cluster create -s 1 -a 3 --volume /data:/data
```

Explanation:
- **`-s 1`**: Creates one server node (the control plane).
- **`-a 3`**: Adds three agent (worker) nodes.
- **`--volume /data:/data`**: Mounts the local `/data` directory into the cluster.

Once the cluster is created, K3D will automatically configure your `kubectl` to point to this new cluster.

---

### Step 4: Install and Configure `kubectl`

`kubectl` is the command-line tool used to interact with Kubernetes clusters. We need to install and configure `kubectl` to manage the K3D cluster.

#### 4.1 Download and Install `kubectl`

Download the latest stable version of `kubectl`:

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
```

Make it executable and move it to your system's PATH:

```bash
chmod +x kubectl
sudo mv kubectl /usr/local/bin
```

Verify the installation by checking the version:

```bash
kubectl version
```

#### 4.2 Configure `kubectl` Autocompletion

Enable bash autocompletion for `kubectl` by adding the following line to your `.bashrc`:

```bash
echo "source <(kubectl completion bash)" >> ~/.bashrc && source ~/.bashrc
```

This will allow you to use tab-completion for `kubectl` commands, making your work more efficient.

---

## Verification

Now that the cluster is up and running, we can verify everything is working correctly.

### Step 5: Verify the Cluster and Pods

To check the nodes in your Kubernetes cluster, run:

```bash
kubectl get nodes
```

You should see a list of the server and agent nodes in the output.

To check if any pods are running across all namespaces, run:

```bash
kubectl get pods -A
```

This will display any system pods running on your cluster, ensuring the cluster is functioning properly.

---

## Conclusion

In this lab, you learned how to set up a Kubernetes cluster using K3D on your local machine. You now know how to:
1. Install Docker as a prerequisite for K3D.
2. Set up and run a Kubernetes cluster with K3D.
3. Use `kubectl` to interact with your Kubernetes cluster.
4. Verify that your cluster is up and running by checking nodes and pods.

K3D provides an easy and efficient way to set up a lightweight Kubernetes cluster for development or testing on your local machine.

Congratulations on completing **Lab 5.1**! Now you are ready to explore more advanced Kubernetes concepts using your K3D cluster.