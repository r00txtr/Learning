### Step 8: Use Docker Compose

Docker Compose is a powerful tool that allows you to define and run multi-container applications. You can define your app’s services, networks, and volumes in a single `compose.yaml` file, making it easier to manage complex applications. With Docker Compose, you can launch or stop all your containers with one command, which is perfect for local development and testing.

---

#### Create the Compose File

1. **Navigate to the app directory**: Inside the `getting-started-app` directory, create a new file named `compose.yaml`.

   ```
   ├── getting-started-app/
   │   ├── Dockerfile
   │   ├── compose.yaml
   │   ├── node_modules/
   │   ├── package.json
   │   ├── spec/
   │   ├── src/
   │   └── yarn.lock
   ```

2. **Defining the App Service**: First, define the application service in the `compose.yaml` file. Begin by specifying the image, command, ports, working directory, volumes, and environment variables for the app.

```yaml
services:
  app:
    image: node:18-alpine
    command: sh -c "yarn install && yarn run dev"
    ports:
      - 127.0.0.1:3000:3000
    working_dir: /app
    volumes:
      - ./:/app
    environment:
      MYSQL_HOST: mysql
      MYSQL_USER: root
      MYSQL_PASSWORD: secret
      MYSQL_DB: todos
```

3. **Defining the MySQL Service**: Next, define the MySQL service. This includes specifying the image, volumes, and environment variables for the MySQL container.

```yaml
  mysql:
    image: mysql:8.0
    volumes:
      - todo-mysql-data:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: secret
      MYSQL_DATABASE: todos
```

4. **Defining Volumes**: The volumes section at the bottom defines a persistent volume for MySQL data.

```yaml
volumes:
  todo-mysql-data:
```

Your complete `compose.yaml` file should look like this:

```yaml
services:
  app:
    image: node:18-alpine
    command: sh -c "yarn install && yarn run dev"
    ports:
      - 127.0.0.1:3000:3000
    working_dir: /app
    volumes:
      - ./:/app
    environment:
      MYSQL_HOST: mysql
      MYSQL_USER: root
      MYSQL_PASSWORD: secret
      MYSQL_DB: todos

  mysql:
    image: mysql:8.0
    volumes:
      - todo-mysql-data:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: secret
      MYSQL_DATABASE: todos

volumes:
  todo-mysql-data:
```

---

### Running the Application Stack

1. **Start the Application Stack**: Use Docker Compose to start your application stack by running the following command in the terminal:

   ```bash
   docker compose up -d
   ```

   This will start both your app and MySQL containers. You should see output indicating that the containers are up and running.

2. **Check Logs**: To view the logs from both services, run:

   ```bash
   docker compose logs -f
   ```

   If successful, you should see logs like:

   ```
   mysql_1  | Version: '8.0.31' MySQL Community Server (GPL)
   app_1    | Connected to mysql db at host mysql
   app_1    | Listening on port 3000
   ```

3. **Access the Application**: Open your browser and navigate to `http://localhost:3000`. The app should be running, and you can interact with it as before.

---

### Tear Down the Application Stack

1. **Stop and Remove Containers**: To stop and remove all services defined in the `compose.yaml`, run:

   ```bash
   docker compose down
   ```

   This will stop the containers and remove the network. **Note:** Volumes are not removed unless you explicitly specify the `--volumes` flag:

   ```bash
   docker compose down --volumes
   ```

---

### Summary

In this step, you learned how Docker Compose simplifies managing multi-container applications by allowing you to define your services, volumes, and networks in a single `compose.yaml` file. Using Docker Compose, you can start, stop, and manage your entire app stack with a single command, making collaboration and setup easier for everyone.
