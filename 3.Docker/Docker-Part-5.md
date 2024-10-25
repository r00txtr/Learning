### Step 7: Multi-Container Apps

In this step, you will learn how to run multi-container applications by adding a MySQL container to your todo app. Following Docker's best practices, each container should handle a single responsibility. This allows for better scaling, versioning, and deployment flexibility, especially when transitioning from development environments to production.

---

### 7.1 Reasons for Running Separate Containers

Some reasons for running MySQL in its own container rather than bundling it with the app include:

- **Scaling**: You might need to scale the API/front-end differently from the database.
- **Versioning & Updates**: Separate containers allow for independent updates and version control.
- **Production-Readiness**: You may use a managed database service in production but use a containerized database for development.
- **Process Management**: Running multiple processes in one container requires a process manager, which adds complexity.

---

### 7.2 Container Networking

By default, containers are isolated and don't know about other containers. To enable communication between containers, you need to connect them to the same Docker network.

---

### 7.3 Starting MySQL

#### Step 1: Create a Docker Network

First, create a network that both your app and MySQL will share:

```bash
docker network create todo-app
```

#### Step 2: Start the MySQL Container

Now, start a MySQL container, attach it to the `todo-app` network, and set up environment variables for the database:

```bash
docker run -d \
    --network todo-app --network-alias mysql \
    -v todo-mysql-data:/var/lib/mysql \
    -e MYSQL_ROOT_PASSWORD=secret \
    -e MYSQL_DATABASE=todos \
    mysql:8.0
```

- `--network todo-app`: Connects the container to the `todo-app` network.
- `--network-alias mysql`: Gives the container the alias `mysql`, allowing other containers on the network to reference it by this name.
- `-v todo-mysql-data:/var/lib/mysql`: Binds a named volume to persist MySQL data.
- `-e MYSQL_ROOT_PASSWORD=secret`: Sets the root password for MySQL.
- `-e MYSQL_DATABASE=todos`: Creates a database named `todos`.

#### Step 3: Verify MySQL is Running

To ensure that MySQL is running and the database is set up, connect to the MySQL shell:

```bash
docker exec -it <mysql-container-id> mysql -u root -p
```

Enter the password (`secret`), and in the MySQL shell, list the databases:

```sql
SHOW DATABASES;
```

You should see the following:

```
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| todos              |
+--------------------+
```

Exit the MySQL shell:

```sql
exit
```

Now, MySQL is ready to be used by your application.

---

### 7.4 Understanding Container Networking

To understand how container networking works and to troubleshoot, you can use the `nicolaka/netshoot` container, which comes with useful networking tools.

#### Step 1: Start a Netshoot Container

Run a Netshoot container on the same network:

```bash
docker run -it --network todo-app nicolaka/netshoot
```

#### Step 2: Use the `dig` Command to Check MySQL's IP Address

Inside the Netshoot container, use the `dig` tool to resolve the IP address of the `mysql` alias:

```bash
dig mysql
```

You will see output like this:

```
;; ANSWER SECTION:
mysql.			600	IN	A	172.23.0.2
```

This shows that the `mysql` alias is mapped to the IP address of the MySQL container. Any container on the `todo-app` network can connect to MySQL using this alias.

---

### 7.5 Running the Todo App with MySQL

Now that MySQL is up and running, you can configure your app to connect to it by using environment variables.

#### Step 1: Start the App Container

Run your app and specify the MySQL connection details using environment variables:

```bash
docker run -dp 127.0.0.1:3000:3000 \
  -w /app -v "$(pwd):/app" \
  --network todo-app \
  -e MYSQL_HOST=mysql \
  -e MYSQL_USER=root \
  -e MYSQL_PASSWORD=secret \
  -e MYSQL_DB=todos \
  node:18-alpine \
  sh -c "yarn install && yarn run dev"
```

Explanation:
- `--network todo-app`: Connects the app to the same network as MySQL.
- `-e MYSQL_HOST=mysql`: Sets the hostname of the MySQL server (i.e., the network alias `mysql`).
- `-e MYSQL_USER=root`: Specifies the MySQL user.
- `-e MYSQL_PASSWORD=secret`: Specifies the MySQL password.
- `-e MYSQL_DB=todos`: Specifies the database to use.
  
Check the container logs to ensure the app is connected to the MySQL database:

```bash
docker logs -f <container-id>
```

You should see logs indicating that the app has successfully connected to MySQL:

```
Connected to mysql db at host mysql
Listening on port 3000
```

---

### 7.6 Testing the App with MySQL

Open your app in the browser at `http://localhost:3000` and add a few items to your todo list.

#### Step 1: Verify Data in MySQL

Connect to the MySQL container to check that your todo items are being saved to the database:

```bash
docker exec -it <mysql-container-id> mysql -p todos
```

Inside the MySQL shell, query the `todo_items` table:

```sql
SELECT * FROM todo_items;
```

You should see the todo items you added:

```
+--------------------------------------+--------------------+-----------+
| id                                   | name               | completed |
+--------------------------------------+--------------------+-----------+
| c906ff08-60e6-44e6-8f49-ed56a0853e85 | Do amazing things! |         0 |
| 2912a79e-8486-4bc3-a4c5-460793a575ab | Be awesome!        |         0 |
+--------------------------------------+--------------------+-----------+
```

This confirms that the app is successfully interacting with MySQL to store and retrieve data.

---

### Conclusion

In this chapter, you learned how to:
- Set up and run a multi-container app using Docker.
- Use Docker networks to allow containers to communicate.
- Connect a Node.js app to a MySQL database.
- Use environment variables to configure database connections.

By using separate containers for the app and database, you're following best practices that allow for better scalability, flexibility, and maintainability. This setup mimics a production environment where you might use managed database services instead of running the database locally.
