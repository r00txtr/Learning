# Lab 1.1: Installing Docker

In this guide, we will walk you through the process of installing Docker on a Linux system (Ubuntu) using easy-to-follow instructions. Docker is a powerful platform that enables developers to automate the deployment and management of applications inside lightweight, portable containers. By the end of this lab, you'll have Docker installed, running, and ready to use.

We will go step by step, explaining each command in detail. Let’s begin!

## Table of Contents
1. **Step 1: Install Docker**
2. **Step 2: Display Docker Version**
3. **Step 3: Display Docker Installation Details**
4. **Step 4: Add User to Docker Group**
5. **Step 5: Test the Docker Installation**
6. **Step 6: Display Downloaded Docker Images**
7. **Step 7: Display All Containers**
8. **Verification**

## Prerequisites

Before we begin, ensure you have:
- A Linux-based system, preferably Ubuntu.
- Access to terminal or SSH for running commands.
- Internet connection for downloading necessary packages.

## Step 1: Install Docker

First, we need to install Docker and some additional required components. Here’s how to do it.

### 1.1 Add Docker's official GPG key

We’ll start by updating the package list and installing some required dependencies:

```bash
sudo apt-get update
sudo apt-get install ca-certificates curl
```

After that, you need to create a directory and download Docker’s GPG key to ensure the security of the packages we install:

```bash
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
```

### 1.2 Add Docker repository to your system

Next, we add the Docker repository to your system so it knows where to find Docker software:

```bash
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

This command determines your system architecture and version and then configures Docker’s official repository.

### 1.3 Install Docker and required components

Now that we’ve added the Docker repository, update the package list again and install Docker:

```bash
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin docker-compose -y
```

Here’s a breakdown:
- `docker-ce`: Docker’s Community Edition.
- `docker-ce-cli`: The command-line interface for Docker.
- `containerd.io`: A container runtime.
- `docker-buildx-plugin`: For building Docker images.
- `docker-compose-plugin`: Manages multi-container Docker applications.

Once installation is done, Docker should be installed on your system.

## Step 2: Display Docker Version

You can verify if Docker was installed correctly by checking its version:

```bash
docker version
```

This command will show the installed Docker version details like the client and server version.

### Example Output:
```
Client: Docker Engine - Community
 Version: 20.10.8
 Server: Docker Engine - Community
 Version: 20.10.8
```

## Step 3: Display Docker Installation Details

To get detailed information about your Docker installation, you can run the following:

```bash
docker info
```

This will display various details, including the number of containers and images, Docker’s storage driver, networking configuration, and more.

### Example Output:
```
Containers: 0
 Images: 0
 Storage Driver: overlay2
 ...
```

## Step 4: Add User to Docker Group

Running Docker commands typically requires root (sudo) privileges. To avoid using `sudo` for every Docker command, you can add your user to the `docker` group.

### 4.1 Create the Docker group (if it doesn't already exist)

```bash
sudo groupadd docker
```

### 4.2 Add your user to the Docker group

```bash
sudo usermod -aG docker $USER
```

This command adds your current user to the `docker` group, allowing you to run Docker commands without `sudo`.

### 4.3 Change Docker socket permissions

```bash
sudo chmod 666 /var/run/docker.sock
```

Changing the permissions on the Docker socket ensures that your user can interact with Docker without needing elevated permissions.

> **Note**: Log out and back in for these changes to take effect, or you can run `newgrp docker` in the terminal to apply the changes without logging out.

## Step 5: Test the Docker Installation

Now that Docker is installed, let’s test it by running a simple Docker container. We will run the "hello-world" container:

```bash
docker run hello-world
```

If Docker is correctly installed, it will download the image from the registry and display a "Hello, World!" message, confirming that Docker is working properly.

### Example Output:
```
Hello from Docker!
This message shows that your installation appears to be working correctly.
...
```

## Step 6: Display Downloaded Docker Images

You can check if the "hello-world" image (or any other images) has been downloaded using the following command:

```bash
docker image ls
```

This will display a list of all Docker images that have been downloaded to your system.

### Example Output:
```
REPOSITORY                           TAG       IMAGE ID       CREATED         SIZE
hello-world                         latest    123abc456789  2 minutes ago   13.3kB
```

## Step 7: Display All Containers (Active or Exited)

To see all containers, whether they are running or stopped, use:

```bash
docker container ls -a
```

This will list all Docker containers along with their status (running, stopped, or exited).

### Example Output:
```
CONTAINER ID   IMAGE                           COMMAND   CREATED          STATUS                       PORTS     NAMES
78cd12345abc   hello-world                     "/hello"   2 minutes ago   Exited (0) 1 minute ago             sleepy_morse
```

## Verification

At this point, you should verify that everything is working correctly. Here’s what you should check:

1. **Run Docker commands without sudo**: After adding your user to the Docker group, try running Docker commands without using `sudo` to see if it works.
2. **Ensure Docker is installed on all nodes**: If you are working in a multi-node environment, make sure Docker is installed on each node.

### Final Check

If you’ve followed all the steps, Docker should now be installed and working on your system. You can easily manage containers, images, and much more using the Docker commands.

If you run into any issues, feel free to review the steps, especially those related to adding the Docker repository and setting the correct permissions.
