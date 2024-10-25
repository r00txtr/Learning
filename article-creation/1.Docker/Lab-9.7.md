# Lab 9.7: Continuous Integration with Docker and Jenkins

In this lab, you will learn how to set up a **Continuous Integration (CI)** pipeline using **Jenkins** and Docker. Jenkins is a popular automation server that can be used to automate building, testing, and deploying applications. With Docker, you can containerize Jenkins and your application, allowing for a more consistent and portable CI environment.

By the end of this lab, you will:
1. Set up Jenkins inside a Docker container.
2. Configure Jenkins to build Docker images and push them to a registry.
3. Automate testing using Docker Compose and multi-stage builds.
4. Implement continuous deployment by integrating Jenkins with Docker Swarm.

---

## Table of Contents
1. **Introduction**
2. **Step-by-Step Instructions**
   1. Set Up Jenkins in Docker
   2. Configure Jenkins for Docker Builds
   3. Automate Testing with Docker Compose
   4. Integrate Jenkins with Docker Swarm for Continuous Deployment
3. **Verification**
4. **Conclusion**

---

## Introduction

**Continuous Integration (CI)** is a software development practice where developers frequently integrate code into a shared repository, followed by automated builds and tests. Docker can help by providing isolated environments to build and test applications, ensuring consistency across different machines.

In this lab, we will:
1. Set up Jenkins inside a Docker container to automate the CI pipeline.
2. Use Jenkins to build Docker images, run tests, and push images to a Docker registry.
3. Automate deployment to a Docker Swarm cluster.

---

## Step-by-Step Instructions

### Step 1: Set Up Jenkins in Docker

#### 1.1 Create a Docker Compose File for Jenkins

First, we will set up Jenkins in a Docker container using **Docker Compose**. Create a `docker-compose.yml` file for Jenkins:

```yaml
version: '3'

services:
  jenkins:
    image: jenkins/jenkins:lts
    container_name: jenkins
    user: root
    ports:
      - "8080:8080"
      - "50000:50000"
    volumes:
      - jenkins_home:/var/jenkins_home
      - /var/run/docker.sock:/var/run/docker.sock
    networks:
      - jenkins-net

networks:
  jenkins-net:
    driver: bridge

volumes:
  jenkins_home:
```

Explanation:
- `jenkins/jenkins:lts`: This is the Long-Term Support (LTS) version of Jenkins.
- `- /var/run/docker.sock:/var/run/docker.sock`: Mounts the Docker socket so Jenkins can interact with Docker on the host machine.

#### 1.2 Start Jenkins

Run the following command to start Jenkins:

```bash
docker-compose up -d
```

Once Jenkins is running, access it at `http://localhost:8080`. To retrieve the initial admin password, run:

```bash
docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword
```

Copy the password and follow the instructions to complete the initial setup of Jenkins.

---

### Step 2: Configure Jenkins for Docker Builds

#### 2.1 Install Docker Plugin

Once Jenkins is set up, install the **Docker Plugin** from the Jenkins dashboard:
1. Go to **Manage Jenkins** -> **Manage Plugins**.
2. Search for **Docker Plugin** and install it.
3. Restart Jenkins after installation.

#### 2.2 Set Up a Jenkins Pipeline for Docker

Create a new pipeline job in Jenkins:
1. Click on **New Item** and choose **Pipeline**.
2. Name the job as `docker-build` and configure the pipeline.

In the **Pipeline** section, use the following script to build and push a Docker image:

```groovy
pipeline {
    agent any
    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    docker.build('yourusername/yourapp:latest')
                }
            }
        }
        stage('Push to Docker Registry') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-credentials') {
                        docker.image('yourusername/yourapp:latest').push()
                    }
                }
            }
        }
    }
}
```

Explanation:
- `docker.build('yourusername/yourapp:latest')`: Builds a Docker image using the provided tag.
- `docker.withRegistry`: Authenticates and pushes the image to Docker Hub.

Make sure to configure Docker Hub credentials in Jenkins by navigating to **Manage Jenkins** -> **Manage Credentials** and adding Docker Hub credentials (`dockerhub-credentials`).

#### 2.3 Test the Pipeline

Run the pipeline by clicking **Build Now**. Jenkins should build the Docker image and push it to Docker Hub.

