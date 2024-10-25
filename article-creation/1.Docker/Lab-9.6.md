# Lab 9.6: Docker Swarm Service Scaling

In this lab, you will explore how to scale services within a **Docker Swarm** cluster. Docker Swarm provides powerful features to manage containerized applications, including scaling services up and down dynamically, performing rolling updates, and maintaining high availability through fault tolerance. You will learn how to scale services based on performance requirements, automate failover, and perform rolling updates for continuous deployment.

By the end of this lab, you will:
1. Scale a service up and down within a Docker Swarm cluster.
2. Ensure high availability and fault tolerance for services.
3. Implement rolling updates with zero downtime.
4. Test service scaling and load balancing across nodes.

---

## Table of Contents
1. **Introduction**
2. **Step-by-Step Instructions**
   1. Initialize Docker Swarm and Deploy a Service
   2. Scale the Service Up and Down
   3. Test Failover and High Availability
   4. Perform a Rolling Update with Zero Downtime
3. **Verification**
4. **Conclusion**

---

## Introduction

**Docker Swarm** allows you to create a cluster of machines running Docker, where services can be deployed, scaled, and managed easily. Scaling services dynamically is essential for meeting user demand, improving fault tolerance, and ensuring service availability. In this lab, you will:
1. Initialize a Docker Swarm and deploy a web service.
2. Scale the service up to handle more traffic and scale it down when resources are no longer needed.
3. Implement high availability and fault tolerance to ensure that services continue running even when nodes fail.
4. Perform rolling updates to services with zero downtime, enabling continuous deployment.

---

## Step-by-Step Instructions

### Step 1: Initialize Docker Swarm and Deploy a Service

#### 1.1 Initialize Docker Swarm

If you have not already initialized a Docker Swarm, run the following command on your primary node (manager node) to initialize the Swarm:

```bash
docker swarm init --advertise-addr <MANAGER_IP>
```

Explanation:
- `--advertise-addr <MANAGER_IP>`: Use the IP address of the manager node to advertise itself to other nodes.

To add worker nodes to the Swarm, run the command provided after initializing the Swarm on each worker node. For example:

```bash
docker swarm join --token <TOKEN> <MANAGER_IP>:2377
```

#### 1.2 Deploy a Service with Replicas

Once the Swarm is set up, deploy a simple web service (e.g., Nginx) with 2 replicas:

```bash
docker service create --name web --replicas 2 -p 80:80 nginx:latest
```

Explanation:
- `--replicas 2`: Deploys 2 replicas of the Nginx service.
- `-p 80:80`: Exposes port 80 for the web service.

Verify the service is running:

```bash
docker service ls
```

You should see the `web` service with 2 replicas.

---

### Step 2: Scale the Service Up and Down

#### 2.1 Scale the Service Up

To handle higher traffic, you can scale the `web` service by increasing the number of replicas. Use the following command to scale up the service to 5 replicas:

```bash
docker service scale web=5
```

Verify that the service has been scaled up:

```bash
docker service ps web
```

The output will display the current state of the service, showing 5 replicas distributed across the Swarm nodes.

#### 2.2 Scale the Service Down

When traffic reduces, you can scale down the service to free up resources. Run the following command to scale the `web` service back to 2 replicas:

```bash
docker service scale web=2
```

Again, verify that the service has been scaled down:

```bash
docker service ps web
```

The replicas will reduce to 2, reflecting the change in the service's state.

---

### Step 3: Test Failover and High Availability

#### 3.1 Simulate a Node Failure

One of the key advantages of Docker Swarm is fault tolerance. In this step, we will simulate a failure of one of the worker nodes and observe how Docker Swarm handles the situation.

First, identify the nodes in your Swarm:

```bash
docker node ls
```

Next, simulate a failure by stopping Docker on one of the worker nodes:

```bash
sudo systemctl stop docker
```

Swarm will detect the node failure and redistribute the affected containers to the remaining healthy nodes.

#### 3.2 Check High Availability

To confirm that the service is still running after the node failure, use the following command:

```bash
docker service ps web
```

You will see that the replicas previously running on the failed node have been rescheduled to other healthy nodes, ensuring continuous availability of the service.

---

### Step 4: Perform a Rolling Update with Zero Downtime

#### 4.1 Update the Service

Docker Swarm allows you to perform rolling updates on services, where each container is updated one at a time, ensuring no downtime. Let’s update the `web` service to use a specific version of Nginx (`nginx:1.21`):

```bash
docker service update --image nginx:1.21 web
```

Explanation:
- `--image nginx:1.21`: Specifies the updated image version for the service.

#### 4.2 Monitor the Rolling Update

To monitor the progress of the rolling update, run:

```bash
docker service ps web
```

This will show how the replicas are updated gradually without causing downtime. Once the update is complete, all replicas will be running the updated version (`nginx:1.21`).

#### 4.3 Rollback the Update (Optional)

In case the update causes issues, you can easily roll back the service to the previous version:

```bash
docker service rollback web
```

Swarm will automatically roll back to the last stable state of the service.

---

## Verification

To verify that the lab has been completed successfully, check the following:
1. The `web` service is running with the specified number of replicas, and scaling up and down works as expected.
2. High availability is maintained when a node fails, and the replicas are rescheduled to other healthy nodes.
3. Rolling updates are performed without downtime, and services can be rolled back if necessary.

---

## Conclusion

In this lab, you learned how to:
1. Scale services in a Docker Swarm cluster to meet changing demand.
2. Ensure high availability and fault tolerance by redistributing containers during node failures.
3. Perform rolling updates and rollbacks with zero downtime.

By mastering service scaling in Docker Swarm, you are well-equipped to manage applications in a production environment, ensuring flexibility, reliability, and continuous availability.

---

### Example Output of `docker service ls`:

```plaintext
ID                  NAME                MODE                REPLICAS            IMAGE               PORTS
0p9jk2kslil3        web                 replicated          5/5                 nginx:1.21          *:80->80/tcp
```

Congratulations on completing **Lab 9.6: Docker Swarm Service Scaling**! You now have a strong foundation in scaling and managing services in Docker Swarm.

Happy Dockering!