# Lab 2.4: Using Volume Driver with SSHFS in Docker

In this lab, we will explore how to use Docker volume drivers, specifically the **SSHFS driver**, to mount remote directories via SSH. This setup allows Docker containers to access data stored on remote machines as if they were local volumes, which is extremely useful in distributed environments.

By the end of this lab, you will understand how to:
1. Set up a remote shared directory.
2. Install and configure the **SSHFS volume driver**.
3. Create and use a Docker volume with the **SSHFS driver**.
4. Verify the setup by running a container with the mounted remote volume.

---

## Table of Contents
1. **Introduction**
2. **Setting Up the Shared Directory on Node 02**
3. **Installing and Configuring SSHFS Plugin on Node 01**
4. **Creating a Docker Volume with SSHFS Driver**
5. **Running a Container with SSHFS Volume**
6. **Verification**

---

## Introduction

We will be working across two nodes:
- `pod-[username]-node01` where Docker will run with the **SSHFS plugin**.
- `pod-[username]-node02` which will serve a remote shared directory accessible via SSH.

We will:
1. Set up a shared directory on **node02**.
2. Install and configure the **SSHFS plugin** on **node01**.
3. Create and use a Docker volume with the **SSHFS driver** to mount the remote directory into a container.
4. Verify that the setup is working by accessing the shared content in the container.

Remember to replace `[username]` with your username and `[X]` with the correct IP segment for your nodes.

---

## Step 1: Setting Up the Shared Directory on Node 02

We will first create a shared directory on **node02** that will be accessed remotely using SSHFS.

### 1.1 Create the Shared Directory

SSH into `pod-[username]-node02` and create a directory named `/share`:

```bash
sudo mkdir /share
sudo chmod 777 /share
```

- `mkdir /share`: Creates the `/share` directory.
- `chmod 777 /share`: Grants full read, write, and execute permissions to all users for the directory.

### 1.2 Create an `index.html` File in the Shared Directory

Next, create a simple `index.html` file inside the `/share` directory:

```bash
echo "Hello, saya sedang belajar Docker!" > /share/index.html
```

This creates an `index.html` file with the content "Hello saya sedang belajar Docker!" inside the `/share` directory. This file will be served by Nginx in a Docker container later.

---

## Step 2: Installing and Configuring SSHFS Plugin on Node 01

Now, switch to **node01** where we will install and configure the **SSHFS Docker plugin**.

### 2.1 Install the SSHFS Plugin

SSH into `pod-[username]-node01` and install the **SSHFS plugin** for Docker:

```bash
docker plugin install --grant-all-permissions vieux/sshfs
```

- `--grant-all-permissions`: Allows the plugin to access all necessary resources.

### 2.2 Verify the Plugin Installation

List the installed Docker plugins to verify that the **SSHFS** plugin was installed successfully:

```bash
docker plugin ls
```

You should see the **vieux/sshfs** plugin listed.

### 2.3 Configure the SSHFS Plugin

Next, configure the SSHFS plugin so that it can use your SSH keys for passwordless authentication between **node01** and **node02**.

First, disable the plugin:

```bash
docker plugin disable [PLUGIN ID]
```

Replace `[PLUGIN ID]` with the actual plugin ID of **vieux/sshfs** that you found in the previous step.

Then, set the plugin to use your SSH key for authentication:

```bash
docker plugin set vieux/sshfs sshkey.source=/home/student/.ssh/
```

Now, re-enable the plugin:

```bash
docker plugin enable [PLUGIN ID]
```

List the plugins again to ensure that the **SSHFS** plugin is enabled:

```bash
docker plugin ls
```

> **Note**: Ensure that `node01` can SSH into **node02** as the `student` user without requiring a password. This setup is essential for SSHFS to function correctly.

---

## Step 3: Creating a Docker Volume with SSHFS Driver

Now that the SSHFS plugin is installed and configured, we can create a Docker volume that mounts the shared directory on **node02**.

### 3.1 Create the Docker Volume with SSHFS Driver

Run the following command to create a Docker volume named `sshvolume` that mounts the `/share` directory from **node02**:

```bash
docker volume create --driver vieux/sshfs -o sshcmd=student@10.7.7.20:/share -o allow_other sshvolume
```

- `--driver vieux/sshfs`: Specifies that the SSHFS plugin should be used as the volume driver.
- `-o sshcmd=student@10.7.7.20:/share`: Specifies the SSH command to mount the `/share` directory from **node02**.
- `-o allow_other`: Allows access to the volume by other users.

---

## Step 4: Running a Container with SSHFS Volume

Now that we’ve created the volume, we will run an Nginx container that uses this volume to serve the `index.html` file from the `/share` directory on **node02**.

### 4.1 Run the Nginx Container with the SSHFS Volume

Run the following command to start an Nginx container with the `sshvolume` mounted at `/usr/share/nginx/html`:

```bash
docker run -d --name=nginxtest-sshfs -p 8090:80 -v sshvolume:/usr/share/nginx/html nginx:latest
```

This command:
- Runs the Nginx container in detached mode (`-d`).
- Mounts the `sshvolume` at `/usr/share/nginx/html`, which is where Nginx serves its web content.
- Exposes port `8090` on the host, allowing access to the Nginx web server.

### 4.2 Test the Nginx Server

To ensure the container is running properly, list the running Docker containers:

```bash
docker ps
```

Now, test the Nginx server by sending a `curl` request to `localhost` on port `8090`:

```bash
curl http://localhost:8090
```

If everything is set up correctly, you should see the output:

```
Hello, saya sedang belajar Docker!
```

This confirms that the `index.html` file in the `/share` directory on **node02** is being served by the Nginx container running on **node01**.

---

## Step 5: Verification

To verify that the lab was completed successfully, ensure the following:
1. The `/share` directory has been created on **node02** and contains the `index.html` file with the content `"Hello, saya sedang belajar Docker!"`.
2. The Docker volume `sshvolume` has been created with the **SSHFS driver**.
3. The Nginx container (`nginxtest-sshfs`) is running and serving the `index.html` file from the remote `/share` directory on **node02**.

### Example Output

When running `curl http://localhost:8090`, you should see the following output:

```
Hello, saya sedang belajar Docker!
```

This indicates that the remote volume is mounted and working correctly.

---

## Conclusion

In this lab, you’ve learned how to:
1. Set up a shared directory on a remote machine.
2. Install and configure the **SSHFS Docker plugin**.
3. Create and use a Docker volume with the **SSHFS driver** to mount remote directories into containers.
4. Verify that the remote directory is accessible through Docker containers.

This setup is useful in distributed environments where multiple systems need to share files via Docker containers, allowing you to leverage remote storage while maintaining local container infrastructure.

Happy Dockering!
