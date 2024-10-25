To achieve the behavior where accessing `http://web1.id` routes to `web1` (NGINX) and accessing `http://web2.id` routes to `web2` (HTTPD), we will set up HAProxy with host-based routing. Here's a step-by-step guide from the start:

### Step 1: Create Docker Network

First, create a custom Docker network so that the containers can communicate with each other:

```bash
docker network create web-network
```

### Step 2: Run `web1` (NGINX) and `web2` (HTTPD) Containers

Now, run both `web1` and `web2` containers on the same network:

```bash
# Run web1 container with NGINX
docker run -d --name web1 --network web-network -p 8081:80 nginx:latest

# Run web2 container with HTTPD
docker run -d --name web2 --network web-network -p 8082:80 httpd:latest
```

### Step 3: Create `haproxy.cfg` Configuration File

Create an HAProxy configuration file (`haproxy.cfg`) that routes traffic based on the hostnames `web1.id` and `web2.id`.

Here is the `haproxy.cfg`:

```cfg
global
    log stdout format raw local0
    maxconn 2048

defaults
    log global
    mode http
    option httplog
    option dontlognull
    timeout connect 5000ms
    timeout client  50000ms
    timeout server  50000ms

frontend http-in
    bind *:80
    # ACL (Access Control Lists) for hostnames
    acl host_web1 hdr(host) -i web1.id
    acl host_web2 hdr(host) -i web2.id

    # Use backend based on hostname
    use_backend web1_backend if host_web1
    use_backend web2_backend if host_web2

backend web1_backend
    server web1 web1:80 check

backend web2_backend
    server web2 web2:80 check
```

### Step 4: Run HAProxy Container with the Configuration

Run the HAProxy container, mounting the `haproxy.cfg` file into the container so that it uses this configuration:

```bash
docker run -d --name username-haproxy --network web-network -p 80:80 -v $(pwd)/haproxy.cfg:/usr/local/etc/haproxy/haproxy.cfg haproxy:alpine
```

### Step 5: Update `/etc/hosts` File

Since you are using custom hostnames (`web1.id` and `web2.id`), you'll need to modify your local `/etc/hosts` file (on Linux/macOS) or `C:\Windows\System32\drivers\etc\hosts` (on Windows) to map these hostnames to `localhost`.

Add the following lines to the file:

```
127.0.0.1 web1.id
127.0.0.1 web2.id
```

### Step 6: Test the Setup

Now, you should be able to access the containers using the following URLs:

- Visit `http://web1.id` in your browser, and it should route to `web1` (NGINX).
- Visit `http://web2.id` in your browser, and it should route to `web2` (HTTPD).

### Troubleshooting

1. **Check if containers are running**:
   ```bash
   docker ps
   ```
   Make sure `web1`, `web2`, and `username-haproxy` are all running.

2. **Logs for debugging**: If something doesn't work, check the HAProxy logs:
   ```bash
   docker logs username-haproxy
   ```

By following these steps, HAProxy should now correctly route requests based on the hostname: `web1.id` to the NGINX server and `web2.id` to the HTTPD server.
