# Lab 9.4: Docker Networking and Security

In this lab, you will focus on Docker networking and security. Docker provides robust networking models that allow containers to communicate, but isolating services and securing communication between containers is crucial in production environments. This lab will cover creating custom networks, securing containers using firewalls, and implementing Docker secrets for sensitive information.

By the end of this lab, you will:
1. Configure Docker bridge networks for better isolation.
2. Secure inter-container communication with firewall rules.
3. Use Docker secrets to manage sensitive data securely.
4. Implement TLS to encrypt container communication.

---

## Table of Contents
1. **Introduction**
2. **Step-by-Step Instructions**
   1. Create Custom Bridge Networks
   2. Secure Networks with Firewall Rules
   3. Manage Sensitive Data with Docker Secrets
   4. Implement TLS for Encrypted Communication
3. **Verification**
4. **Conclusion**

---

## Introduction

Docker networking is a core component that enables containers to communicate both with each other and the outside world. By default, Docker provides several network drivers, such as `bridge` and `overlay`, to manage container communication. However, customizing networks to isolate services and securing the network is essential, especially for production environments.

In this lab, we will:
1. Create custom Docker networks to isolate services.
2. Use firewall rules and `iptables` to restrict network access.
3. Secure sensitive data using Docker secrets.
4. Encrypt communication between containers using TLS.

---

## Step-by-Step Instructions

### Step 1: Create Custom Bridge Networks

#### 1.1 Create an Isolated Network

The first step is to create a custom bridge network to isolate your containers. By creating an isolated network, you can ensure that only certain containers are able to communicate with each other.

Run the following command to create a custom bridge network:

```bash
docker network create --driver bridge secure-net
```

Explanation:
- `--driver bridge`: Specifies that the network should use the bridge driver.
- `secure-net`: The name of your custom network.

#### 1.2 Run Containers on the Custom Network

Now, run two Nginx containers that will communicate only within the `secure-net` network:

```bash
docker run -d --name nginx1 --network secure-net nginx:latest
docker run -d --name nginx2 --network secure-net nginx:latest
```

Explanation:
- `--network secure-net`: Ensures that both containers are connected to the custom network.

Verify that the containers are connected to the custom network:

```bash
docker network inspect secure-net
```

This command will display details about the containers attached to the `secure-net` network.

---

### Step 2: Secure Networks with Firewall Rules

#### 2.1 Restrict Communication Using iptables

By default, Docker allows unrestricted communication between containers on the same network. To enhance security, you can restrict communication between containers using `iptables` rules.

To prevent access to container `nginx2` from external sources but allow `nginx1` to communicate with it, apply the following `iptables` rule:

```bash
iptables -A DOCKER-USER -i eth0 ! -s $(docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' nginx1) -d $(docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' nginx2) -j DROP
```

Explanation:
- This rule blocks access to `nginx2` from all sources except `nginx1`.

Test the rule by trying to access `nginx2` from another container not on the `secure-net` network.

---

### Step 3: Manage Sensitive Data with Docker Secrets

#### 3.1 Create a Docker Secret

Docker secrets allow you to securely store and manage sensitive information like passwords and API keys. Let's create a secret for a database password.

Run the following command to create a secret named `db_password`:

```bash
echo "MySecurePassword" | docker secret create db_password -
```

Explanation:
- `docker secret create db_password -`: Creates a secret called `db_password` with the provided value.

#### 3.2 Use Docker Secrets in a Service

Now, deploy a MySQL service and use the secret as the database password:

```bash
docker service create --name mysql --secret db_password -e MYSQL_ROOT_PASSWORD_FILE=/run/secrets/db_password mysql:latest
```

Explanation:
- `--secret db_password`: Injects the secret into the container.
- `MYSQL_ROOT_PASSWORD_FILE=/run/secrets/db_password`: Uses the secret as the root password for MySQL.

---

### Step 4: Implement TLS for Encrypted Communication

#### 4.1 Generate Certificates for TLS

To secure container communication using TLS, you will need to generate SSL certificates.

Use `openssl` to generate a self-signed certificate:

```bash
openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365 -nodes
```

Explanation:
- This command generates a self-signed certificate (`cert.pem`) and private key (`key.pem`).

#### 4.2 Run a Container with TLS

Now, run an Nginx container with the generated certificates:

```bash
docker run -d --name nginx-secure -v $(pwd)/cert.pem:/etc/nginx/cert.pem -v $(pwd)/key.pem:/etc/nginx/key.pem -p 443:443 nginx:latest
```

Explanation:
- `-v $(pwd)/cert.pem`: Mounts the certificate file into the container.
- `-p 443:443`: Exposes port 443 for HTTPS.

#### 4.3 Verify TLS

Access the Nginx container over HTTPS:

```bash
curl -k https://localhost
```

The `-k` flag allows `curl` to accept the self-signed certificate.

---

## Verification

To verify that the lab has been completed successfully, check the following:
1. Containers are running on a custom isolated network (`secure-net`).
2. Firewall rules are correctly restricting access between containers.
3. Docker secrets are securely injected into the MySQL service.
4. TLS is successfully implemented for encrypted communication.

---

## Conclusion

In this lab, you learned how to:
1. Create custom Docker networks to isolate container communication.
2. Enhance security by restricting access between containers using `iptables`.
3. Securely manage sensitive data with Docker secrets.
4. Encrypt container communication using TLS.

By understanding Docker networking and security best practices, you can better secure your Dockerized applications and ensure they are robust and production-ready.

---

### Example Output of `docker network inspect secure-net`:

```json
[
    {
        "Name": "secure-net",
        "Id": "7a0f7cf013a3d9c291f7a171cfd2fdb3b7bcace59e4b0f112b2bf56ab24e8c3e",
        "Scope": "local",
        "Driver": "bridge",
        "IPAM": {
            "Driver": "default",
            "Config": [
                {
                    "Subnet": "172.18.0.0/16",
                    "Gateway": "172.18.0.1"
                }
            ]
        },
        "Containers": {
            "nginx1": {
                "Name": "nginx1",
                "EndpointID": "74822dd23fa2db5c6296b53fc9bfa4c3bf7c9fcd7dbac68ae77b0c20f7c8d07d",
                "MacAddress": "02:42:ac:12:00:02",
                "IPv4Address": "172.18.0.2/16"
            },
            "nginx2": {
                "Name": "nginx2",
                "EndpointID": "898b44a8f16c0ec9073f92cbf452720ac3f857e539a4b11c7ef7b605c5f7338b",
                "MacAddress": "02:42:ac:12:00:03",
                "IPv4Address": "172.18.0.3/16"
            }
        }
    }
]
```

Congratulations on completing **Lab 9.4: Docker Networking and Security**! You now have a strong understanding of how to secure Docker networks and manage sensitive information.

Happy Dockering!