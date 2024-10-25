# Lab 6.2: Deploy Service to Docker Swarm

In this lab, we will learn how to deploy services to a **Docker Swarm**. A **Docker Service** is a task running on a node in a Swarm, allowing you to scale containers across multiple nodes. You will also learn how to manage services, limit resources, and inspect the running services in the Swarm.

By the end of this lab, you will:
1. Deploy an Nginx service with two replicas in a Swarm.
2. Test the Nginx service across multiple nodes.
3. Deploy a service with CPU and memory limits.
4. Inspect and manage services in the Swarm.

---

## Table of Contents
1. **Introduction**
2. **Step-by-Step Instructions**
    1. Deploying Nginx Service with Replicas
    2. Testing the Service Across Nodes
    3. Inspecting and Managing the Service
    4. Deploying a Service with CPU and Memory Limits
3. **Verification**
4. **Conclusion**

---

## Introduction

A **Docker Service** in Swarm mode allows you to run containers across multiple nodes with load balancing and scaling. In this lab, you will:
1. Create an Nginx service with two replicas, which means it will run on two nodes.
2. Test if the service is accessible from both nodes.
3. Create another service with CPU and memory limits to manage resource usage.
4. Inspect the service specifications and running details.

We will use `pod-[username]-node01` as the Swarm manager and `pod-[username]-node02` as a worker node. Replace `[username]` with your username and `[X]` with the appropriate IP address segment for your nodes.

---

## Step-by-Step Instructions

### Step 1: Deploying Nginx Service with Replicas

#### 1.1 Create an Nginx Service with Two Replicas

First, create a Docker service named `web` with two replicas. The service will use the Nginx image and expose port 80 on both nodes:

```bash
docker service create --name web --replicas 2 -p 80:80 nginx:latest
```

Explanation:
- `--name web`: Names the service `web`.
- `--replicas 2`: Creates two instances (replicas) of the Nginx container, which will be distributed across the nodes.
- `-p 80:80`: Maps port 80 on the host to port 80 inside the container.

Once the command runs successfully, Docker will schedule two Nginx containers across the available nodes in the Swarm.

### Step 2: Testing the Service Across Nodes

#### 2.1 Check if the Service is Running Correctly

You can check the status of the running services by listing all services in the Swarm:

```bash
docker service ls
```

This command will display the service name, number of replicas, and ports that are exposed. The output should show the `web` service with two replicas running.

#### 2.2 Test the Service on Node01 and Node02

Now, test if the Nginx service is accessible from both **node01** and **node02**.

Run the following `curl` command on **node01**:

```bash
curl http://10.7.7.10
```

Next, run the `curl` command on **node02**:

```bash
curl http://10.7.7.20
```

Both commands should return the default Nginx welcome page, confirming that the service is correctly running and accessible from both nodes.

---

### Step 3: Inspecting and Managing the Service

#### 3.1 Inspect Service Details

To inspect detailed information about the `web` service, use the following command:

```bash
docker service inspect --pretty web
```

This will display human-readable information about the service, including its configuration, image, environment variables, and network settings.

#### 3.2 Check Where the Service is Running

To see which nodes the Nginx replicas are running on, use the following command:

```bash
docker service ps web
```

This command lists the nodes where the service tasks are running, along with their current status.

---

### Step 4: Deploying a Service with CPU and Memory Limits

Now, let’s create a new service with resource limits. We will deploy an Apache HTTP server with specific CPU and memory limits.

#### 4.1 Create the Service with CPU and Memory Limits

Run the following command to create a service named `[username]` with CPU and memory constraints:

```bash
docker service create --name [username] --reserve-cpu 1 --limit-cpu 1 \
--reserve-memory 256mb --limit-memory 128mb httpd:latest
```

Explanation:
- `--reserve-cpu 1`: Reserves 1 CPU core for this service.
- `--limit-cpu 1`: Limits the service to use only 1 CPU core.
- `--reserve-memory 256mb`: Reserves 256MB of memory for the service.
- `--limit-memory 128mb`: Limits the service to use only 128MB of memory.
- `registry.adinusa.id/btacademy/httpd:latest`: Uses the Apache HTTP server image.

#### 4.2 Inspect the Service Specifications

To verify the CPU and memory limits of the service, inspect the service details:

```bash
docker service inspect --pretty [username]
```

This will show detailed information, including the resource limits you set.

---

## Verification

To ensure that the lab was completed successfully, check the following:
1. The **Nginx** service (`web`) is running with two replicas, and you can access it from both **node01** and **node02**.
2. The service with your username is running with the specified CPU and memory limits.
3. The services are running on multiple nodes, and the correct specifications are applied.

---

## Conclusion

In this lab, you learned how to:
1. Deploy a service with multiple replicas using **Docker Swarm**.
2. Test a service across multiple nodes to ensure it is accessible from different locations.
3. Manage service resources by setting CPU and memory limits.
4. Inspect and verify services running in the Swarm.

Docker Swarm makes it easy to scale applications, manage resources, and ensure high availability by distributing services across multiple nodes.

### Example Output of `docker service ls`:

```
ID             NAME         MODE         REPLICAS   IMAGE                                  PORTS
0p9jk2kslil3   web          replicated   2/2        nginx                                   *:80->80/tcp
1j8hk3jslka7   [username]   replicated   1/1        httpd                                   *:80->80/tcp
```

Congratulations on completing **Lab 6.2: Deploying a Service to Docker Swarm**! You now have a strong foundation in deploying and managing services in a multi-node Docker Swarm cluster.

Happy Dockering!
