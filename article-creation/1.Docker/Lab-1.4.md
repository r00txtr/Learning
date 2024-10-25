# Lab 1.4: Docker Run - Part 3

In this lab, we will continue to explore more advanced Docker functionalities, including running MySQL and phpMyAdmin containers, working with Ubuntu containers, and setting up a WordPress instance connected to a MariaDB database. By the end of this lab, you will have a good understanding of how to link containers, manage resource limitations, and test your containers in a real-world scenario.

---

## Table of Contents
1. **Introduction**
2. **Running MySQL and phpMyAdmin Containers (Practice 05)**
    1. Run MySQL Container
    2. Pull and Run phpMyAdmin Container
    3. Test the Setup in a Browser
3. **Working with Ubuntu Containers (Practice 06)**
    1. Run Ubuntu Containers
    2. Pause and Unpause Containers
4. **Running WordPress with MariaDB (Practice 07)**
    1. Run MariaDB with Limited Resources
    2. Run WordPress and Link it to MariaDB
    3. Check Logs, Processes, and Resource Usage
5. **Verification**

---

## Introduction

We will continue running Docker containers in this lab on the node `pod-[username]-node01`. You will use MySQL, phpMyAdmin, Ubuntu, MariaDB, and WordPress images, managing them with advanced parameters such as memory and CPU limits, pausing/unpausing containers, and linking containers to work together.

---

## Running MySQL and phpMyAdmin Containers (Practice 05)

In this section, we will run a MySQL container and link it to a phpMyAdmin container to easily manage the MySQL database via a web interface.

### 1. Running MySQL Container

We start by running a MySQL container with additional parameters, such as setting a root password and creating a database.

```bash
docker run -d --name my-mysql -e MYSQL_ROOT_PASSWORD=RAHASIA -e MYSQL_DATABASE=latihan05 -p 3306:3306 mysql
```

This command does the following:
- `-d`: Runs the container in detached mode (in the background).
- `--name my-mysql`: Names the container `my-mysql`.
- `-e MYSQL_ROOT_PASSWORD=RAHASIA`: Sets the root password to `RAHASIA`.
- `-e MYSQL_DATABASE=latihan05`: Creates a database named `latihan05`.
- `-p 3306:3306`: Exposes MySQL on port `3306`.

### 2. Pull phpMyAdmin Image from DockerHub

Now, we pull the phpMyAdmin image from DockerHub, which we will use to manage the MySQL container:

```bash
docker pull phpmyadmin:latest
```

### 3. Running phpMyAdmin Container

Next, we run the phpMyAdmin container and link it to the `my-mysql` container:

```bash
docker run --name my-phpmyadmin -d --link my-mysql:db -p 8090:80 phpmyadmin
```

This command links the phpMyAdmin container to the `my-mysql` container (via `--link`), allowing it to access the MySQL database.

### 4. Test the Setup in a Browser

To verify that everything is working, open a browser and navigate to the following URL:

```
http://10.10.10.11:8090 (Your-Local-IP)
```

Log in using:
- **Username**: `root`
- **Password**: `RAHASIA`

You should now be able to manage the MySQL database via phpMyAdmin.

---

## Working with Ubuntu Containers (Practice 06)

In this section, we will create and manage Ubuntu containers, focusing on pausing and unpausing them and observing their resource usage.

### 1. Run Ubuntu Containers

We’ll create two Ubuntu containers, `ubuntu1` and `ubuntu2`, both running in detached mode with interactive terminals:

```bash
docker run -dit --name ubuntu1 ubuntu
docker run -dit --name ubuntu2 ubuntu
```

This command:
- `-dit`: Runs the container in detached, interactive, and terminal modes.
- `--name`: Assigns names to the containers (`ubuntu1` and `ubuntu2`).

### 2. Display List of Containers

To check if the containers are running:

```bash
docker ps
```

This will display all active containers.

### 3. Pause Ubuntu Containers

You can pause the containers using the following commands:

```bash
docker pause ubuntu1
docker pause ubuntu2
```

Pausing the containers stops them from using CPU resources but keeps their state intact.

### 4. Check Paused Container Status

After pausing, check if the containers are listed as "Paused":

```bash
docker ps
```

You should see the status of `ubuntu1` and `ubuntu2` as "Paused."

### 5. Check Resource Usage for Paused Containers

Even when containers are paused, you can still check their resource usage:

```bash
docker stats ubuntu1
docker stats ubuntu2
```

### 6. Unpause Ubuntu Container

To unpause `ubuntu1`:

```bash
docker unpause ubuntu1
```

The container will resume its normal operations.

---

## Running WordPress with MariaDB (Practice 07)

In this section, we will run a WordPress instance connected to a MariaDB database, with resource limits on both containers.

### 1. Run MariaDB Container with Limited Resources

We will start by creating a MariaDB container with limited memory and CPU usage:

```bash
docker container run -d --name ch6_mariadb --memory 256m --cpu-shares 1024 --cap-drop net_raw -e MYSQL_ROOT_PASSWORD=test mariadb:5.5
```

This command:
- Limits the container to 256MB of memory (`--memory 256m`).
- Assigns a CPU priority using `--cpu-shares 1024`.
- Drops the `net_raw` capability to restrict the container's network capabilities.

### 2. Run WordPress and Link to MariaDB

Next, we will run a WordPress container and link it to the MariaDB container:

```bash
docker container run -d -p 80:80 -P --name ch6_wordpress  --memory 512m --cpu-shares 512 \
--cap-drop net_raw --link ch6_mariadb:mysql -e WORDPRESS_DB_PASSWORD=test \
mwar8205/wordpress:5.0.0-php7.2-apache
```

This WordPress container:
- Is limited to 512MB of memory.
- Has a lower CPU priority than the MariaDB container (`--cpu-shares 512`).
- Is linked to the `ch6_mariadb` container using `--link ch6_mariadb:mysql`.

### 3. Check Logs, Running Processes, and Resource Usage

To check logs for both containers:

```bash
docker logs ch6_mariadb
docker logs ch6_wordpress
```

To check running processes:

```bash
docker top ch6_mariadb
docker top ch6_wordpress
```

To monitor resource usage:

```bash
docker stats ch6_mariadb
docker stats ch6_wordpress
```

### 4. Test WordPress Installation

Open a browser and navigate to:

```
http://10.10.10.11 (Your-Local-IP)
```

You should be guided through the WordPress setup process. Complete the installation using English as the default language.

---

## Verification

To verify that everything is functioning correctly, perform the following checks:

1. **Access WordPress**: Ensure that you can complete the WordPress installation and access the site in English.
2. **phpMyAdmin Accessibility**: Confirm that you can log in to phpMyAdmin and manage the MySQL database.
3. **Pause Status of Ubuntu2**: Verify that the `ubuntu2` container is paused.
4. **WordPress Container Specs**: Check that the WordPress container has the specified memory and CPU limits using `docker stats`.

---

Congratulations! You’ve successfully completed **Lab 1.4: Docker Run - Part 3**. You’ve learned how to manage MySQL, phpMyAdmin, WordPress, and Ubuntu containers, including setting resource limits and linking containers to work together.

Happy containerizing!
