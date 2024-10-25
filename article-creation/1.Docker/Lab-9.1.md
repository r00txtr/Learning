# Lab 9.1: Configuring Docker Storage Driver

In this lab, we will explore how to configure a Docker storage driver and verify the changes through a series of commands. Docker uses storage drivers to manage image and container layers. You can configure different storage drivers based on your needs. The **vfs** driver is one such driver that we will configure in this lab.

By the end of this lab, you will:
1. Modify Docker’s storage driver configuration.
2. Verify that Docker is using the new storage driver.
3. Pull an image and check how Docker stores it using the new storage driver.
4. Inspect the Docker file system and logs for a running container.

---

## Table of Contents
1. **Introduction**
2. **Step-by-Step Instructions**
    1. Configuring the Storage Driver
    2. Pulling an Image and Verifying Storage
    3. Inspecting Logs and File System Changes for a Running Container
3. **Verification**
4. **Conclusion**

---

## Introduction

Docker uses storage drivers to handle how images and containers are stored on the host machine. The **vfs** (Virtual File System) driver is a basic storage driver that doesn’t use advanced techniques like copy-on-write but instead duplicates files for each container. Although **vfs** is not commonly used in production, it's useful in specific situations, such as testing.

In this lab, we will:
1. Configure Docker to use the **vfs** storage driver.
2. Restart Docker and verify the new configuration.
3. Pull an image and examine the storage details.
4. Inspect logs and file system changes for a running container.

We will execute these steps on `pod-[username]-node01`. Replace `[username]` with your actual username where needed.

---

## Step-by-Step Instructions

### Step 1: Configuring the Storage Driver

#### 1.1 Edit the Docker Daemon Configuration

We will start by modifying the Docker configuration to set the storage driver to **vfs**.

1. Open the Docker configuration file (`daemon.json`) for editing:

   ```bash
   sudo vim /etc/docker/daemon.json
   ```

2. Add the following configuration to set the **vfs** storage driver:

   ```json
   {
     "storage-driver": "vfs"
   }
   ```

This tells Docker to use the **vfs** storage driver, which stores images and containers using full copies instead of layers.

#### 1.2 Restart the Docker Service

After editing the configuration, restart Docker to apply the changes:

```bash
sudo service docker restart
```

---

### Step 2: Verifying the Storage Driver and Pulling an Image

#### 2.1 Check Docker Info

To confirm that Docker is now using the **vfs** storage driver, check the Docker system information:

```bash
docker info
```

In the output, you should see the following under the **Storage Driver** section:

```
Storage Driver: vfs
```

#### 2.2 Pull an Image

Let’s pull an image from the Docker registry to ensure that Docker is storing images correctly with the **vfs** storage driver:

```bash
docker pull ubuntu:jammy
```

Docker will download the Ubuntu image from the registry and store it using the new storage driver.

---

### Step 3: Inspecting Storage and Logs for a Running Container

#### 3.1 Check Docker Directory

We can now inspect the Docker directory to see where the images and containers are stored using the **vfs** driver.

1. List the contents of the Docker directory for **vfs**:

   ```bash
   sudo ls -al /var/lib/docker/vfs/dir/
   ```

   This directory contains the files for the pulled images.

2. Check the size of the files stored in the **vfs** directory:

   ```bash
   sudo du -sh /var/lib/docker/vfs/dir/
   ```

This gives you an idea of how much space the images are using on the disk.

---

### Step 4: Inspecting Logs and File System Changes for a Running Container

Let’s run an Nginx container and inspect its logs and file system changes.

#### 4.1 Check Image History

First, check the history of the Nginx image:

```bash
docker history nginx:latest
```

This will show the layers and commands used to build the Nginx image.

#### 4.2 Run the Nginx Container

Next, run the Nginx container in detached mode, exposing port 80:

```bash
docker run -dit -p 80:80 --name nginx1 nginx
```

#### 4.3 Check File System Changes

We can check for changes in the container’s file system:

```bash
docker diff nginx1
```

This command shows the differences between the current container and its base image, highlighting any files that have been added, modified, or deleted.

#### 4.4 Inspect Container Logs

Now, let’s inspect the logs of the running Nginx container.

1. View detailed logs:

   ```bash
   docker logs --details nginx1
   ```

2. View logs with timestamps:

   ```bash
   docker logs --timestamps nginx1
   ```

3. View the regular logs:

   ```bash
   docker logs nginx1
   ```

These commands allow you to see the log output from the running container, which is essential for debugging and monitoring.

---

## Verification

To ensure that the lab was completed successfully, check the following:

1. **Storage Driver**: The Docker storage driver should be set to **vfs**. This can be confirmed using `docker info`.
2. **Docker Directory**: Verify that the pulled images are stored in the `/var/lib/docker/vfs/dir/` directory.
3. **Running Nginx Container**: Ensure that the Nginx container is running and accessible on port 80.
4. **Logs**: Verify that you can view the logs of the Nginx container using `docker logs`.

---

## Conclusion

In this lab, you learned how to:
1. Configure Docker to use the **vfs** storage driver.
2. Verify the storage configuration using `docker info`.
3. Pull an image and inspect how it is stored on the host system.
4. Run a container, check its file system changes, and view its logs.

Configuring and understanding storage drivers is crucial when working with Docker, especially in environments where performance and storage efficiency are important considerations.

### Example Output of `docker info`:

```
Storage Driver: vfs
```

Congratulations on completing **Lab 9.1: Configuring Docker Storage Driver**! You now have a better understanding of how Docker manages storage and logs.

Happy Dockering!
