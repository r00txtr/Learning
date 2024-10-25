# **Lab Challenge 1: Running a Laravel Application in Containers with CI/CD Practices**

In this lab challenge, you will deploy a Laravel application using Docker containers and implement CI/CD practices with Jenkins. As a DevOps team member, your goal is to containerize the Laravel application and automate its deployment in a containerized environment using Jenkins pipelines.

---

## **Step 1: Clone the Source Code**

The development team has already created a Laravel application. The source code is available on GitHub, and you will clone this repository to your local environment.

#### **Example Commands:**

```bash
$ git clone https://github.com/rahmathidayat9/laraschool.git
$ cd laraschool
```

This will create a local copy of the `laraschool` project.

---

## **Step 2: Dockerize the Laravel Application**

To containerize the Laravel application, you need to create a `Dockerfile` for the web server and a `docker-compose.yml` file to manage both the web server and database services.

### **2.1 Create a Dockerfile**

The Laravel application requires PHP, Nginx, and MySQL. Below is an example of a `Dockerfile` for the Laravel app using **PHP-FPM** and **Nginx**.

#### **Dockerfile Example:**

```dockerfile
# Use PHP base image with Nginx
FROM php:7.4-fpm

# Set working directory
WORKDIR /var/www

# Copy the Laravel project to the container
COPY . /var/www

# Install dependencies
RUN apt-get update && apt-get install -y \
    nginx \
    libpng-dev \
    libjpeg-dev \
    libfreetype6-dev \
    && docker-php-ext-configure gd \
    && docker-php-ext-install gd pdo_mysql

# Copy Nginx config
COPY ./nginx/nginx.conf /etc/nginx/nginx.conf

# Expose port 8080
EXPOSE 8080

# Start Nginx and PHP-FPM
CMD ["sh", "-c", "service nginx start && php-fpm"]
```

### **2.2 Create a Docker Compose File**

You also need to define services for the web application and the database using **docker-compose.yml**. This will orchestrate running both services together.

#### **Docker Compose Example:**

```yaml
version: '3'
services:
  app:
    build: .
    ports:
      - "8080:80"
    volumes:
      - .:/var/www
    environment:
      - DB_HOST=db
      - DB_PORT=3306
      - DB_DATABASE=laraschool
      - DB_USERNAME=root
      - DB_PASSWORD=root
  db:
    image: mysql:5.7
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: laraschool
      MYSQL_USER: root
      MYSQL_PASSWORD: root
    ports:
      - "3306:3306"
```

---

## **Step 3: Set Up Jenkins for CI/CD**

### **3.1 Create a New Jenkins Pipeline Job**

1. Open Jenkins in your browser: `http://your-server-ip:8080`.
2. Log in to Jenkins.
3. Create a new Jenkins job:
   - Click **New Item**, enter the name `Laraschool-Deploy`, and select **Pipeline**.
   - Click **OK** to create the job.

### **3.2 Configure Jenkins Pipeline**

Jenkins will automate the build and deployment of your Laravel app. To do this, we need to create a **Jenkinsfile** that will define the pipeline stages.

#### **Jenkinsfile Example:**

```groovy
pipeline {
    agent any
    environment {
        DOCKER_HUB_CREDENTIALS = credentials('docker_hub_credentials')
    }
    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t $DOCKER_HUB_USERNAME/laraschool-app:latest .'
                }
            }
        }
        stage('Push to Docker Hub') {
            steps {
                script {
                    sh 'echo $DOCKER_HUB_PASSWORD | docker login -u $DOCKER_HUB_USERNAME --password-stdin'
                    sh 'docker push $DOCKER_HUB_USERNAME/laraschool-app:latest'
                }
            }
        }
        stage('Deploy with Docker Compose') {
            steps {
                script {
                    sh 'docker-compose down --rmi all'
                    sh 'docker-compose up -d'
                }
            }
        }
    }
    post {
        always {
            sh 'docker ps'
        }
    }
}
```

---

## **Step 4: Push Docker Image to Docker Hub**

### **4.1 Create a Docker Hub Account**

If you don’t already have a Docker Hub account, create one at [Docker Hub](https://hub.docker.com/).

### **4.2 Add Docker Hub Credentials to Jenkins**

To securely store and use Docker Hub credentials in Jenkins:

1. Go to **Manage Jenkins > Manage Credentials**.
2. Click **Add Credentials** and select **Username with password**.
3. Enter your **Docker Hub username** and **password** and give the credential an ID, e.g., `docker_hub_credentials`.

---

## **Step 5: Run the Laravel Application**

Now that Jenkins is set up to build and push Docker images to Docker Hub, you can run the Laravel application locally using Docker Compose.

#### **Example Command:**

```bash
$ docker-compose up -d
```

This command will start the web and database services defined in the `docker-compose.yml` file. Your Laravel application will be accessible at `http://localhost:8080`.

---

## **Step 6: Verify Jenkins Pipeline and Deployment**

After pushing the code, the Jenkins pipeline will be triggered, automatically building the Docker image, pushing it to Docker Hub, and deploying the application using Docker Compose.

### **6.1 Verify the Pipeline**

1. Go to your Jenkins dashboard and view the pipeline status.
2. Ensure that the build, push, and deploy stages are completed successfully.

### **6.2 Verify the Docker Image on Docker Hub**

Check your Docker Hub repository to ensure that the Laravel Docker image has been pushed successfully.

### **6.3 Access the Laravel Application**

Open your browser and navigate to `http://localhost:8080` to verify that the Laravel application is running correctly.

---

## **Conclusion**

By following this lab challenge, you have successfully set up a CI/CD pipeline using Jenkins to build and deploy a Laravel application in Docker containers. You also learned how to push Docker images to Docker Hub and run the application using Docker Compose.

This completes the lab challenge for containerizing and deploying a Laravel application using CI/CD practices. Congratulations!
