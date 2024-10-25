Here’s the revised **Lab 11.2** for deploying a Laravel application using **K3s**:

---

# Lab 11.2: Deploy Laravel with K3s

In this lab, you will deploy a Laravel application on a **K3s** cluster. You will create a custom Docker image for the Laravel app, set up a **MySQL** database, and deploy both on a K3s cluster using **Kubernetes manifests**.

By the end of this lab, you will:
1. Clone a Laravel application from a Git repository.
2. Build a custom Docker image for the Laravel application.
3. Set up and configure a MySQL database within the cluster.
4. Use Kubernetes manifests to deploy both the Laravel app and MySQL database.

---

## Table of Contents
1. **Introduction**
2. **Step-by-Step Instructions**
    1. Set Up Directory and Clone Repository
    2. Build Laravel Docker Image and Push to a Container Registry
    3. Create Kubernetes Manifests for MySQL and Laravel
    4. Deploy Laravel and MySQL on K3s
3. **Verification**
4. **Conclusion**

---

## Introduction

In this lab, you will deploy a Laravel application in a K3s Kubernetes cluster. You will first build a custom Docker image for the Laravel app, push it to a container registry, and then create Kubernetes manifests to deploy the app and a MySQL database.

### Key Concepts:
- **K3s**: A lightweight Kubernetes distribution optimized for IoT, edge, and CI environments.
- **Docker**: A platform to develop, ship, and run applications in containers.
- **Laravel**: A popular PHP framework used for building web applications.
- **MySQL**: An open-source relational database management system.

---

## Step-by-Step Instructions

### Step 1: Set Up Directory and Clone Repository

1. Create a directory for the Laravel deployment and navigate into it:

   ```bash
   mkdir ~/k3s-laravel
   cd ~/k3s-laravel
   ```

2. Clone the Laravel application repository:

   ```bash
   git clone https://github.com/academynusa/perpus-laravel.git
   cd perpus-laravel
   ```

---

### Step 2: Build Laravel Docker Image and Push to a Container Registry

1. **Create a `Dockerfile`** in the `perpus-laravel` directory:

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

2. **Create the `entrypoint.sh` script** as before:

    ```bash
    #!/bin/bash

    # Wait for the database to be ready
    echo "Waiting for MySQL to be ready..."
    sleep 10

    until nc -z -v -w30 db 3306; do
       echo "Waiting for database connection..."
       sleep 5
    done
    echo "MySQL is up - executing command."

    # Run composer update and Laravel setup
    composer update
    php artisan key:generate
    php artisan migrate --force
    php artisan db:seed --force

    # Start Laravel application
    php-fpm
    php artisan serve --host=0.0.0.0 --port=8000
    ```

3. **Build and push the Docker image to a container registry (DockerHub/Private Registry)**:

   Build the Docker image:

   ```bash
   docker build -t your-username/laravel-app .
   ```

   Push the image to Docker Hub (replace `your-username` with your Docker Hub username):

   ```bash
   docker push your-username/laravel-app
   ```

---

### Step 3: Create Kubernetes Manifests for MySQL and Laravel

1. **Create a Kubernetes manifest file for MySQL (mysql-deployment.yaml):**

    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: mysql
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: mysql
      template:
        metadata:
          labels:
            app: mysql
        spec:
          containers:
            - name: mysql
              image: mysql:5.7
              env:
                - name: MYSQL_ROOT_PASSWORD
                  value: root
                - name: MYSQL_DATABASE
                  value: perpusku_gc
                - name: MYSQL_USER
                  value: root
                - name: MYSQL_PASSWORD
                  value: root
              ports:
                - containerPort: 3306
              volumeMounts:
                - name: mysql-persistent-storage
                  mountPath: /var/lib/mysql
          volumes:
            - name: mysql-persistent-storage
              emptyDir: {}
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: mysql
    spec:
      ports:
        - port: 3306
      selector:
        app: mysql
    ```

2. **Create a Kubernetes manifest file for Laravel (laravel-deployment.yaml):**

    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: laravel
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: laravel
      template:
        metadata:
          labels:
            app: laravel
        spec:
          containers:
            - name: laravel
              image: your-username/laravel-app
              ports:
                - containerPort: 8000
              env:
                - name: DB_CONNECTION
                  value: mysql
                - name: DB_HOST
                  value: mysql
                - name: DB_DATABASE
                  value: perpusku_gc
                - name: DB_USERNAME
                  value: root
                - name: DB_PASSWORD
                  value: root
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: laravel
    spec:
      ports:
        - port: 8000
      selector:
        app: laravel
    ```

---

### Step 4: Deploy Laravel and MySQL on K3s

1. **Deploy MySQL:**

   ```bash
   kubectl apply -f mysql-deployment.yaml
   ```

2. **Deploy Laravel:**

   ```bash
   kubectl apply -f laravel-deployment.yaml
   ```

3. **Expose Laravel service using a NodePort or Ingress** (NodePort example):

    ```yaml
    apiVersion: v1
    kind: Service
    metadata:
      name: laravel-nodeport
    spec:
      type: NodePort
      selector:
        app: laravel
      ports:
        - port: 8000
          targetPort: 8000
          nodePort: 30080
    ```

   Apply the NodePort service:

   ```bash
   kubectl apply -f laravel-nodeport.yaml
   ```

   Access Laravel via `http://<node-ip>:30080`.

---

## Verification

1. **Verify Laravel is running** by opening `http://<node-ip>:30080` in a browser. You should see the Laravel welcome page.

2. **Check MySQL** logs to ensure the database is running:

   ```bash
   kubectl logs deployment/mysql
   ```

3. **Check Laravel app logs**:

   ```bash
   kubectl logs deployment/laravel
   ```

---

## Conclusion

In this lab, you have successfully:
1. Built and pushed a custom Docker image for a Laravel application.
2. Created Kubernetes manifests for Laravel and MySQL.
3. Deployed both Laravel and MySQL on a K3s cluster.

Congratulations on completing **Lab 11.2: Deploy Laravel with K3s**! You now have a strong foundation in deploying Laravel applications in a Kubernetes environment.

Happy Learning!

---
