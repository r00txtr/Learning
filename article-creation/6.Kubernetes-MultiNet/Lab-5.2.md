# Lab 5.2 - Installing Prometheus Server on Linux

In this lab, we will guide you through the steps of installing and configuring **Prometheus Server** on a Linux machine. Prometheus is an open-source system monitoring and alerting toolkit that is perfect for tracking the health of your infrastructure and services.

By the end of this lab, you will:
1. Install Prometheus Server.
2. Configure Prometheus to scrape metrics from different targets.
3. Run Prometheus Server as a service.
4. Access Prometheus' web interface to monitor and visualize metrics.

Let's get started!

---

## Step-by-Step Instructions

### Step 1: Install Prometheus Server

We will install Prometheus on **node01**. Start by switching to the root user and downloading the Prometheus package.

#### 1.1 Switch to the root user and navigate to `/opt`

```bash
sudo su -
```

Navigate to the `/opt` directory:

```bash
cd /opt
```

#### 1.2 Download Prometheus

Download the latest version of Prometheus (v2.44.0 in this case):

```bash
wget https://github.com/prometheus/prometheus/releases/download/v2.44.0/prometheus-2.44.0.linux-amd64.tar.gz
```

#### 1.3 Extract the Tarball

Once downloaded, extract the tarball:

```bash
tar xvfz prometheus-2.44.0.linux-amd64.tar.gz
```

This will extract the Prometheus files into a directory called `prometheus-2.44.0.linux-amd64`.

---

### Step 2: Configure Prometheus

We now need to configure Prometheus by editing the `config.yml` file, which defines what targets Prometheus will scrape for metrics.

#### 2.1 Navigate to the Prometheus Directory

Change to the directory where Prometheus was extracted:

```bash
cd prometheus-2.44.0.linux-amd64/
```

#### 2.2 Edit the Configuration File

Open the `config.yml` file in your preferred text editor (we will use `vim` for this example):

```bash
vim config.yml
```

Update the configuration file as follows (replace `[username]` with your actual username):

```yaml
global:
  scrape_interval:     10s
  evaluation_interval: 15s

scrape_configs:
  - job_name: 'prometheus-[username]'
    static_configs:
    - targets: ['172.16.7.10:9090']

  - job_name: 'node-[username]'
    scrape_interval:  5s
    static_configs:
    - targets: ['172.16.7.10:9100','172.16.7.20:9100']
```

**Explanation**:
- `scrape_interval`: How often Prometheus scrapes metrics from the targets.
- `job_name`: Name of the scrape job (replace `[username]` with your username).
- `targets`: IP addresses and ports where Prometheus will collect metrics. In this case:
  - Prometheus itself (on port `9090`).
  - Node Exporter running on `172.16.7.10:9100` and `172.16.7.20:9100`.

Save the file and exit the editor.

---

### Step 3: Testing Prometheus Manually

Before running Prometheus as a service, let's test it by running it manually to ensure there are no configuration errors.

#### 3.1 Check the Configuration File

Run the `promtool` to check the syntax of your configuration file:

```bash
./promtool check config config.yml
```

If there are no errors, you will see an "OK" message.

#### 3.2 Run Prometheus and Check the Version

To see the available options for Prometheus, run:

```bash
./prometheus --help
```

To check the installed version of Prometheus, run:

```bash
./prometheus --version
```

#### 3.3 Start Prometheus Manually

To run Prometheus manually, use the following command:

```bash
./prometheus --config.file=/opt/prometheus-2.44.0.linux-amd64/config.yml
```

Prometheus will start, and the metrics will be available on port `9090`. However, running it manually this way will keep it running only in the foreground. Let’s now configure it to run as a background service.

---

### Step 4: Setting up Prometheus as a Service

To ensure Prometheus runs automatically in the background and starts on boot, we will configure it as a systemd service.

#### 4.1 Create the Prometheus Service File

Create a new service file for Prometheus in `/etc/systemd/system/`:

```bash
vim /etc/systemd/system/prometheus_server.service
```

Add the following content to define the Prometheus service:

```ini
[Unit]
Description=Prometheus Server

[Service]
User=root
ExecStart=/opt/prometheus-2.44.0.linux-amd64/prometheus --config.file=/opt/prometheus-2.44.0.linux-amd64/config.yml --web.external-url=http://0.0.0.0:9090/

[Install]
WantedBy=default.target
```

Save and exit the file.

#### 4.2 Reload systemd and Start Prometheus

After creating the service file, reload systemd to apply the changes:

```bash
systemctl daemon-reload
```

Now, enable and start the Prometheus service:

```bash
systemctl enable prometheus_server.service
systemctl start prometheus_server.service
```

#### 4.3 Verify Prometheus Service Status

To check if the Prometheus service is running correctly, run the following command:

```bash
systemctl status prometheus_server.service
```

You should see an output indicating that the service is active and running.

#### 4.4 Check Logs

To view the logs for Prometheus, you can use the `journalctl` command:

```bash
journalctl -u prometheus_server
```

This will show you the logs generated by Prometheus.

---

### Step 5: Accessing Prometheus Web Interface

Once Prometheus is running as a service, you can access its web interface from your browser. Open the following URL (replace `10.10.10.11` with your actual server IP address):

```
http://10.10.10.11:9090/
```

This will bring you to the Prometheus Expression Browser, where you can query metrics and monitor your targets.

---

### Step 6: Running Prometheus Queries

With Prometheus up and running, you can now run some basic queries to monitor your system.

#### 6.1 Querying Node Status

To check if the nodes are up, use the following query:

```bash
up{job="node-[username]"}
```

This will display the status of your nodes.

#### 6.2 Querying the Number of Active Instances

To check how many instances are active, run this query:

```bash
sum(up{job="node-[username]"})
```

#### 6.3 Checking Uptime of Nodes

To view the uptime (in hours) of the node at `172.16.7.10`, run the following query:

```bash
(time() - process_start_time_seconds{instance="172.16.7.10:9100"}) / 3600
```

For the node at `172.16.7.20`, use this query:

```bash
(time() - process_start_time_seconds{instance="172.16.7.20:9100"}) / 3600
```

#### 6.4 Querying Available Memory

To display the available memory on your nodes in MB, use this query:

```bash
node_memory_MemAvailable_bytes/1024/1024
```

#### 6.5 Querying Memory Usage as a Percentage

To view the percentage of memory being used, you can run the following query:

```bash
100 * (1 - ((avg_over_time(node_memory_MemFree_bytes[10m]) + avg_over_time(node_memory_Cached_bytes[10m]) + avg_over_time(node_memory_Buffers_bytes[10m])) / avg_over_time(node_memory_MemTotal_bytes[10m])))
```

---

## Step 7: Verifying the Setup

After setting up Prometheus, ensure the following:

- The Prometheus service is running correctly using `systemctl status prometheus_server.service`.
- You can access the Prometheus web interface at `http://10.10.10.11:9090/`.
- The `prometheus-[username]` and `node-[username]` targets are visible under `http://172.16.7.10:9090/targets`.

---

## Conclusion

In this lab, you successfully installed and configured **Prometheus Server** on your Linux machine. We covered how to:
1. Install Prometheus.
2. Configure it to scrape metrics from your targets.
3. Run it as a background service.
4. Access the Prometheus web interface and run queries to monitor your system.

With Prometheus in place, you are now ready to monitor your infrastructure effectively. Happy monitoring!