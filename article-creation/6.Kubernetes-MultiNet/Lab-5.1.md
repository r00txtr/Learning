# Lab 5.1 - Installing and Configuring Node Exporter on Linux

In this lab, we will learn how to install and configure **Node Exporter** on Linux, which is an essential tool for monitoring system metrics using **Prometheus**. Node Exporter collects hardware and OS-level metrics such as CPU, memory, disk, and network usage, making it a powerful tool for monitoring the health of your servers.

By the end of this lab, you will:
1. Download and install Node Exporter.
2. Run Node Exporter manually and as a background service.
3. Verify that metrics are accessible through curl and a web browser.

Let's dive in!

---

## Step-by-Step Instructions

### Step 1: Install Node Exporter

We will begin by downloading and installing Node Exporter on all nodes in your cluster. Follow the steps below on each node.

#### 1.1 Switch to the root user and navigate to `/opt`

To simplify installation and ensure proper permissions, switch to the root user:

```bash
sudo su -
```

Navigate to the `/opt` directory, where we will download and install Node Exporter:

```bash
cd /opt
```

#### 1.2 Download Node Exporter

Next, download the Node Exporter package (version 1.6.0 in this case) using `wget`:

```bash
wget https://github.com/prometheus/node_exporter/releases/download/v1.6.0/node_exporter-1.6.0.linux-amd64.tar.gz
```

#### 1.3 Extract the Tarball

Once the download is complete, extract the tarball using the `tar` command:

```bash
tar xvfz node_exporter-1.6.0.linux-amd64.tar.gz
```

This will extract the Node Exporter files into the `node_exporter-1.6.0.linux-amd64` directory.

---

### Step 2: Running Node Exporter Manually

In this step, we will run Node Exporter manually and verify it is working before setting it up as a service.

#### 2.1 Change to the Node Exporter Directory

Move into the directory where Node Exporter was extracted:

```bash
cd node_exporter-1.6.0.linux-amd64
```

#### 2.2 Run Node Exporter with the Help Command

To learn about the available options for Node Exporter, run the following command:

```bash
./node_exporter --help
```

You will see detailed information about the available flags and options for configuring Node Exporter.

#### 2.3 Check Node Exporter Version

To confirm the version of Node Exporter installed, you can run:

```bash
./node_exporter --version
```

You should see an output indicating that version 1.6.0 is installed.

#### 2.4 Run Node Exporter

Now, let's run Node Exporter in the foreground (it will output logs to the terminal):

```bash
./node_exporter
```

This will start Node Exporter and expose the metrics on port **9100**.

#### Important Note:

Running Node Exporter this way will not put it in the background, meaning if you close this terminal session, Node Exporter will stop running. To test its metrics, open a new terminal window and leave this process running.

---

### Step 3: Accessing Node Exporter Metrics

#### 3.1 Using `curl`

Once Node Exporter is running, you can access the metrics it collects using `curl`. Replace `172.16.7.10` and `172.16.7.20` with your server IP addresses:

```bash
curl http://172.16.7.10:9100/metrics
curl http://172.16.7.20:9100/metrics
```

You should see a long list of system metrics in response to this command.

#### 3.2 Using a Web Browser

You can also access these metrics via a web browser. Simply open the following URL (replace `10.10.10.11` and `10.10.10.12` with your server IPs) in your browser:

```
http://10.10.10.11:9100/metrics
http://10.10.10.12:9100/metrics
```

This page will display the metrics Node Exporter collects in a readable format.

---

### Step 4: Running Node Exporter as a Service

To ensure Node Exporter runs automatically in the background and starts on boot, we will configure it as a systemd service.

#### 4.1 Create the Node Exporter Service File

We need to create a new service file for Node Exporter under `/etc/systemd/system/`. Open the file in your preferred text editor (e.g., `vim`):

```bash
vim /etc/systemd/system/node_exporter.service
```

Add the following content to define the service:

```ini
[Unit]
Description=Node Exporter

[Service]
User=root
ExecStart=/opt/node_exporter-1.6.0.linux-amd64/node_exporter

[Install]
WantedBy=default.target
```

#### 4.2 Reload systemd and Start the Service

After creating the service file, reload the systemd configuration to apply the changes:

```bash
systemctl daemon-reload
```

Next, enable and start the Node Exporter service so it starts automatically and runs in the background:

```bash
systemctl enable --now node_exporter.service
```

#### 4.3 Verify the Status of the Node Exporter Service

To check if Node Exporter is running correctly as a service, run the following command:

```bash
systemctl status node_exporter.service
```

You should see an output indicating that the service is active and running.

#### 4.4 Check Logs

To view the logs for Node Exporter, you can use the `journalctl` command:

```bash
journalctl -u node_exporter
```

This will show the logs generated by the Node Exporter service.

---

### Step 5: Verifying Node Exporter Metrics

After setting up Node Exporter as a service, verify that it is collecting metrics properly. You can check the metrics using `curl`, as we did in Step 3, or via a web browser.

```bash
curl http://172.16.7.10:9100/metrics
```

The metrics should be available and accessible just like when we ran Node Exporter manually.

---

## Conclusion

In this lab, you successfully installed and configured **Node Exporter** on your Linux machine. We covered how to:
1. Download and install Node Exporter.
2. Run Node Exporter manually to verify it works.
3. Set up Node Exporter as a service using `systemd`.
4. Verify that metrics are being collected and can be accessed via `curl` or a web browser.

With Node Exporter set up, you're now ready to monitor system-level metrics with Prometheus. Congratulations on completing this lab!