# Lab 8.1: Configuring Health Check for Docker Containers

In this lab, we will configure health checks for Docker containers. Health checks allow you to monitor the state of your containers and ensure that services inside them are running correctly. By setting up health checks, Docker can automatically determine if a container is "healthy" or "unhealthy," allowing you to take action based on its health status.

By the end of this lab, you will:
1. Implement a health check in a Dockerfile.
2. Build and run a Docker image with a health check.
3. Verify the health status of a container using various tools.
4. Simulate an unhealthy state for a container and observe how Docker responds.

---

## Table of Contents
1. **Introduction**
2. **Health Check Practice 01**
    1. Creating the Working Directory
    2. Creating a Dockerfile with Health Check
    3. Building and Running the Docker Image
    4. Verifying Container Health
3. **Health Check Practice 02**
    1. Setting up a Custom Node.js Server with Health Check
    2. Building and Running the Docker Image
    3. Verifying Container Health
4. **Verification**
5. **Conclusion**

---

## Introduction

In Docker, health checks allow you to determine if your container is functioning as expected. This is especially useful for applications that may occasionally fail but continue running, giving the appearance of normal operation. By defining health checks in the Dockerfile, you can automate the monitoring of your containers.

We will execute this lab on `pod-[username]-node01`. Make sure to replace `[username]` with your actual username where needed.

---

## Health Check Practice 01

### Step 1: Creating the Working Directory

To start, we will create a directory where we will work on our first health check practice.

```bash
cd $HOME
mkdir hc-latihan01
cd hc-latihan01
```

### Step 2: Creating a Dockerfile with Health Check

Next, we will create a Dockerfile that sets up a health check for a simple HTTP server.

1. Create a Dockerfile using `vim`:

   ```bash
   vim Dockerfile
   ```

2. Add the following content:

   ```Dockerfile
   FROM mwar8205/docker-http-server:health
   HEALTHCHECK --interval=1s --retries=3 \
       CMD curl --fail http://localhost:80/ || exit 1
   ```

   This Dockerfile:
   - Specifies a health check that runs every 1 second and retries up to 3 times.
   - Uses `curl` to check if the HTTP server at `localhost:80` is reachable. If not, it will fail and mark the container as "unhealthy."

### Step 3: Building and Running the Docker Image

1. Build the Docker image from the Dockerfile:

   ```bash
   docker build -t http-healthcheck .
   ```

2. Run the image in detached mode and expose port 80:

   ```bash
   docker run -d -p 80:80 --name http-healthcheck http-healthcheck
   ```

### Step 4: Verifying Container Health

1. Check if the container is running and view the health status:

   ```bash
   docker ps
   ```

   Look for the `STATUS` section, which will indicate if the container is healthy or unhealthy.

2. Verify that the HTTP server is accessible by running:

   ```bash
   curl http://localhost/
   ```

3. Simulate an unhealthy state by requesting a non-existent endpoint:

   ```bash
   curl http://localhost/unhealthy
   ```

4. Check the container status again:

   ```bash
   docker container ls
   ```

   The status should now reflect that the container is unhealthy.

---

## Health Check Practice 02

### Step 1: Setting up a Custom Node.js Server with Health Check

We will now create a custom Node.js application with a health check.

1. Create a new directory for the second practice:

   ```bash
   cd $HOME
   mkdir hc-latihan02
   cd hc-latihan02
   ```

2. Create a `server.js` file:

   ```bash
   vim server.js
   ```

3. Add the following code to `server.js`:

   ```javascript
   "use strict";
   const http = require('http');

   function createServer () {
       return http.createServer(function (req, res) {
           res.writeHead(200, {'Content-Type': 'text/plain'});
           res.end('OK\n');
       }).listen(8080);
   }

   let server = createServer();

   http.createServer(function (req, res) {
       res.writeHead(200, {'Content-Type': 'text/plain'});
       if (server) {
           server.close();
           server = null;
           res.end('Shutting down...\n');
       } else {
           server = createServer();
           res.end('Starting up...\n');
       }
   }).listen(8081);
   ```

   This code:
   - Creates an HTTP server that listens on port 8080 and responds with "OK".
   - Creates another server on port 8081 that can shut down and restart the server on port 8080.

### Step 2: Creating a Dockerfile with Health Check

1. Create a Dockerfile:

   ```bash
   vim Dockerfile
   ```

2. Add the following content:

   ```Dockerfile
   FROM node:latest
   COPY server.js /
   EXPOSE 8080 8081
   HEALTHCHECK --interval=5s --timeout=10s --retries=3 CMD curl -sS 127.0.0.1:8080 || exit 1
   CMD ["node","server.js"]
   ```

   This Dockerfile:
   - Exposes ports 8080 and 8081.
   - Defines a health check that runs every 5 seconds and times out after 10 seconds.
   - Uses `curl` to check if the server on port 8080 is reachable.

### Step 3: Building and Running the Docker Image

1. Build the Docker image:

   ```bash
   docker build -t node-server .
   ```

2. Run the image:

   ```bash
   docker run -d --name nodeserver -p 8080:8080 -p 8081:8081 node-server
   ```

### Step 4: Verifying Container Health

1. Check the server on port 8080:

   ```bash
   curl 127.0.0.1:8080
   docker ps 
   docker inspect --format "{{ json .State.Health }}" nodeserver | jq .
   ```

2. Check the server on port 8081:

   ```bash
   curl 127.0.0.1:8081
   docker ps 
   docker inspect --format "{{ json .State.Health }}" nodeserver | jq .
   ```

3. Verify the health status using the Docker CLI and `jq` to parse the health data.

---

## Verification

To ensure that the lab was completed successfully, verify the following:

1. The `http-healthcheck` container becomes "unhealthy" when the `/unhealthy` endpoint is triggered.
2. The `node-server` container remains "healthy" when the server on port 8080 is running.

---

## Conclusion

In this lab, you learned how to:
1. Configure health checks for Docker containers.
2. Implement health checks in Dockerfiles.
3. Monitor the health status of containers using `docker ps`, `docker inspect`, and `curl`.
4. Simulate unhealthy states and verify how Docker responds.

By using health checks, you can ensure that your containers are running as expected and take action if they become unhealthy.

Happy Dockering!