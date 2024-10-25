# Lab 6.3: Docker Swarm Scale and Update

In this lab, we will explore how to **scale** and **update** services in a **Docker Swarm**. Scaling allows you to increase or decrease the number of replicas for a service, while rolling updates let you update the service without downtime by replacing replicas with new ones incrementally.

By the end of this lab, you will:
1. Create a Docker service with multiple replicas.
2. Scale the service by adjusting the number of replicas.
3. Perform a rolling update to change the service image.
4. Verify that the scaling and update processes have been completed successfully.

---

## Table of Contents
1. **Introduction**
2. **Step-by-Step Instructions**
    1. Creating the Service with Multiple Replicas
    2. Scaling the Service
    3. Performing a Rolling Update
3. **Verification**
4. **Conclusion**

---

## Introduction

In a **Docker Swarm**, services can be scaled to meet traffic demands or updated to roll out new features or fixes. Scaling a service means adjusting the number of container replicas that are running the service, while a **rolling update** allows you to update the service image with minimal downtime.

We will:
1. Create a service with 3 replicas.
2. Scale the service down to 1 replica.
3. Perform a rolling update to change the service image.
4. Verify that the updates are applied and the service is running as expected.

---

## Step-by-Step Instructions

### Step 1: Creating the Service with Multiple Replicas

First, we will create an Nginx service with 3 replicas. These replicas will be spread across the available nodes in the Docker Swarm, ensuring high availability.

#### 1.1 Create the Nginx Service

Run the following command on `pod-[username]-node01` to create the service:

```bash
docker service create --name web2 --replicas 3 -p 80:80 nginx:latest
```

Explanation:
- `--name web2`: Names the service `web2`.
- `--replicas 3`: Creates 3 instances (replicas) of the Nginx container.
- `-p 80:80`: Maps port 80 on the host to port 80 inside the container.
- `nginx:latest`: Uses the latest version of the Nginx image from the registry.

After running this command, Docker will create 3 Nginx containers (replicas) across the Swarm nodes.

---

### Step 2: Scaling the Service

Now that we have the Nginx service running with 3 replicas, let’s reduce the number of replicas to 1.

#### 2.1 Scale the Service

To change the number of replicas to 1, run the following command:

```bash
docker service scale web2=1
```

This command adjusts the number of replicas for the `web2` service to 1.

---

### Step 3: Performing a Rolling Update

Docker Swarm supports **rolling updates**, which allow you to update a service’s image with minimal downtime. During a rolling update, Swarm replaces each replica one by one, ensuring that the service remains available throughout the process.

#### 3.1 Update the Service Image

Let’s update the `web2` service to use a new image, `sistyo/myweb`, which will serve a PHP page:

```bash
docker service update --image sistyo/myweb web2
```

Explanation:
- `--image sistyo/myweb`: Updates the service to use the `sistyo/myweb` image, which serves a PHP page.
- `web2`: The name of the service to be updated.

#### 3.2 Inspect the Service

After the update, inspect the service to ensure that the new image is being used:

```bash
docker service inspect --pretty web2
```

This command displays detailed information about the service, including the current image, replicas, and update status.

---

## Verification

To verify that the lab was completed successfully, check the following:

### 1. Ensure the Number of Replicas is 1

Run the following command to check the current number of replicas:

```bash
docker service ls
```

The output should show that the `web2` service has 1 replica:

```
ID             NAME   MODE         REPLICAS  IMAGE
abcdef123456   web2   replicated   1/1       sistyo/myweb:latest
```

### 2. Verify the Updated Image

Inspect the service to ensure it is using the updated image (`sistyo/myweb`):

```bash
docker service inspect --pretty web2
```

Look for the `Image` field in the output, which should show `sistyo/myweb:latest`.

### 3. Check if the Update was Completed

Check the update status by running the following command:

```bash
docker service inspect --pretty web2
```

The output should include a message indicating that the update was completed successfully:

```
UpdateStatus:
 State: completed
 Message: update completed
```

### 4. Test the Service

Test the service to ensure it is now serving the updated PHP page. Run the following `curl` command on **node01**:

```bash
curl http://10.7.7.10
```

You should see the PHP page served by the updated image.

---

## Conclusion

In this lab, you learned how to:
1. Create a Docker service with multiple replicas.
2. Scale the service by adjusting the number of replicas.
3. Perform a rolling update to change the service image.
4. Verify that the scaling and update processes were completed successfully.

### Example Output for `docker service ls`:

```
ID             NAME   MODE         REPLICAS  IMAGE
abcdef123456   web2   replicated   1/1       sistyo/myweb:latest
```

By scaling services and performing rolling updates, you can manage and maintain highly available applications in Docker Swarm, ensuring minimal downtime during updates.

Congratulations on completing **Lab 6.3: Docker Swarm Scale and Update**! You now have a solid understanding of scaling and updating services in a Docker Swarm cluster.

Happy Dockering!
