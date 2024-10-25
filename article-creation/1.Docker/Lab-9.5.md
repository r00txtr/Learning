# Lab 9.5: Load Balancing with Traefik

In this lab, you will learn how to set up **Traefik** as a reverse proxy and load balancer for Dockerized services. Traefik dynamically discovers services and applies routing rules based on container labels. You will also learn how to configure HTTPS using **Let’s Encrypt** for automatic certificate management, implement middleware features such as request redirection, and enforce rate limiting for services.

By the end of this lab, you will:
1. Set up Traefik as a load balancer for multiple services.
2. Configure Traefik with dynamic service discovery using Docker labels.
3. Implement HTTPS using Let’s Encrypt for certificate generation.
4. Use Traefik middleware to manage traffic redirection and rate limiting.

---

## Table of Contents
1. **Introduction**
2. **Step-by-Step Instructions**
   1. Install Traefik as a Reverse Proxy
   2. Configure Traefik for Docker Dynamic Service Discovery
   3. Enable HTTPS with Let’s Encrypt
   4. Implement Middleware Features
3. **Verification**
4. **Conclusion**

---

## Introduction

**Traefik** is a modern reverse proxy and load balancer designed specifically for microservices and containerized environments like Docker. It dynamically routes traffic to services based on metadata, making it ideal for environments where services are constantly changing.

In this lab, we will:
1. Deploy Traefik as a reverse proxy for multiple services.
2. Dynamically discover Docker services and route traffic using labels.
3. Configure HTTPS with Let’s Encrypt to secure service endpoints.
4. Implement advanced middleware features like request redirection and rate limiting.

---

## Step-by-Step Instructions

### Step 1: Install Traefik as a Reverse Proxy

#### 1.1 Create a Traefik Docker Compose File

To get started, we will deploy Traefik using Docker Compose. Create a `docker-compose.yml` file for Traefik:

```yaml
version: '3'

services:
  traefik:
    image: traefik:v2.4
    command:
      - "--api.insecure=true"
      - "--providers.docker=true"
      - "--entrypoints.web.address=:80"
      - "--entrypoints.websecure.address=:443"
      - "--certificatesresolvers.myresolver.acme.tlschallenge=true"
      - "--certificatesresolvers.myresolver.acme.email=your-email@example.com"
      - "--certificatesresolvers.myresolver.acme.storage=/letsencrypt/acme.json"
    ports:
      - "80:80"
      - "443:443"
      - "8080:8080" # Traefik dashboard
    volumes:
      - "/var/run/docker.sock:/var/run/docker.sock"
      - "./letsencrypt:/letsencrypt"
    networks:
      - web

networks:
  web:
    external: true
```

Explanation:
- `--providers.docker=true`: Enables Traefik to discover Docker services dynamically.
- `--entrypoints.web.address=:80`: Routes HTTP traffic through port 80.
- `--entrypoints.websecure.address=:443`: Routes HTTPS traffic through port 443.
- `--certificatesresolvers`: Configures Let’s Encrypt to issue certificates automatically.

Run Traefik with Docker Compose:

```bash
docker-compose up -d
```

#### 1.2 Access the Traefik Dashboard

Once Traefik is running, you can access its dashboard by navigating to `http://localhost:8080/dashboard/`. The dashboard will show you the running services, routes, and configuration.

---

### Step 2: Configure Traefik for Docker Dynamic Service Discovery

#### 2.1 Set Up Example Services

Now, we will deploy two simple web services: **whoami** services, which return information about the container. Each service will be labeled to enable Traefik routing.

Create the following Docker Compose file:

```yaml
version: '3'

services:
  whoami1:
    image: "containous/whoami"
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.whoami1.rule=Host(`whoami1.localhost`)"
      - "traefik.http.services.whoami1.loadbalancer.server.port=80"
    networks:
      - web

  whoami2:
    image: "containous/whoami"
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.whoami2.rule=Host(`whoami2.localhost`)"
      - "traefik.http.services.whoami2.loadbalancer.server.port=80"
    networks:
      - web

networks:
  web:
    external: true
```

