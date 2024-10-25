## Step 5: Persist the Database in Docker

In previous steps, you might have noticed that your todo list resets every time you restart the container. This happens because containers are ephemeral by default, meaning any changes made inside them (like adding todo items) disappear when the container is stopped or removed.

In this section, you'll learn how to persist data across container restarts by using Docker volumes.

---

### 5.1 Understanding the Container Filesystem

Each container uses the layers of an image for its filesystem. Containers can create, update, and delete files, but these changes are isolated to the container and are lost when the container is removed or stopped.

---

### 5.2 Introduction to Docker Volumes

Docker volumes solve the problem of data loss by allowing you to persist specific directories on your host machine. When you mount a volume to a container, changes to that directory are reflected on the host and remain even after the container is removed. 

Volumes are useful for persisting data, like the SQLite database in your todo app, which is stored at `/etc/todos/todo.db` inside the container.

---

### 5.3 Persisting the Todo App Data

Your todo app uses an SQLite database located at `/etc/todos/todo.db`. By mounting a Docker volume to the `/etc/todos` directory, you can ensure that the data is stored on your host machine and persists between container restarts.

#### Step 1: Create a Docker Volume

To persist the todo data, you first need to create a Docker volume using the `docker volume create` command:

```bash
docker volume create todo-db
```

This command creates a volume named `todo-db` that Docker will manage.

#### Step 2: Stop and Remove the Current Container

Since the current todo app container isn't using the volume, you need to stop and remove it:

1. List running containers:

   ```bash
   docker ps
   ```

2. Stop and remove the container by ID:

   ```bash
   docker rm -f <container_id>
   ```

#### Step 3: Start a New Container with the Volume

Now, start a new container and mount the `todo-db` volume to `/etc/todos`, where the database is stored inside the container:

```bash
docker run -dp 127.0.0.1:3000:3000 --mount type=volume,src=todo-db,target=/etc/todos getting-started
```

- The `--mount` flag tells Docker to use the `todo-db` volume and mount it to the `/etc/todos` directory inside the container.
- The `src=todo-db` specifies the source volume name.
- The `target=/etc/todos` specifies where to mount the volume inside the container.

This ensures that any changes to `/etc/todos` inside the container (like new todo items) will be saved in the `todo-db` volume on the host machine.

---

### 5.4 Verify that Data Persists

1. Once the container starts, open the app in your browser by visiting `http://127.0.0.1:3000`.
2. Add a few items to your todo list and verify that they are saved.

#### Step 1: Stop and Remove the Container

Stop and remove the container again:

```bash
docker ps   # List running containers
docker rm -f <container_id>  # Stop and remove the container
```

#### Step 2: Start a New Container with the Volume

Start a new container using the same volume mount:

```bash
docker run -dp 127.0.0.1:3000:3000 --mount type=volume,src=todo-db,target=/etc/todos getting-started
```

#### Step 3: Check if Data Persists

After starting the container, open the app again. You should see the todo items you added earlier, demonstrating that the data persisted through the container restarts.

---

### 5.5 Inspecting the Volume

Docker automatically manages volumes, but you can inspect and view the data stored within them. To see where Docker stores the `todo-db` volume on your host machine, use the `docker volume inspect` command:

```bash
docker volume inspect todo-db
```

The output will look something like this:

```json
[
    {
        "CreatedAt": "2024-01-01T02:18:36Z",
        "Driver": "local",
        "Labels": {},
        "Mountpoint": "/var/lib/docker/volumes/todo-db/_data",
        "Name": "todo-db",
        "Options": {},
        "Scope": "local"
    }
]
```

- The **Mountpoint** field shows the location of the volume on your host machine (`/var/lib/docker/volumes/todo-db/_data`).
- This is where Docker stores the files inside the volume.

Note: You may need root access to access the volume directory on the host.

---

### Conclusion

By mounting a volume to your container, you can now persist the data between container restarts. Docker volumes are a powerful way to ensure data is retained even if the container is removed or re-created. You’ve now successfully persisted the todo app’s data using Docker volumes. In the next steps, you can explore other types of databases or more advanced Docker concepts like Docker Compose for managing multi-container applications.
