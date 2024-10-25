# Lab 9.2: Configuring HAProxy for Web Servers

In this lab, you will configure **HAProxy** to proxy requests to two web server containers running **NGINX** and **Apache HTTP Server**. You will deploy HAProxy in a container and ensure it routes traffic to the appropriate web server container based on the requested domain.

By the end of this lab, you will:
1. Create two web server containers running **NGINX** and **Apache HTTP Server**.
2. Configure HAProxy to route traffic to the appropriate web server based on the domain name.
3. Deploy HAProxy in a container using an HAProxy configuration file.
4. Ensure that HAProxy routes traffic correctly without modifying the host file (`/etc/hosts`) directly for container IPs.

---

## Table of Contents
1. **Introduction**
2. **Step-by-Step Instructions**
    1. Creating the Directory and Network
    2. Deploying Web Server Containers
    3. Configuring HAProxy
    4. Deploying the HAProxy Container
3. **Verification**
4. **Conclusion**

---

## Introduction

In this lab, we will use **HAProxy** as a proxy server to route traffic between two web servers: **web1** running NGINX and **web2** running Apache HTTP Server. The goal is to set up a system where HAProxy routes traffic based on the domain name in the request. 

### Key Concepts:
- **HAProxy**: A fast and reliable load balancer and proxy server for TCP and HTTP-based applications.
- **NGINX**: A lightweight web server often used for serving static content.
- **Apache HTTP Server**: One of the most popular web servers, particularly for dynamic web pages.

You will be working with:
- **pod-[username]-node01** for running all containers and configurations.
- **Docker** for container orchestration.

### Clue
- Do not point container IPs directly in `/etc/hosts`.
- Route all domains to the IP of `node01`.
- Use Docker volumes to store HAProxy configuration files.
- This setup is for proxying, not for load balancing.

---

## Step-by-Step Instructions

### Step 1: Create a Directory and Network

#### 1.1 Create the challenge1 directory
On `pod-[username]-node01`, create a directory called `challenge1` where you will store your HAProxy configuration file.

```bash
mkdir ~/challenge1
cd ~/challenge1
```

#### 1.2 Create a Docker network
To allow the containers to communicate, create a Docker network.

```bash
docker network create webnet
```

This will create an isolated network where all containers (web1, web2, and HAProxy) can communicate.

---

### Step 2: Deploy Web Server Containers

#### 2.1 Create the web1 container (NGINX)
Run the following command to create a container for **web1** using the **nginx:latest** image:

```bash
docker run -d --name web1 --network webnet -p 8081:80 nginx:latest
```

This starts the NGINX container and makes it accessible on port 8081.

#### 2.2 Create the web2 container (Apache HTTPD)
Run the following command to create a container for **web2** using the **httpd:latest** image:

```bash
docker run -d --name web2 --network webnet -p 8082:80 httpd:latest
```

This starts the Apache HTTP Server container and makes it accessible on port 8082.

---

### Step 3: Configure HAProxy

#### 3.1 Create the HAProxy configuration file
In the `challenge1` directory, create a file called `haproxy.cfg`. This file will define the rules for routing traffic to web1 or web2 based on the domain name.

Use the following example configuration:

```plaintext
global
    log 127.0.0.1 local0
    maxconn 2048

defaults
    log     global
    mode    http
    option  httplog
    timeout connect 5000ms
    timeout client  50000ms
    timeout server  50000ms

frontend http-in
    bind *:80
    acl host_web1 hdr(host) -i web1.[username].id
    acl host_web2 hdr(host) -i web2.[username].id

    use_backend web1-backend if host_web1
    use_backend web2-backend if host_web2

backend web1-backend
    server web1 web1:80

backend web2-backend
    server web2 web2:80
```

Replace `[username]` with your actual username.

Explanation:
- The `frontend` section listens on port 80 and matches the domain name to decide which backend to route the request to.
- The `backend` sections specify the web servers (web1 or web2) to forward the traffic to.

---

### Step 4: Deploy the HAProxy Container

#### 4.1 Create a Docker volume for HAProxy configuration
Create a volume to store the HAProxy configuration file:

```bash
docker volume create haproxy-config
```

#### 4.2 Copy the `haproxy.cfg` to the volume
Copy the HAProxy configuration file (`haproxy.cfg`) to the Docker volume:

```bash
docker run --rm -v haproxy-config:/cfg -v $(pwd):/data alpine cp /data/haproxy.cfg /cfg
```

#### 4.3 Create and run the HAProxy container
Now, create and start the HAProxy container using the `mwar8205/haproxy-alpine:2.4` image:

```bash
docker run -d --name [username]-haproxy --network webnet -p 80:80 -v haproxy-config:/usr/local/etc/haproxy mwar8205/haproxy-alpine:2.4
```

This command:
- Starts the HAProxy container and binds it to port 80 on the host.
- Mounts the HAProxy configuration from the `haproxy-config` volume.

---

## Verification

### 1. Test HAProxy Routing
Open a web browser or use `curl` to test whether HAProxy routes traffic correctly:

- For **web1**:
  ```bash
  curl -H "Host: web1.[username].id" http://your-local-ip
  ```

- For **web2**:
  ```bash
  curl -H "Host: web2.[username].id" http://your-local-ip
  ```

Ensure that:
- Requests to `web1.[username].id` return the NGINX page.
- Requests to `web2.[username].id` return the Apache HTTP Server page.

### 2. Verify HAProxy Configuration
Check the logs of the HAProxy container to ensure it’s routing requests correctly:

```bash
docker logs [username]-haproxy
```

---

## Conclusion

In this lab, you have successfully:
1. Set up two web server containers using NGINX and Apache HTTP Server.
2. Configured HAProxy to route traffic based on domain names.
3. Deployed HAProxy in a container and verified its functionality.

This completes **Lab 9.2: Configuring HAProxy for Web Servers**.