# Lab 6.4: Install and Configure Portainer for Docker Swarm

In this lab, we will learn how to install and configure **Portainer**, a lightweight management tool for Docker that provides a simple and user-friendly web interface to manage your containers, services, volumes, and networks. Portainer also allows you to manage Docker Swarm clusters, which makes it easier to visualize and control multiple nodes.

By the end of this lab, you will:
1. Install Portainer on **node01**.
2. Connect **node02** to the Portainer dashboard using the Portainer agent.
3. Verify that the Portainer dashboard is running and all nodes are connected.

---

## Table of Contents
1. **Introduction**
2. **Step-by-Step Instructions**
    1. Installing Portainer on Node01
    2. Accessing the Portainer Dashboard
    3. Connecting Node02 to the Portainer Dashboard
3. **Verification**
4. **Conclusion**

---

## Introduction

**Portainer** is a popular open-source tool for managing Docker environments. It provides a web-based dashboard that simplifies Docker and Docker Swarm management. By installing Portainer, you can monitor and manage your Docker Swarm cluster more easily. In this lab, we will:
1. Install Portainer on `pod-[username]-node01`.
2. Set up **node02** as an agent in the Portainer dashboard to manage it alongside **node01**.

---

## Step-by-Step Instructions

### Step 1: Installing Portainer on Node01

We will start by installing Portainer on **node01**. Portainer runs as a Docker container, and we will expose its web interface on port 9000.

#### 1.1 Create a Docker Volume for Portainer

First, create a volume to store Portainer's persistent data:

```bash
docker volume create portainer_data
```

This volume will be used to store all Portainer-related data.

#### 1.2 Run the Portainer Container

Now, let's run the Portainer container:

```bash
docker run -d -p 8000:8000 -p 9000:9000 --name portainer --restart=always \
-v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest
```

Explanation:
- `-p 8000:8000 -p 9000:9000`: Exposes ports 8000 and 9000, where port 9000 is used for the web interface.
- `--name portainer`: Names the container `portainer`.
- `--restart=always`: Ensures the container always restarts if it stops.
- `-v /var/run/docker.sock:/var/run/docker.sock`: Mounts the Docker socket to allow Portainer to communicate with the Docker engine.
- `-v portainer_data:/data`: Mounts the `portainer_data` volume for persistent storage.

Portainer will now be running in a container, and you can access it using a web browser.

---

### Step 2: Accessing the Portainer Dashboard

#### 2.1 Open the Portainer Web Interface

To access the Portainer dashboard, open your browser and navigate to:

```
http://10.10.10.11:9000
```

This opens the Portainer web interface, where you can manage your Docker environment.

#### 2.2 Set Up Your Admin User

When you first access the dashboard, you will be prompted to create an admin user. Enter a username and password to secure your Portainer instance.

Once logged in, you will see the main Portainer dashboard.

---

### Step 3: Connecting Node02 to the Portainer Dashboard

Now that Portainer is running on **node01**, we will connect **node02** to the dashboard using the Portainer agent. This allows you to manage both nodes from the same Portainer interface.

#### 3.1 Add Node02 as a New Environment in Portainer

1. In the Portainer dashboard, click on the **Environments** menu.
2. Click **Add Environment**.
3. Select **Docker Swarm** and click **Start Wizard**.
4. Choose **Agent** as the connection method.
5. Portainer will provide a command to run on **node01** to deploy the Portainer agent.

#### 3.2 Run the Portainer Agent on Node02

Copy the command provided by the Portainer wizard, and then run the command on **node02** to install the Portainer agent.

Here’s an example of what the command might look like:

```bash
docker run -d -p 9001:9001 --name portainer_agent --restart=always \
-v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/docker/volumes:/var/lib/docker/volumes \
portainer/agent
```

This command:
- Runs the Portainer agent on **node02**.
- Exposes the agent on port 9001.
- Mounts the necessary Docker volumes and sockets to allow the agent to communicate with the Docker engine.

#### 3.3 Connect Node02 to the Portainer Dashboard

1. After running the agent on **node02**, go back to the Portainer dashboard on **node01**.
2. In the **Environment URL** field, enter the IP address of **node02** followed by port 9001. For example:

```
10.7.7.20:9001
```

3. In the **Name** field, enter a descriptive name for **node02**, such as `pod-[username]-node02`.
4. Click **Connect**.

---

### Step 4: Verifying the Connection

#### 4.1 Verify Node02 is Connected

Once the connection is complete, go back to the **Home** menu in the Portainer dashboard. You should see two environments:
- The local Docker environment (`pod-[username]-node01`).
- The remote agent environment (`pod-[username]-node02`).

#### 4.2 Check Node02 in the Dashboard

If everything is set up correctly, you will see **node02** listed as a connected environment, and you will be able to manage it alongside **node01**.

---

## Verification

To ensure that the lab is completed successfully, verify the following:
1. **Portainer Dashboard Access**: You can access the Portainer web interface by navigating to `http://10.10.10.11:9000` in your browser.
2. **Node02 Connection**: **node02** is connected as an agent in the Portainer dashboard, and you can see it listed in the **Home** menu.
3. **Manage Both Nodes**: You can manage both **node01** and **node02** from the same Portainer interface.

---

## Conclusion

In this lab, you learned how to:
1. Install Portainer on **node01** to manage your Docker Swarm.
2. Set up the Portainer agent on **node02** to manage it from the same Portainer dashboard.
3. Verify that both nodes are successfully connected and manageable from the Portainer interface.

Portainer makes Docker management easy by providing a graphical user interface (GUI) to interact with your Docker environments, making it a powerful tool for both beginners and advanced users.

### Example Portainer Dashboard Screenshot:

You should see something like this on the **Home** screen:

```
+-------------------------+-----------------+---------------------+
| pod-[username]-node01    | Docker Standalone |  Running            |
+-------------------------+-----------------+---------------------+
| pod-[username]-node02    | Docker Standalone |  Running            |
+-------------------------+-----------------+---------------------+
```

Congratulations on completing **Lab 6.4: Install and Configure Portainer**! You now have a fully operational Docker management interface to monitor and control your Swarm.

Happy Dockering!
