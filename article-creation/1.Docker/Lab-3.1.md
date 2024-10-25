# Lab 3.1: Understanding the Default Bridge Network in Docker

In this lab, we will explore how to work with Docker's default bridge network and create a custom bridge network. Understanding Docker's networking model is essential for controlling container communication, both within the same host and across different networks.

By the end of this lab, you will:
1. Display Docker's network setup.
2. Run containers in the default bridge network.
3. Create and use a custom bridge network.
4. Test connectivity between containers using both IP addresses and container names.

---

## Table of Contents
1. **Introduction**
2. **Displaying Docker Networks**
3. **Running Containers in the Default Network**
4. **Creating and Connecting a Custom Bridge Network**
5. **Testing Connectivity Between Containers**
6. **Verification**

---

## Introduction

Docker provides different network drivers for containers to communicate with each other. The **bridge network** is the default network driver used by Docker. When containers are run on the default bridge network, they can communicate using their IP addresses, but not by their container names.

In this lab, we will:
1. Explore the default bridge network.
2. Run two containers and examine their communication capabilities.
3. Create a custom bridge network and observe how it changes container communication.

We will be working on `pod-[username]-node01`. Replace `[username]` with your actual username and `[X]` with the specific IP address for your environment.

---

## Step 1: Displaying Docker Networks

Let’s start by viewing the current Docker networks on your system.

### 1.1 Display a List of Docker Networks

To see the existing networks on your Docker setup, run:

```bash
sudo docker network ls
```

This will list all Docker networks, including the default ones:
- `bridge`: The default bridge network used by containers.
- `host`: Binds container directly to the host’s network interface.
- `none`: A container with no network interface.

You will see output similar to this:

```
NETWORK ID     NAME      DRIVER    SCOPE
a1b2c3d4e5f6   bridge    bridge    local
...
```

The `bridge` network is the default one that Docker uses for container communication.

---

## Step 2: Running Containers in the Default Network

Next, we will run two containers using the default `bridge` network and test how they communicate.

### 2.1 Run Two Alpine Containers

Run two Alpine containers with the `ash` shell in detached mode:

```bash
docker run -dit --name alpine1 alpine ash
docker run -dit --name alpine2 alpine ash
```

- `-dit`: Runs the container in detached, interactive mode with a terminal.
- `--name alpine1` and `--name alpine2`: Assigns the names `alpine1` and `alpine2` to the containers.
- `ash`: Runs the Alpine containers with the `ash` shell.

To verify that the containers are running, use:

```bash
docker container ls
```

You should see both `alpine1` and `alpine2` running.

---

## Step 3: Creating and Connecting a Custom Bridge Network

Now, we will create a custom Docker bridge network and attach the `alpine1` container to this new network.

### 3.1 Create a Custom Bridge Network

Create a new network named `bridge1` using the `bridge` driver:

```bash
docker network create --driver bridge bridge1
```

This creates a new bridge network called `bridge1`.

### 3.2 Connect the Alpine1 Container to the Custom Network

Now, attach the `alpine1` container to the newly created `bridge1` network:

```bash
docker network connect bridge1 alpine1
```

The `alpine1` container is now connected to both the default bridge network and the `bridge1` network.

---

## Step 4: Testing Connectivity Between Containers

Let’s test connectivity between the two Alpine containers. We will ping the containers using both their IP addresses and their container names to understand how communication works in the default and custom bridge networks.

### 4.1 Check the IP Address of Alpine2

First, find the IP address of `alpine2` (which is still on the default `bridge` network):

```bash
docker inspect alpine2 | jq -r '.[].NetworkSettings.IPAddress'
```

You will get an IP address like `172.17.X.X`. Note this address as we will use it for testing.

### 4.2 Access the Alpine1 Container

Now, access the `alpine1` container’s shell:

```bash
docker exec -it alpine1 sh
```

This opens an interactive shell inside the `alpine1` container.

### 4.3 Check the IP Address of Alpine1

Inside the `alpine1` container, run the following command to check its IP address:

```bash
ip add
```

You will see the IP address assigned to the `alpine1` container, likely starting with `172.17`.

### 4.4 Ping Test to the Internet from Alpine1

Now, test the internet connection by pinging Google’s DNS server from the `alpine1` container:

```bash
ping -c 3 8.8.8.8
```

If everything is working correctly, the pings should succeed, confirming that the container can access the internet.

### 4.5 Ping Test to Alpine2’s IP Address

Next, ping the `alpine2` container using its IP address:

```bash
ping -c 3 172.17.X.X
```

Replace `172.17.X.X` with the actual IP address of `alpine2`. The pings should succeed, confirming that `alpine1` can communicate with `alpine2` using its IP address.

### 4.6 Ping Test to Alpine2 by Container Name

Now, try to ping `alpine2` using its container name:

```bash
ping -c 3 alpine2
```

In the default bridge network, this ping will **fail** because containers in the default bridge network cannot resolve each other by name.

### 4.7 Exit the Alpine1 Container

To exit the `alpine1` container without stopping it, press `Ctrl+P` followed by `Ctrl+Q`. This combination keeps the container running while detaching you from the interactive session.

---

## Step 5: Verification

To verify that the lab was completed successfully, check the following:
1. The `bridge1` network has been created using the `docker network ls` command.
2. The `alpine1` container is connected to both the default bridge network and the `bridge1` network.
3. Ping tests to `8.8.8.8` (Google DNS) and `alpine2`’s IP address succeed from `alpine1`.
4. Ping tests using the container name `alpine2` fail when both containers are only on the default bridge network, as expected.

### Example Output for Successful Ping Test:

```
PING 8.8.8.8 (8.8.8.8): 56 data bytes
64 bytes from 8.8.8.8: seq=0 ttl=56 time=14.857 ms
64 bytes from 8.8.8.8: seq=1 ttl=56 time=14.204 ms
64 bytes from 8.8.8.8: seq=2 ttl=56 time=14.758 ms
```

This confirms that the internet connection is working.

---

## Conclusion

In this lab, you’ve learned how to:
1. List existing Docker networks.
2. Run containers on Docker’s default bridge network.
3. Create a custom bridge network and connect containers to it.
4. Test connectivity between containers using IP addresses and container names.

You now have a solid understanding of how Docker networks work and how to control container communication using bridge networks.

Happy Dockering!
