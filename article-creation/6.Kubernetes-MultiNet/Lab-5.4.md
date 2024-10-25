# Lab 5.4 - Exposing Metrics from Docker Containers

In this lab, we will guide you through the process of exposing Docker container metrics for monitoring purposes. By the end of this lab, you'll learn how to expose Docker and MySQL container metrics and visualize them using Prometheus. This is crucial for monitoring containerized applications in real-time.

We will cover:
1. Installing Docker.
2. Exposing Docker metrics.
3. Configuring Prometheus to scrape Docker and MySQL metrics.
4. Running MySQL and MySQL Exporter in Docker containers.
5. Verifying metrics with `curl` and a browser.

Let’s get started!

---

## Step 1: Installing Docker

The first step is to install Docker on **node1**, as it's required for running containers and exposing metrics.

### 1.1 Set Up Docker on Ubuntu

Execute the following commands to set up Docker:

1. Add Docker’s GPG key and repository:
   ```bash
   sudo install -m 0755 -d /etc/apt/keyrings
   sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
   sudo chmod a+r /etc/apt/keyrings/docker.asc
   ```

2. Add the Docker repository:
   ```bash
   echo \
   "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
   $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
   sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
   ```

3. Install Docker:
   ```bash
   sudo apt-get update
   sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin docker-compose -y
   ```

4. Allow your user to manage Docker:
   ```bash
   sudo chown $USER /var/run/docker.sock
   sudo usermod -aG docker $USER
   sudo systemctl restart docker
   ```

Docker is now installed and ready to be used.

---

## Step 2: Exposing Docker Metrics

After installing Docker, we need to expose the Docker metrics so that Prometheus can scrape them.

### 2.1 Edit the Docker Daemon Configuration

1. Open the Docker daemon configuration file:
   ```bash
   sudo vim /etc/docker/daemon.json
   ```

2. Add the following configuration to enable Docker metrics:
   ```json
   {
     "experimental": true,
     "metrics-addr": "172.16.7.10:9323"
   }
   ```

This configuration will expose Docker metrics on the IP `172.16.7.10` and port `9323`.

---

## Step 3: Adding Docker Job to Prometheus

Now that Docker is exposing metrics, we need to configure Prometheus to scrape these metrics.

### 3.1 Edit the Prometheus Configuration

1. Open the Prometheus configuration file:
   ```bash
   sudo vim /opt/prometheus-2.44.0.linux-amd64/config.yml
   ```

2. Add the following job for Docker metrics (replace `[username]` with your username):
   ```yaml
   scrape_configs:
     - job_name: 'docker-[username]'
       static_configs:
       - targets: ['172.16.7.10:9323']
   ```

### 3.2 Restart Docker and Prometheus

After adding the Docker job, restart Docker and Prometheus services:

```bash
sudo systemctl restart docker
sudo systemctl restart prometheus_server.service
```

---

## Step 4: Running MySQL in a Docker Container

Now let’s create a Docker network for MySQL and run MySQL in a container.

### 4.1 Create a Docker Network for MySQL

1. Add your user to the Docker group and create a network:
   ```bash
   sudo usermod -aG docker ${USER} 
   sudo chmod 666 /var/run/docker.sock
   docker network create db_network
   ```

### 4.2 Run MySQL in a Docker Container

Start a MySQL container with the following command:

```bash
docker run -d \
  --name mysql80 \
  --publish 3306 \
  --network db_network \
  --restart unless-stopped \
  --env MYSQL_ROOT_PASSWORD=katakunci \
  --volume mysql80-datadir:/var/lib/mysql \
  mysql:8
```

This command will run MySQL version 8 in a Docker container on the `db_network` with the root password set to `katakunci`.

---

## Step 5: Setting Up MySQL Exporter

To scrape MySQL metrics, we will run a MySQL exporter in another Docker container.

### 5.1 Create a Monitoring User in MySQL

First, create a user for the MySQL exporter:

```bash
docker exec -it mysql80 mysql -u root -p
```

When prompted, enter the password `katakunci`. Then run the following SQL commands:

```sql
CREATE USER 'exporter'@'%' IDENTIFIED BY 'katakunci' WITH MAX_USER_CONNECTIONS 3;
GRANT PROCESS, REPLICATION CLIENT, SELECT ON *.* TO 'exporter'@'%';
```

### 5.2 Run the MySQL Exporter

Now, start the MySQL exporter container:

```bash
docker run -d \
  --name mysql80-exporter \
  --publish 9104:9104 \
  --restart always \
  --network db_network \
  --env DATA_SOURCE_NAME="exporter:katakunci@(mysql80:3306)/" \
  prom/mysqld-exporter:v0.14.0 \
  --collect.info_schema.processlist \
  --collect.info_schema.innodb_metrics \
  --collect.info_schema.tablestats \
  --collect.info_schema.tables \
  --collect.info_schema.userstats \
  --collect.engine_innodb_status
```

The MySQL exporter will now expose MySQL metrics on port `9104`.

---

## Step 6: Accessing Metrics

With both Docker and MySQL metrics exposed, you can now access the metrics using `curl` or a browser.

### 6.1 Access Metrics Using `curl`

To access Docker metrics:

```bash
curl http://172.16.7.10:9323/metrics
```

To access MySQL metrics:

```bash
curl http://172.16.7.10:9104/metrics
```

### 6.2 Access Metrics Using a Browser

Open the following URLs in your browser to view the metrics:

- Docker: `http://10.10.10.11:9323/metrics`
- MySQL: `http://10.10.10.11:9104/metrics`

---

## Step 7: Verifying Docker and MySQL Containers in Prometheus

You can check the status of your Docker and MySQL containers in Prometheus.

### 7.1 Prometheus Query to Check Running Containers

To see the status of your running Docker containers, open Prometheus at `http://10.10.10.11:9090` and run the following query:

```promql
engine_daemon_container_states_containers{state="running"}
```

### 7.2 Prometheus Query to See Pulled Docker Images

To see how many Docker images have been pulled, run this query:

```promql
engine_daemon_image_actions_seconds_count{action="pull"}
```

---

## Step 8: Verification

Make sure that:
1. The `mysql80` and `mysql80-exporter` containers are running.
2. You can access MySQL metrics using both `curl` and your browser.
3. The Docker job (`docker-[username]`) appears on the Prometheus targets page at `http://10.10.10.11:9090/targets`.

---

## Conclusion

In this lab, you successfully exposed metrics from Docker containers and configured Prometheus to scrape them. You:
1. Installed Docker and configured it to expose metrics.
2. Ran MySQL and a MySQL exporter in Docker containers.
3. Added Docker and MySQL metrics as Prometheus targets.
4. Verified the metrics using `curl` and a web browser.

This setup is essential for monitoring containerized services effectively using Prometheus. Congratulations on completing the lab!