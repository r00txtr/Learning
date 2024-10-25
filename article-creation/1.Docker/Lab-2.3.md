# Lab 2.3: Mount Volume with Read-Only Mode in Docker

In this lab, we will learn how to manage Docker volumes with different access modes, focusing on mounting a volume with read-only access. Volumes in Docker are useful for persisting data and sharing it between containers, but controlling how containers can access those volumes (read-only or read-write) is important for managing data integrity. By the end of this lab, you will know how to create volumes, inspect them, and manage their access permissions in containers.

---

## Table of Contents
1. **Introduction**
2. **Step-by-Step Instructions**
    1. Create a Docker Volume
    2. Inspect the Docker Volume
    3. Running a Container with Read-Write Volume Access
    4. Testing Container Access
    5. Running a Container with Read-Only Volume Access
3. **Verification**

---

## Introduction

In Docker, volumes allow data to be shared and persisted across containers, and they can be mounted in different modes:
- **Read-Write** mode (`rw`): Containers can both read and write to the volume.
- **Read-Only** mode (`ro`): Containers can only read data from the volume, ensuring that data remains unchanged.

In this lab, we will create a Docker volume, mount it with read-write access, and then demonstrate mounting it with read-only access to understand how these modes affect container behavior.

---

## Step-by-Step Instructions

We will be working on the node `pod-[username]-node01`. Remember to replace `[username]` with your actual username and `[X]` with the specific IP address segment.

### 1. Create a Docker Volume

First, we need to create a Docker volume named `my-vol` that will be shared across containers:

```bash
docker volume create my-vol
```

This creates a volume named `my-vol`, which will be used to store data.

### 2. Display the List of Docker Volumes

To confirm that the volume has been created, list all available Docker volumes:

```bash
docker volume ls
```

You should see `my-vol` listed in the output.

### 3. Inspect the Docker Volume

To view detailed information about the volume, including its mount point and options, use the following command:

```bash
docker volume inspect my-vol
```

This will show the volume's details, such as the location where the data is stored on the host (`/var/lib/docker/volumes/my-vol/_data`).

---

### 4. Running a Container with Read-Write Volume Access

Now, let’s run a container using the `nginx` image and mount the `my-vol` volume in read-write mode.

```bash
docker run -d --name=nginx-rwvol -v my-vol:/usr/share/nginx/html -p 4005:80 nginx:latest
```

This command:
- Runs an `nginx` container in detached mode (`-d`).
- Mounts the `my-vol` volume at `/usr/share/nginx/html`, where Nginx serves its default web page.
- Exposes port `4005` on the host, allowing you to access the container’s web server.

### 5. Display the Container’s IP Address

To access the web server running in the container, you need to find its internal IP address:

```bash
docker inspect nginx-rwvol | jq -r '.[].NetworkSettings.IPAddress'
```

This command displays the IP address of the `nginx-rwvol` container.

### 6. Test Browsing to the Container’s IP Address

Once you have the container's IP address (let’s assume it’s `172.17.X.X`), use `curl` to test accessing the Nginx server:

```bash
curl http://172.17.X.X
```

You should see the default Nginx welcome page.

---

### 7. Create a New File in the Mounted Volume

We will now create a custom `index.html` file and place it inside the `my-vol` volume, which is mounted in the Nginx container.

```bash
sudo echo "This is from my-vol source directory" > index.html
sudo mv index.html /var/lib/docker/volumes/my-vol/_data
```

This creates a new `index.html` file with the content "This is from my-vol source directory" and moves it to the volume directory used by the container.

### 8. Test Browsing Again

Use `curl` to access the container’s web page again:

```bash
curl http://172.17.X.X
```

You should now see the content of the newly created `index.html` file, which reads:

```
This is from my-vol source directory
```

---

### 9. Running a Container with Read-Only Volume Access

Next, we will run a second Nginx container but this time mount the `my-vol` volume in **read-only** mode.

```bash
docker run -d --name=nginx-rovol -v my-vol:/usr/share/nginx/html:ro nginx:latest
```

In this case:
- The `my-vol` volume is mounted at `/usr/share/nginx/html` with read-only access (`:ro`).
- The container will be able to read from the volume but won’t be able to modify or write to it.

### 10. Inspect the Read-Only Container’s Mount Information

To verify that the volume is mounted in read-only mode, inspect the container and check the "Mounts" section:

```bash
docker inspect nginx-rovol | jq '.[].Mounts'
```

You should see something like this in the output:

```json
{
  "Type": "volume",
  "Name": "my-vol",
  "Source": "/var/lib/docker/volumes/my-vol/_data",
  "Destination": "/usr/share/nginx/html",
  "Mode": "ro",
  "RW": false
}
```

The `"Mode": "ro"` confirms that the volume is mounted in read-only mode.

---

## Verification

To ensure that everything is functioning correctly, perform the following checks:

1. **Check the Docker Volume**: Ensure the `my-vol` volume was created successfully by running `docker volume ls`.
2. **Check Content in the Read-Write Container**: Use `curl` to verify that the Nginx server in `nginx-rwvol` serves the custom `index.html` file with the content "This is from my-vol source directory".
3. **Inspect Read-Only Mount**: Check the details of the `nginx-rovol` container to verify that the volume is mounted as read-only by inspecting the "Mounts" section and confirming `"Mode": "ro"`.

---

## Conclusion

In this lab, you’ve learned how to:
- Create and inspect Docker volumes.
- Run containers with volumes mounted in read-write and read-only modes.
- Verify how containers interact with volumes and enforce access restrictions.

This is an essential skill for managing data integrity in Docker-based applications, ensuring that some containers can only read data without modifying it, while others can write and modify the data as needed.

### Screenshot Example

Here is an example of what the `docker inspect` output for the `nginx-rovol` container might look like:

```bash
"Mounts": [
  {
    "Type": "volume",
    "Name": "my-vol",
    "Source": "/var/lib/docker/volumes/my-vol/_data",
    "Destination": "/usr/share/nginx/html",
    "Driver": "local",
    "Mode": "ro",
    "RW": false,
    "Propagation": ""
  }
]
```

---

Congratulations! You’ve successfully completed **Lab 2.3: Mount Volume with Read-Only Mode**. You now have a solid understanding of how to control access to Docker volumes and how containers can interact with data stored in shared volumes.

Happy Dockering!
