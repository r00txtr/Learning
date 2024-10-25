# Lab 5.1: Using Docker Compose

In this lab, we will explore **Docker Compose**, a tool that simplifies the process of running multi-container Docker applications. You can use Docker Compose to define and manage services, networks, and volumes with a single command by writing everything into a YAML file. In this lab, we will set up a **WordPress** site with a **MySQL** database using Docker Compose.

By the end of this lab, you will:
1. Install Docker Compose.
2. Create a Docker Compose configuration for WordPress and MySQL.
3. Run the application using Docker Compose.
4. Verify that the WordPress site is up and running.

---

## Table of Contents
1. **Introduction**
2. **Installing Docker Compose**
3. **Creating a WordPress and MySQL Setup with Docker Compose**
4. **Running the Docker Compose Application**
5. **Verification**
6. **Conclusion**

---

## Introduction

**Docker Compose** allows you to define and run multi-container Docker applications with ease. You define the services, volumes, and networks required in a `docker-compose.yml` file and then run everything with a single `docker-compose up` command.

In this lab, you will set up a simple WordPress site backed by a MySQL database. WordPress will use MySQL to store its content, and both will be orchestrated using Docker Compose.

We will execute commands on both `pod-[username]-node01` and `pod-[username]-node02`. Replace `[username]` with your actual username and `[password]` with a secure password.

---

## Step 1: Installing Docker Compose

### 1.1 Download and Install Docker Compose

First, install Docker Compose on both nodes.

Execute the following commands on both `pod-[username]-node01` and `pod-[username]-node02`:

```bash
VERSION=$(curl --silent https://api.github.com/repos/docker/compose/releases/latest | jq .name -r)
DESTINATION=/usr/bin/docker-compose
sudo curl -sL https://github.com/docker/compose/releases/download/${VERSION}/docker-compose-$(uname -s)-$(uname -m) -o $DESTINATION
sudo chmod 755 $DESTINATION
```

This command:
- Fetches the latest version of Docker Compose.
- Downloads it to `/usr/bin/docker-compose`.
- Sets the correct permissions to make it executable.

### 1.2 Verify Docker Compose Installation

After installing, verify that Docker Compose was installed correctly by checking its version:

```bash
docker-compose --version
```

You should see output showing the Docker Compose version, similar to:

```
docker-compose version 2.27.0, build 5becea4c
```

---

## Step 2: Creating a WordPress and MySQL Setup with Docker Compose

Now that Docker Compose is installed, let’s define a WordPress and MySQL multi-container application using a `docker-compose.yml` file.

### 2.1 Create a Directory for the Project

First, create a directory to store the `docker-compose.yml` file and the related project files.

```bash
cd $HOME
mkdir -p latihan/my_wordpress
cd latihan/my_wordpress
```

This creates a directory named `my_wordpress` in your `latihan` folder and navigates into it.

### 2.2 Create the Docker Compose File

Create a `docker-compose.yml` file that defines the WordPress and MySQL services:

```bash
vim docker-compose.yml
```

Add the following content to the file:

```yaml
services:
   db:
     image: mysql:5.7
     volumes:
       - dbdata:/var/lib/mysql
     restart: always
     environment:
       MYSQL_ROOT_PASSWORD: somewordpress
       MYSQL_DATABASE: wordpress
       MYSQL_USER: admin01
       MYSQL_PASSWORD: admin_rahasia

   wordpress:
     depends_on:
       - db
     image: wordpress:latest
     ports:
       - "8000:80"
     restart: always
     environment:
       WORDPRESS_DB_HOST: db:3306
       WORDPRESS_DB_USER: root
       WORDPRESS_DB_PASSWORD: somewordpress

volumes:
    dbdata:
```

#### Explanation:
- **db**: This service uses the MySQL image. It mounts a volume named `dbdata` to store the MySQL database and sets the root password, database name, user, and password.
- **wordpress**: This service uses the latest WordPress image and links to the MySQL database (`db`). It maps port 80 inside the container to port 8000 on the host.
- **volumes**: The `dbdata` volume is used to persist MySQL data across container restarts.

Replace `[username]` with your Docker username and `[password]` with a secure password of your choice.

---

## Step 3: Running the Docker Compose Application

### 3.1 Run Docker Compose

Now, let’s bring up the WordPress and MySQL services using Docker Compose.

Run the following command:

```bash
docker-compose up -d
```

- `up`: Starts the services defined in the `docker-compose.yml` file.
- `-d`: Runs the services in detached mode (in the background).

Docker Compose will start the `db` and `wordpress` containers, create the necessary networks and volumes, and link the services together.

### 3.2 List the Running Containers

After running Docker Compose, list the running containers to verify that both services are up and running:

```bash
docker container ls
```

You should see the `db` and `wordpress` containers running.

---

## Step 4: Testing and Verification

Now that the WordPress and MySQL services are running, let’s verify that everything works as expected.

### 4.1 Test the WordPress Site

You can test the WordPress site by browsing to `http://localhost:8000` or using `curl`:

```bash
curl http://localhost:8000
```

If everything is set up correctly, you will see the WordPress installation page, where you can configure your WordPress site by setting the site title, username, and password.

---

## Conclusion

In this lab, you have learned how to:
1. Install **Docker Compose** on multiple nodes.
2. Define a multi-container application using a `docker-compose.yml` file.
3. Run a **WordPress** site backed by a **MySQL** database using Docker Compose.
4. Test the WordPress site to ensure it is up and running.

### Example Output of `docker container ls`:

```
CONTAINER ID   IMAGE                      COMMAND                  CREATED              STATUS              PORTS                    NAMES
a06ae3ae7185   wordpress:latest           "docker-entrypoint.s…"   About a minute ago   Up About a minute   0.0.0.0:8000->80/tcp     my_wordpress-wordpress-1
adbdfda80637   mysql:5.7                  "docker-entrypoint.s…"   About a minute ago   Up About a minute   3306/tcp, 33060/tcp      my_wordpress-db-1d
```

Congratulations on completing **Lab 5.1: Using Docker Compose**! You now have a solid understanding of how to use Docker Compose to manage multi-container applications.

Happy Dockering!
