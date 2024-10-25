# Lab 3.2: Understanding Docker's Host Network

In this lab, we will explore Docker’s **host network mode**. Unlike the default bridge network, where each container gets its own network namespace, the **host network** mode allows containers to share the host’s network stack. This can be useful in certain scenarios where network performance is critical, or when a container needs direct access to the host's network interfaces.

By the end of this lab, you will understand how to:
1. Run a Docker container with the host network.
2. Test and verify network connectivity.
3. Check the IP address and network ports to see how the host network affects container behavior.

---

## Table of Contents
1. **Introduction**
2. **Running a Container with the Host Network**
3. **Testing Connectivity to the Host**
4. **Verifying IP Address and Port Binding**
5. **Conclusion**

---

## Introduction

The **host network** mode in Docker allows a container to share the same network namespace as the host. This means that the container does not get its own IP address or separate network interface. Instead, it uses the host's network interfaces directly. In practice, this means:
- The container will have the same IP address as the host.
- The container can use the same ports as the host, with no need for port mapping.

We will demonstrate this by running an Nginx container using the host network, testing access to the Nginx server on the host, and verifying network behavior.

We will be executing commands on `pod-[username]-node01`. Replace `[username]` with your actual username.

---

## Step 1: Running a Container with the Host Network

We will start by running a container from the **Nginx** image using the **host network** mode.

### 1.1 Run the Nginx Container with Host Network

Run the following command to start the Nginx container with the host network:

```bash
docker run -itd --network host --name my_nginx nginx
```

Let’s break down this command:
- `-itd`: Runs the container in interactive, detached mode (in the background).
- `--network host`: Uses the host network mode, meaning the container will share the host’s network stack.
- `--name my_nginx`: Names the container `my_nginx`.
- `registry.adinusa.id/btacademy/nginx`: Specifies the Nginx image from the Docker registry.

By using the **host** network, the container will now use the same network interfaces as the host system.

### 1.2 Verify the Container is Running

You can verify that the container is running by listing active containers:

```bash
docker ps
```

You should see the `my_nginx` container running.

---

## Step 2: Testing Connectivity to the Host

Now that the Nginx container is running with the host network, we can check if Nginx is accessible on the host system's IP address.

### 2.1 Test Browsing to Localhost

Since the container is sharing the host’s network stack, we can access the Nginx server through the host’s IP address or `localhost`. Use `curl` to test if Nginx is serving content on `localhost`:

```bash
curl http://localhost
```

You should see the default Nginx welcome page in the output:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
...
</html>
```

This indicates that the Nginx server inside the container is accessible on the host’s network interface.

---

## Step 3: Verifying IP Address and Port Binding

Next, let’s check how the container is interacting with the host’s network by looking at its IP address and verifying which ports are in use.

### 3.1 Verify the Host IP Address

To see the host’s IP addresses, run the following command:

```bash
ip add
```

This command will show all the network interfaces and their associated IP addresses on the host. Since the container is using the host network, it does not have its own separate IP address.

### 3.2 Check Bound Ports

We will now check which process is using port `80` on the host. Since the Nginx container is using the host network, it should have bound to port `80` on the host.

Run the following command to see which service is using port `80`:

```bash
sudo netstat -tulpn | grep :80
```

This command shows all services listening on port `80`. You should see that the Nginx server from the Docker container is bound to port `80`.

Example output:

```
tcp        0      0 0.0.0.0:80           0.0.0.0:*               LISTEN      12345/nginx: master
```

Here, `12345` is the process ID (PID) of the Nginx process running inside the container.

---

## Conclusion

In this lab, you have learned how to:
1. Run a Docker container using the **host network** mode, which allows the container to share the host’s network stack.
2. Test connectivity to the container by accessing the Nginx server using `localhost`.
3. Verify network behavior by checking the IP address and port bindings on the host.

### Key Takeaways:
- The **host network** mode is useful for applications that require high network performance or direct access to the host’s network interfaces.
- Containers running in the host network do not have their own IP addresses and instead share the host’s IP address.
- The container can bind directly to ports on the host, without needing port mapping.

By understanding Docker's host network mode, you can better manage network resources and optimize container performance for specific use cases.

Happy Dockering!
