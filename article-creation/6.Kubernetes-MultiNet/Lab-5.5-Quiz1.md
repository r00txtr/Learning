# Lab 5.5 - Quiz 1 - Configuring Targets on Prometheus Server

In this quiz, we will walk through the process of configuring Prometheus to scrape additional targets, including **MySQL Exporter** and **cAdvisor**, for container metrics. By the end of this quiz, you'll know how to configure Prometheus to monitor these new targets and validate that they are working properly.

We'll cover:
1. Installing **cAdvisor** to monitor container metrics.
2. Adding **MySQL Exporter** and **cAdvisor** as targets in Prometheus.
3. Configuring the scrape intervals.
4. Verifying that the metrics are being scraped correctly.

Let's begin!

---

## Step 1: Installing cAdvisor

**cAdvisor** (Container Advisor) is a tool that provides container resource usage and performance metrics. We'll install it on **node1** and configure it to expose metrics that Prometheus can scrape.

### 1.1 Installing cAdvisor

1. Run the following commands to install and configure cAdvisor:

   ```bash
   sudo docker run -d \
     --name=cadvisor \
     --publish=8080:8080 \
     --volume=/var/run/docker.sock:/var/run/docker.sock \
     --volume=/sys:/sys \
     --volume=/var/lib/docker/:/var/lib/docker:ro \
     --restart always \
     google/cadvisor:latest
   ```

   This command runs cAdvisor as a Docker container, exposing metrics on port **8080**.

2. Verify that cAdvisor is running:

   ```bash
   docker ps | grep cadvisor
   ```

You should see cAdvisor listed as a running container.

---

## Step 2: Adding MySQL Exporter and cAdvisor to Prometheus Targets

Now, we'll configure Prometheus to scrape metrics from both the **MySQL Exporter** and **cAdvisor**.

### 2.1 Open the Prometheus Configuration File

Open the Prometheus configuration file in a text editor:

```bash
sudo vim /opt/prometheus-2.44.0.linux-amd64/config.yml
```

### 2.2 Add MySQL Exporter and cAdvisor Targets

Add the following configurations to the `scrape_configs` section. Replace `[username]` with your username:

```yaml
scrape_configs:
  - job_name: 'mysql-[username]'
    scrape_interval: 5s
    static_configs:
    - targets: ['172.16.7.10:9104']

  - job_name: 'cadvisor-[username]'
    static_configs:
    - targets: ['172.16.7.10:8080']
```

#### Explanation:
- **job_name: 'mysql-[username]'**: Defines the target for the MySQL Exporter with a scrape interval of 5 seconds.
- **job_name: 'cadvisor-[username]'**: Defines the target for cAdvisor, which will expose container metrics on port **8080**.

### 2.3 Restart Prometheus

After updating the configuration, restart the Prometheus service:

```bash
sudo systemctl restart prometheus_server.service
```

---

## Step 3: Verifying the Configuration

### 3.1 Verify cAdvisor Metrics

You can access the cAdvisor metrics using `curl` or directly via a browser.

1. Access cAdvisor metrics using `curl`:

   ```bash
   curl http://172.16.7.10:8080/metrics
   ```

2. Alternatively, open the following URL in your browser:

   ```
   http://10.10.10.11:8080/metrics
   ```

If successful, you'll see a list of metrics related to container resource usage.

### 3.2 Verify Targets in Prometheus

Next, check that both the MySQL Exporter and cAdvisor targets are visible in the Prometheus UI.

1. Open the Prometheus targets page in your browser:

   ```
   http://10.10.10.11:9090/targets
   ```

2. Ensure that the following targets are listed:
   - `mysql-[username]`
   - `cadvisor-[username]`

### 3.3 Verifying Scrape Interval for MySQL Exporter

Ensure that the scrape interval for the `mysql-[username]` job is set to 5 seconds. You can check this by reviewing the `config.yml` file or by observing the scrape timings in the Prometheus UI.

---

## Step 4: Exploring Metrics in the Expression Browser

With MySQL Exporter and cAdvisor properly configured, you can now explore the available metrics.

### 4.1 Accessing the Prometheus Expression Browser

1. Open Prometheus’ expression browser at:

   ```
   http://10.10.10.11:9090/
   ```

2. In the expression browser, you can query metrics from the MySQL Exporter and cAdvisor jobs.

### 4.2 Example Queries

Here are some example queries to try:

- **MySQL Exporter Metrics**:
  - Query MySQL uptime:
    ```
    mysql_global_status_uptime
    ```

  - Query the number of MySQL connections:
    ```
    mysql_global_status_threads_connected
    ```

- **cAdvisor Metrics**:
  - Query running containers:
    ```
    container_tasks_state{state="running"}
    ```

  - Query CPU usage of a container:
    ```
    container_cpu_usage_seconds_total
    ```

You can explore additional metrics by typing `mysql` or `container` in the expression browser, which will auto-complete the available metric names.

---

## Verification

Before completing the quiz, make sure the following checks are successful:

1. **cAdvisor metrics** can be accessed at `http://10.10.10.11:8080/metrics` via a browser or `curl`.
2. The **Prometheus targets** page at `http://10.10.10.11:9090/targets` lists the `mysql-[username]` and `cadvisor-[username]` targets.
3. The scrape interval for the `mysql-[username]` job is set to **5 seconds**.

---

## Conclusion

In this quiz, you successfully:
1. Installed and configured cAdvisor to expose container metrics.
2. Added **MySQL Exporter** and **cAdvisor** as targets in Prometheus.
3. Configured the scrape interval for MySQL Exporter.
4. Verified the configuration using `curl` and the Prometheus UI.

With these steps, you have enhanced your monitoring setup to include container metrics, giving you more visibility into the performance and health of your containers. Great job!