---

### Step 3: Automate Testing with Docker Compose

#### 3.1 Create a Docker Compose File for Testing

Create a `docker-compose.test.yml` file for your application that includes both the app and its dependencies (e.g., a database).

```yaml
version: '3'

services:
  app:
    build:
      context: .
    ports:
      - "8080:8080"
    depends_on:
      - db
    networks:
      - test-net

  db:
    image: mysql:5.7
    environment:
      MYSQL_ROOT_PASSWORD: example
    networks:
      - test-net

networks:
  test-net:
    driver: bridge
```

#### 3.2 Modify the Jenkins Pipeline for Testing

Update the Jenkins pipeline to include a testing stage using Docker Compose:

```groovy
pipeline {
    agent any
    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    docker.build('yourusername/yourapp:latest')
                }
            }
        }
        stage('Test with Docker Compose') {
            steps {
                script {
                    sh 'docker-compose -f docker-compose.test.yml up -d'
                    // Add your testing logic here, such as running unit or integration tests.
                    sh 'docker-compose -f docker-compose.test.yml down'
                }
            }
        }
        stage('Push to Docker Registry') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-credentials') {
                        docker.image('yourusername/yourapp:latest').push()
                    }
                }
            }
        }
    }
}
```

This script will build the Docker image, run the tests using Docker Compose, and then push the image to Docker Hub.

---

### Step 4: Integrate Jenkins with Docker Swarm for Continuous Deployment

#### 4.1 Set Up a Docker Swarm

If you have not set up a Docker Swarm, initialize it:

```bash
docker swarm init --advertise-addr <MANAGER_IP>
```

Add the worker nodes using the provided join token.

#### 4.2 Update the Jenkins Pipeline for Continuous Deployment

Modify the Jenkins pipeline to include a deployment stage. The deployment will use Docker Swarm to update the service:

```groovy
pipeline {
    agent any
    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    docker.build('yourusername/yourapp:latest')
                }
            }
        }
        stage('Test with Docker Compose') {
            steps {
                script {
                    sh 'docker-compose -f docker-compose.test.yml up -d'
                    // Add your testing logic here
                    sh 'docker-compose -f docker-compose.test.yml down'
                }
            }
        }
        stage('Push to Docker Registry') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-credentials') {
                        docker.image('yourusername/yourapp:latest').push()
                    }
                }
            }
        }
        stage('Deploy to Swarm') {
            steps {
                script {
                    sh 'docker service update --image yourusername/yourapp:latest your-service'
                }
            }
        }
    }
}
```

Explanation:
- `docker service update --image yourusername/yourapp:latest your-service`: Updates the service in Docker Swarm with the new image.

#### 4.3 Deploy the Service

Run the pipeline in Jenkins. If everything is configured correctly, Jenkins will build the image, run tests, push the image to Docker Hub, and then deploy the updated service to your Docker Swarm.

---

## Verification

To verify that the lab has been completed successfully, check the following:
1. Jenkins is successfully building the Docker image and pushing it to Docker Hub.
2. Automated tests are running in the testing stage using Docker Compose.
3. The service is updated in Docker Swarm as part of the continuous deployment stage.

---

## Conclusion

In this lab, you learned how to:
1. Set up Jenkins inside Docker to automate your CI/CD pipeline.
2. Use Jenkins to build Docker images, run automated tests, and push images to a Docker registry.
3. Implement continuous deployment by integrating Jenkins with Docker Swarm.

By using Jenkins with Docker, you can create a reliable and scalable CI/CD pipeline that automates building, testing, and deploying your applications.

---

### Example Output of Jenkins Build:

```plaintext
[Pipeline] Start of Pipeline
[Pipeline] Stage 'Build Docker Image'
Building Docker image...
[Pipeline] Stage 'Test with Docker Compose'
Running tests with Docker Compose...
[Pipeline] Stage 'Push to Docker Registry'
Pushing image to Docker Hub...
[Pipeline] Stage 'Deploy to Swarm'
Deploying service in Docker Swarm...
```

Congratulations on completing **Lab 9.7: Continuous Integration with Docker and Jenkins**! You are now capable of automating your software development workflow with Docker and Jenkins.

Happy Dockering!