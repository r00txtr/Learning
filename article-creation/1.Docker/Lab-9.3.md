# Lab 9.3: Deploy Laravel with Docker

In this lab, you will deploy a Laravel application using Docker. You will create a custom Docker image for the Laravel app, set up a MySQL database container, and run both containers using Docker Compose.

By the end of this lab, you will:
1. Clone a Laravel application from a Git repository.
2. Create a custom Docker image for the Laravel application.
3. Set up and configure a MySQL database container.
4. Use Docker Compose to orchestrate both the Laravel app and MySQL containers.

---

## Table of Contents
1. **Introduction**
2. **Step-by-Step Instructions**
    1. Set Up Directory and Clone Repository
    2. Create Dockerfile
    3. Create Entrypoint Script
    4. Create Docker Compose File
    5. Build and Run the Application
3. **Verification**
4. **Conclusion**

---

## Introduction

In this lab, we will deploy a Laravel application using Docker. You will use a custom **Dockerfile** to build a Laravel image and create a containerized environment for both the Laravel application and a **MySQL** database.

### Key Concepts:
- **Docker**: A platform to develop, ship, and run applications in containers.
- **Laravel**: A popular PHP framework used for building web applications.
- **MySQL**: An open-source relational database management system.

---

## Step-by-Step Instructions

### Step 1: Set Up Directory and Clone Repository

1. Create a directory called `challenge2` and change to this directory:

   ```bash
   mkdir ~/challenge2
   cd ~/challenge2
   ```

2. Clone the Laravel application repository:

   ```bash
   git clone https://github.com/academynusa/perpus-laravel.git
   cd perpus-laravel
   ```

---

### Step 2: Create Dockerfile

1. In the `perpus-laravel` directory, create a `Dockerfile` with the following contents:

    ```Dockerfile
    # Use PHP 7.2 as a base image
    FROM php:7.2-fpm

    # Set working directory
    WORKDIR /var/www

    # Install system dependencies
    RUN apt-get update && apt-get install -y \
        build-essential \
        libpng-dev \
        libjpeg-dev \
        libfreetype6-dev \
        zip \
        unzip \
        netcat \
        net-tools \
        git \
        curl \
        libzip-dev \
        && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
        && docker-php-ext-install -j$(nproc) gd zip pdo pdo_mysql mbstring

    # Install Composer
    COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

    # Copy application code
    COPY . /var/www

    # Set correct permissions
    RUN chown -R www-data:www-data /var/www

    # Copy .env file
    RUN cp .env.example .env

    # Run composer update, generate app key, migrate and seed in one go
    COPY ./entrypoint.sh /usr/local/bin/entrypoint.sh
    RUN chmod +x /usr/local/bin/entrypoint.sh

    # Expose port 8000
    EXPOSE 8000

    # Start the application
    CMD ["/usr/local/bin/entrypoint.sh"]
    ```

---

### Step 3: Create Entrypoint Script

1. In the `perpus-laravel` directory, create an `entrypoint.sh` file with the following contents:

    ```bash
    #!/bin/bash

    # Wait for the database to be ready
    echo "Waiting for MySQL to be ready..."
    sleep 10

    # Wait for MySQL to be ready
    until nc -z -v -w30 db 3306; do
       echo "Waiting for database connection..."
       sleep 5
    done
    echo "MySQL is up - executing command."

    # Copy .env.example to .env if it doesn't exist
    if [ ! -f .env ]; then
        cp .env.example .env
    fi

    # Run composer update
    composer config --no-plugins allow-plugins.kylekatarnls/update-helper true
    composer config --no-plugins allow-plugins.symfony/thanks true
    composer update

    # Generate APP_KEY in .env
    php artisan key:generate

    # Update database configuration in .env
    sed -i 's/DB_CONNECTION=.*/DB_CONNECTION=mysql/g' .env
    sed -i 's/DB_HOST=.*/DB_HOST=db/g' .env
    sed -i 's/DB_DATABASE=.*/DB_DATABASE=perpusku_gc/g' .env
    sed -i 's/DB_USERNAME=.*/DB_USERNAME=root/g' .env
    sed -i 's/DB_PASSWORD=.*/DB_PASSWORD=root/g' .env

    # Run database migrations and seeding
    php artisan migrate --force
    php artisan db:seed --force

    # Start PHP-FPM and Laravel server
    php-fpm
    php artisan serve --host=0.0.0.0 --port=8000
    ```

2. Make the script executable:

   ```bash
   chmod +x entrypoint.sh
   ```

---

### Step 4: Create Docker Compose File

1. In the `perpus-laravel` directory, create a `docker-compose.yml` file with the following contents:

    ```yaml
    version: '3'
    services:
      app:
        build:
          context: .
          dockerfile: Dockerfile
        image: img-perpus
        container_name: perpus-app
        environment:
          SERVICE_NAME: app
          SERVICE_TAGS: dev
        ports:
          - "8000:8000"
        volumes:
          - .:/var/www
        networks:
          - app-network
        depends_on:
          - db

      db:
        image: mysql:5.7
        container_name: mysql-db
        environment:
          MYSQL_ROOT_PASSWORD: root
          MYSQL_DATABASE: perpusku_gc
          MYSQL_USER: root
          MYSQL_PASSWORD: root
        ports:
          - "3306:3306"
        volumes:
          - dbdata:/var/lib/mysql
        networks:
          - app-network

    networks:
      app-network:
        driver: bridge

    volumes:
      dbdata:
        driver: local
    ```

---

### Step 5: Build and Run the Application

1. Build the Docker image for the Laravel application:

   ```bash
   docker-compose build
   ```

2. Start the Laravel application and MySQL containers:

   ```bash
   docker-compose up -d
   ```

---

## Verification

1. Check that the Laravel application is running by opening a browser and navigating to `http://localhost:8000`.

2. You should see the Laravel welcome page.

3. Verify that the MySQL container is running by checking its logs:

   ```bash
   docker logs mysql-db
   ```

4. Check the logs of the Laravel app container to ensure everything started correctly:

   ```bash
   docker logs perpus-app
   ```

---

## Conclusion

In this lab, you have successfully:
1. Cloned a Laravel application from a Git repository.
2. Created a custom Docker image for the Laravel application.
3. Set up a MySQL database container.
4. Used Docker Compose to orchestrate both the Laravel app and MySQL containers.

Congratulations on completing **Lab 9.3: Deploy Laravel with Docker**! You now have a strong foundation in deploying Laravel applications using Docker and Docker Compose.

Happy Dockering!