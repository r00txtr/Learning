# Lab 2.2: Mount Volume with NFS Server in Docker

In this lab, we will set up a Network File System (NFS) using Docker and mount the NFS volume between two nodes. This allows multiple systems to share and access files through a network, which is especially useful in distributed environments. By the end of this lab, you will have a basic NFS server running in Docker, and you’ll learn how to access it from another node.

---

## Table of Contents
1. **Introduction**
2. **Setting Up NFS Server on Node 01**
    1. Create a Working Directory
    2. Create an NFS Server with Docker
3. **Setting Up NFS Client on Node 02**
    1. Install Necessary Packages
    2. Mount the NFS Volume
    3. Create Files to Test NFS Mount
4. **Verification**
5. **Conclusion**

---

## Introduction

Network File System (NFS) is a protocol that allows files to be shared across a network. In this lab, we will:
- Create an NFS server on one node using Docker.
- Mount the NFS storage to another node as a client.
- Verify that files created on the client node appear on the server node.

We will be executing these commands on two nodes:
- `pod-[username]-node01` (NFS server)
- `pod-[username]-node02` (NFS client)

Replace `[username]` with your actual username and `[X]` with the IP address of the second octet for your nodes.

---

## Step 1: Set Up the NFS Server on Node 01

We will begin by creating an NFS server using Docker on `pod-[username]-node01`.

### 1.1 Create a Working Directory

First, create a directory on your server that will act as the shared storage for the NFS server.

```bash
sudo mkdir -p /data/nfs-storage01/
```

This command creates a directory at `/data/nfs-storage01/`, where all files shared over the network will be stored.

### 1.2 Create the NFS Server Using Docker

Now, we will create the NFS server using Docker. This server will share the `/data/nfs-storage01` directory across the network.

```bash
docker run -itd --privileged --restart unless-stopped -e SHARED_DIRECTORY=/data -v /data/nfs-storage01:/data -p 2049:2049 mwar8205/nfs-server-alpine:12
```

Here’s what this command does:
- `-itd`: Runs the container interactively in detached mode (background).
- `--privileged`: Provides the container with extended privileges to handle NFS operations.
- `--restart unless-stopped`: Automatically restarts the container if it stops, unless you manually stop it.
- `-e SHARED_DIRECTORY=/data`: Sets an environment variable inside the container, specifying the shared directory.
- `-v /data/nfs-storage01:/data`: Mounts the host’s `/data/nfs-storage01` directory to the `/data` directory in the container.
- `-p 2049:2049`: Exposes port 2049, which is the default NFS port.

---

## Step 2: Set Up the NFS Client on Node 02

Next, we will configure the NFS client on `pod-[username]-node02` to mount the shared volume from the NFS server.

### 2.1 Install Necessary Packages

On the client node (`node02`), install the `nfs-client` package, which allows the system to access NFS shares:

```bash
ssh 10.7.7.20 (Your-IP-node02)
sudo apt install nfs-client -y
```

The command connects you to `node02` and installs the necessary NFS client tools.

### 2.2 Mount the NFS Volume

Now, mount the NFS volume from the server on `node02`. There are two ways to do this:

Option 1 (using `vers=4` for NFS version 4):

```bash
sudo mount -v -o vers=4,loud 10.7.7.10:/ /mnt
```

Option 2 (explicitly specifying NFSv4):

```bash
sudo mount -v -t nfs4 10.7.7.10:/ /mnt
```

Both commands mount the NFS directory from `10.7.7.10` (NFS server’s IP) to the `/mnt` directory on `node02`.

### 2.3 Create Files to Test the NFS Mount

After mounting the NFS directory, let’s create some test files to ensure the volume is working properly.

```bash
sudo touch /mnt/file1.txt
sudo touch /mnt/file2.txt
```

This will create two files, `file1.txt` and `file2.txt`, in the mounted `/mnt` directory, which corresponds to the shared directory on the NFS server.

After creating the files, exit the session:

```bash
exit
```

---

## Step 3: Verification on Node 01

Now, let’s go back to `node01` and verify that the files created on `node02` are visible in the NFS server's shared directory.

### 3.1 List Files in the Shared Directory

On `node01`, list the contents of the `/data/nfs-storage01/` directory to check if the files created on `node02` are present:

```bash
ls /data/nfs-storage01/
```

### Example Output:

```
file1.txt
file2.txt
```

If the files `file1.txt` and `file2.txt` appear in the output, the NFS setup is working correctly. The files created on `node02` are now shared and visible on `node01`.

---

## Conclusion

In this lab, you’ve successfully set up an NFS server using Docker and mounted its volume on another node. You learned how to:
1. Create an NFS server with Docker.
2. Mount the NFS volume from a client node.
3. Verify that the shared directory is accessible and synchronized between the two nodes.

### Screenshots

Here is a screenshot of what the output should look like after listing the files on `node01`:

```
$ ls /data/nfs-storage01/
file1.txt  file2.txt
```

This confirms that the files created on `node02` have been correctly shared with `node01`.

---

Congratulations on completing **Lab 2.2: Mount Volume with NFS Server**! You now have a good understanding of how to use Docker to create an NFS server and manage shared storage across multiple nodes.

Happy Dockering!
