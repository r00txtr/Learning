# Lab 7.5: Running a Container from Portainer

In this lab, you will learn how to run a container using the **Portainer** web interface. Portainer provides a graphical way to manage your Docker containers, making it easy for you to deploy, inspect, and monitor containers without needing to use the command line. By the end of this lab, you will have successfully launched an Nginx container from the Portainer dashboard and verified that it is running correctly.

---

## Table of Contents
1. **Introduction**
2. **Step-by-Step Instructions**
    1. Accessing the Portainer Dashboard
    2. Running a Container from Portainer
    3. Verifying the Container is Running
    4. Inspecting and Managing the Container
3. **Verification**
4. **Conclusion**

---

## Introduction

**Portainer** is a web-based Docker management tool that allows you to manage containers, images, networks, and volumes in a visual interface. In this lab, you will:
1. Access the Portainer dashboard.
2. Use Portainer to create and run an Nginx container.
3. Verify that the container is running and accessible via a web browser.
4. Inspect logs, view container stats, and access the console for the container.

We will use `pod-[username]-node02` to deploy and run the container. Replace `[username]` with your username throughout the lab.

---

## Step-by-Step Instructions

### Step 1: Accessing the Portainer Dashboard

Before running a container, you need to access the Portainer dashboard, which is hosted on **node01**.

1. Open your web browser and navigate to the Portainer dashboard:
   ```
   http://10.10.10.11:9000
   ```
2. Log in using the credentials you set up in the previous lab.

---

### Step 2: Running a Container from Portainer

Now that you're logged into Portainer, you can deploy an Nginx container on **node02**.

#### 2.1 Navigate to Node02

1. From the Portainer **Home** screen, select **node02** as the target node where you will deploy the container.

#### 2.2 Create a New Container

1. Click on **Containers** in the left-hand menu.
2. Click **+ Add Container** to create a new container.

#### 2.3 Configure the Container

1. In the **Name** field, enter a name for the container: `[username]-web`. Replace `[username]` with your actual username (e.g., `irma-web`).
   
2. In the **Image** field, enter the image name:
   ```
   nginx:latest
   ```
   This will pull the latest version of the official Nginx image from Docker Hub.

3. Under **Publish a new network port**, click **Add Port**.
   
4. In the **Host** column, enter `8080` (this is the port on your host machine that will map to the container).

5. In the **Container** column, enter `80` (this is the port inside the container that Nginx will use).

#### 2.4 Deploy the Container

1. Make sure **node02** is selected as the target node under **Node**.
2. Click **Deploy the Container**.

After deploying the container, you should see a notification at the top-right corner of the dashboard indicating that the container was successfully created.

---

### Step 3: Verifying the Container is Running

#### 3.1 Check the Container Status

1. After the container is deployed, go to the **Containers** menu in Portainer.
2. You should see the `[username]-web` container listed as **running**.

#### 3.2 Access the Nginx Container

1. To verify that the Nginx container is working correctly, open your web browser and navigate to:
   ```
   http://10.10.10.12:8080
   ```
2. You should see the default Nginx welcome page, which confirms that the container is running and accessible on **node02**.

---

### Step 4: Inspecting and Managing the Container

Portainer offers several tools to inspect and manage running containers.

#### 4.1 View Container Logs

To view the container logs:
1. In the **Containers** menu, locate the `[username]-web` container.
2. Click **Logs** from the **Quick Actions** menu.
3. You can see logs from the running Nginx container here, which will help you debug any issues.

#### 4.2 Inspect the Container

To inspect the container:
1. Click **Inspect** from the **Quick Actions** menu.
2. This will display detailed information about the container, including its configuration, environment variables, and networking setup.

#### 4.3 View Container Stats

To view container resource usage (CPU and memory):
1. Click **Stats** from the **Quick Actions** menu.
2. This will show real-time resource usage for the container.

#### 4.4 Access the Container Console

To access the container console:
1. Click **Console** from the **Quick Actions** menu.
2. You can run commands inside the container directly from this console.

---

## Verification

To verify that the lab was completed successfully, check the following:
1. The **[username]-web** container is listed in the **Containers** menu in Portainer and is in the **running** state.
2. You can access the Nginx container from a web browser by navigating to `http://10.10.10.12:8080`.
3. You can view logs, inspect, and access the console of the running container from the Portainer dashboard.

---

## Conclusion

In this lab, you learned how to:
1. Access the **Portainer** dashboard and deploy a container from the web interface.
2. Run an Nginx container, map ports, and verify that it is running correctly.
3. Use Portainer’s tools to inspect, manage, and monitor the container.

Portainer provides a user-friendly interface that simplifies container management, making it easier to deploy and monitor Docker environments without needing to rely on the command line.

### Example Screenshot of Nginx:

When you navigate to `http://10.10.10.12:8080`, you should see the default Nginx welcome page:

```
Welcome to nginx!
If you see this page, the nginx web server is successfully installed and working.
```

Congratulations on completing **Lab 7.5: Running a Container from Portainer**! You now have a solid understanding of deploying and managing containers through a graphical interface.

Happy Dockering!
