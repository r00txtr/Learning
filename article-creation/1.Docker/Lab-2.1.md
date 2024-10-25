# Lab 2.1: Mount Volume in Docker

In this lab, we will explore how to use Docker volumes to manage and persist data in containers. Volumes are an important concept in Docker because they allow containers to share data with each other or with the host machine. By the end of this lab, you will have a clear understanding of how to create, mount, and work with volumes in Docker containers.

---

## Table of Contents
1. **Introduction**
2. **Creating a Working Directory**
3. **Creating Files and Data Volume Container**
4. **Mounting the Volume in a New Container**
5. **Verification**

---

## Introduction

When working with Docker, volumes are a powerful way to persist data outside of the container's filesystem, which is ephemeral by default. In this lab, we will:
1. Create a data volume in one container.
2. Mount that volume to another container and share data between them.

We will perform this lab on the node `pod-[username]-node01`.

---

## Step 1: Create a Working Directory

We will first set up a working directory on your host system, which will be used to create files and manage data.

### 1.1 Navigate to the Home Directory

Start by navigating to your home directory:

```bash
cd $HOME
```

### 1.2 Create a Directory for the Lab

Next, create a folder structure for the lab tasks:

```bash
mkdir -p latihan/latihan01-volume
```

The `-p` flag ensures that all directories in the path are created if they don't already exist.

### 1.3 Change Directory to the New Folder

Move into the newly created directory:

```bash
cd latihan/latihan01-volume
```

---

## Step 2: Create Files and a Data Volume Container

Next, we will create some files and a Docker container that holds a data volume.

### 2.1 Create Files

Using a loop, we will create 10 files named `file-1`, `file-2`, ..., `file-10` in the current directory:

```bash
for i in {1..10}; do touch file-$i; done
```

This command will create 10 empty files in the `latihan01-volume` directory.

### 2.2 Create a Data Volume Container

We will now create a Docker container with a volume. This container won't be used to run any processes but will store the data.

```bash
sudo docker create -v /my-volume --name my-volume busybox
```

- `-v /my-volume`: This creates a volume mounted at `/my-volume` inside the container.
- `--name my-volume`: This names the container `my-volume`.
- `registry.adinusa.id/btacademy/busybox`: We are using the `busybox` image from the registry, which is a lightweight container used for minimal tasks.

### 2.3 Copy Files into the Volume

Next, we copy the files created in the current directory to the container’s `/my-volume` directory:

```bash
sudo docker cp . my-volume:/my-volume
```

This command copies all files from the current directory (`.`) to the `/my-volume` directory inside the `my-volume` container.

---

## Step 3: Mount the Volume in a New Container

Now that we have created the `my-volume` container, we will mount this volume into a new container and access the files stored in it.

### 3.1 Run a New Container and Mount the Volume

To mount the volume from the `my-volume` container into a new container, use the following command:

```bash
docker run --volumes-from my-volume ubuntu ls /my-volume
```

- `--volumes-from my-volume`: This mounts the volume from the `my-volume` container.
- `registry.adinusa.id/btacademy/ubuntu`: We are running the new container using the Ubuntu image.
- `ls /my-volume`: This lists the contents of the `/my-volume` directory inside the new container, which should include the files we created earlier.

### Example Output:

```
file-1
file-2
file-3
file-4
file-5
file-6
file-7
file-8
file-9
file-10
```

You should see the 10 files you created in step 2.1.

---

## Verification

To verify that the lab was completed successfully, ensure the following:

1. The `my-volume` container has been created with the volume mounted at `/my-volume`.
2. The files created in the `latihan01-volume` directory have been successfully copied into the `my-volume` container.
3. When mounting the volume in a new container (using the `--volumes-from` option), you are able to list and access the files stored in `/my-volume`.

By following these steps, you have successfully created and mounted a Docker volume. This is a fundamental skill when working with Docker, as volumes allow you to share data between containers and persist data even after containers are stopped.

---

### Screenshots

Here’s an example screenshot of what the terminal should look like after running the volume mount command:

```
$ docker run --volumes-from my-volume registry.adinusa.id/btacademy/ubuntu ls /my-volume
file-1
file-2
file-3
file-4
file-5
file-6
file-7
file-8
file-9
file-10
```

---

Congratulations! You have successfully completed **Lab 2.1: Mount Volume in Docker**. You’ve learned how to create and mount Docker volumes, which are essential for sharing and persisting data in containerized applications.

Happy Dockering!
