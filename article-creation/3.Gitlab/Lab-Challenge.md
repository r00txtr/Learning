## Lab Challenge 1: Running a Laravel Application in Containers with CI/CD Practices

In this challenge, we will go through how to deploy a Laravel application using Docker and CI/CD practices. You, as the DevOps team member, are tasked with containerizing the Laravel application and ensuring it runs in a containerized environment with automated deployments.

---

### Step 1: Clone the Source Code

The development team has already created a Laravel application, and the source code is available in the following repository: [Laraschool GitHub Repo](https://github.com/rahmathidayat9/laraschool). You will clone this repository to start working on it locally.

#### Example Commands:

```bash
$ git clone https://github.com/rahmathidayat9/laraschool.git
$ cd laraschool
```

---

### Step 2: Create a New Project on GitLab

To manage your CI/CD pipeline, create a new project on GitLab:

1. Log in to your GitLab account.
2. Click **"New Project"**.
3. Set the following details:
   - **Project Name**: `laraschool-app-username`
   - **Visibility Level**: Public
4. Clone this new repository to your local machine.

#### Example Commands:

```bash
$ git clone https://gitlab.com/USERNAME_GITLAB/laraschool-app-username.git
```

---

### Step 3: Dockerize the Laravel Application

To run the Laravel application in a container, you need to create a `Dockerfile`. Laravel requires a web server and a database to function properly. We will use **Nginx** as the web server and **MySQL** for the database.

1. Create a `Dockerfile` for your Laravel application. The base image should come from your registry (e.g., `registry.adinusa.id/btacademy/nginx:latest`).

#### Dockerfile Example:

```dockerfile
# Use the latest version of PHP and Nginx
FROM registry.adinusa.id/btacademy/nginx:latest

# Set working directory
WORKDIR /var/www

# Copy all files from the current directory to the container
COPY . /var/www

# Install dependencies
RUN apt-get update && apt-get install -y \
    php7.4-cli \
    php7.4-mbstring \
    php7.4-xml \
    php7.4-mysql \
    php7.4-fpm

# Expose port 8080
EXPOSE 8080

# Start Nginx
CMD ["nginx", "-g", "daemon off;"]
```

2. Create a `docker-compose.yml` file to manage the services for both the web server and the database.

#### Docker Compose Example:

```yaml
version: '3'
services:
  web:
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

### Step 4: Set Up the CI/CD Pipeline

Now, we will configure GitLab CI/CD to automate the build and deployment process. Create a `.gitlab-ci.yml` file in your project repository with the following stages:

#### `.gitlab-ci.yml` Example:

```yaml
stages:
  - build
  - deploy

build:
  image: docker:latest
  services:
    - docker:dind
  script:
    - docker build -t $DOCKER_HUB_USERNAME/laraschool-username:1.0 .
    - docker push $DOCKER_HUB_USERNAME/laraschool-username:1.0
  only:
    - develop

deploy:
  image: docker:latest
  script:
    - docker-compose down
    - docker-compose up -d
  only:
    - main
```

---

### Step 5: Push Docker Image to Docker Hub

1. To push your Docker image to Docker Hub, you will need to create an account on [Docker Hub](https://hub.docker.com/) if you haven’t already.
2. Add your Docker Hub credentials to GitLab CI/CD environment variables for secure access.

#### Steps to Add Credentials:

1. Navigate to **Settings** > **CI/CD** > **Variables** in GitLab.
2. Add:
   - **DOCKER_HUB_USERNAME**: Your Docker Hub username
   - **DOCKER_HUB_PASSWORD**: Your Docker Hub password

Now, when the pipeline runs, it will automatically push the Docker image to your Docker Hub registry.

---

### Step 6: Run the Laravel Application

To run the Laravel application, use the `docker-compose.yml` file and start the services. The web server will run on **port 8080**.

#### Example Command:

```bash
$ docker-compose up -d
```

This will start your Laravel application, which you can access at `http://localhost:8080`.

---

### Step 7: Verify Pipeline and Deployment

1. **Ensure Pipeline Passed**: Go to your GitLab project’s pipeline page and confirm that the build and deploy stages have completed successfully.
2. **Verify Docker Image on Docker Hub**: Check your Docker Hub repository to ensure the image was pushed successfully.
3. **Access the Application**: Open your browser and navigate to `http://localhost:8080` to confirm that the Laravel application is running correctly.

---

### Conclusion

By following these steps, you’ve successfully set up a CI/CD pipeline to automate the build, deployment, and containerization of the Laravel School Application. You’ve also learned how to push Docker images to Docker Hub and run the application in containers.