Explanation:
- `traefik.http.routers.whoami1.rule=Host('whoami1.localhost')`: Routes traffic to the `whoami1` service based on the hostname.
- The same applies for `whoami2`.

Start the services:

```bash
docker-compose up -d
```

#### 2.2 Test Service Routing

Now, add the following lines to your `/etc/hosts` file to simulate the hostnames:

```plaintext
127.0.0.1 whoami1.localhost
127.0.0.1 whoami2.localhost
```

Test the routing by visiting `http://whoami1.localhost` and `http://whoami2.localhost`. You should see a response from the respective `whoami` service, confirming that Traefik is correctly routing traffic.

---

### Step 3: Enable HTTPS with Let’s Encrypt

#### 3.1 Configure Let’s Encrypt for HTTPS

To secure your services with HTTPS, Traefik can automatically issue certificates using Let’s Encrypt. This was already configured in the Traefik `docker-compose.yml` file under `certificatesresolvers`. The certificates will be stored in the `./letsencrypt/acme.json` file.

To test HTTPS locally, use a self-signed certificate or set up a domain with DNS validation.

Access your services via HTTPS:

```bash
https://whoami1.localhost
https://whoami2.localhost
```

The certificates should be automatically issued by Let’s Encrypt, and you will see a secure connection.

---

### Step 4: Implement Middleware Features

#### 4.1 Set Up Request Redirection

You can use Traefik middleware to redirect HTTP traffic to HTTPS or manage other redirection rules. Update the labels in your Docker Compose file:

```yaml
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.whoami1.rule=Host(`whoami1.localhost`)"
      - "traefik.http.routers.whoami1.entrypoints=web"
      - "traefik.http.middlewares.redirect-to-https.redirectscheme.scheme=https"
      - "traefik.http.routers.whoami1.middlewares=redirect-to-https"
```

Explanation:
- `redirect-to-https`: Redirects all HTTP traffic to HTTPS.

#### 4.2 Implement Rate Limiting

To prevent overloading a service, you can use middleware to limit the number of requests. Add the following labels to your service:

```yaml
    labels:
      - "traefik.http.middlewares.ratelimit.ratelimit.average=5"
      - "traefik.http.middlewares.ratelimit.ratelimit.burst=10"
      - "traefik.http.routers.whoami1.middlewares=ratelimit"
```

Explanation:
- `ratelimit.average=5`: Allows 5 requests per second.
- `ratelimit.burst=10`: Allows up to 10 requests in a short burst.

---

## Verification

To verify that the lab was completed successfully, check the following:
1. Traefik is running and accessible through the dashboard (`http://localhost:8080/dashboard`).
2. Services `whoami1` and `whoami2` are accessible through Traefik’s reverse proxy.
3. HTTPS is enabled, and the certificates have been generated by Let’s Encrypt.
4. HTTP traffic is redirected to HTTPS.
5. Rate limiting is applied to the `whoami1` service.

---

## Conclusion

In this lab, you learned how to:
1. Set up Traefik as a reverse proxy and load balancer for Dockerized services.
2. Dynamically discover and route services using Docker labels.
3. Implement HTTPS using Let’s Encrypt for automatic certificate management.
4. Use Traefik middleware for request redirection and rate limiting.

By using Traefik, you have added a flexible and powerful load balancer that can dynamically adjust to the changing needs of your Docker services, ensuring secure and efficient traffic management.

---

### Example Output of `docker-compose up` for Traefik and Services:

```plaintext
Starting traefik ... done
Starting whoami1 ... done
Starting whoami2 ... done
```

Congratulations on completing **Lab 9.5: Load Balancing with Traefik**! You are now well-equipped to handle dynamic routing and load balancing for your Docker services.

Happy Dockering!