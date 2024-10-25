# **Lab 3.4: Integrating Jenkins with Docker Hub**

In this lab, we will walk through integrating **Jenkins CI/CD** with **Docker Hub** to build, test, and deploy a simple **Node.js** application. This step-by-step guide will cover how to create a Jenkins pipeline that builds and pushes Docker images to Docker Hub, automates deployment, and integrates Docker containers. We will also securely store Docker Hub credentials in Jenkins.

---

## **Table of Contents**

1. **Introduction**
2. **Step-by-Step Instructions**
   1. Create a Simple Node.js Project
   2. Create a Dockerfile
   3. Set Up Docker Compose
   4. Set Up Jenkins Job
   5. Configure Jenkins Pipeline Script (Jenkinsfile)
   6. Secure Docker Hub Credentials in Jenkins
   7. Push Changes and Trigger the Jenkins Build
   8. Monitor Jenkins Build and Verify the Pipeline
3. **Conclusion**

---

## **Introduction**

In this lab, you will:
- Create a basic **Node.js** project.
- Build and deploy the project using **Jenkins Pipelines** and **Docker**.
- Push the Docker image to **Docker Hub**.
- Automate the deployment process using Jenkins and Docker.

By the end of this lab, you'll have a fully automated build pipeline integrated with Docker Hub.

---

## **Step-by-Step Instructions**

### **Step 1: Create a Simple Node.js Project**

#### **1.1 Create the Project Directory**

Start by creating a directory for the Node.js project on your local machine:

```bash
cd ~
mkdir jenkins-node-docker-app
cd jenkins-node-docker-app
```

#### **1.2 Initialize the Node.js Project**

Run the following command to initialize the Node.js project and create a `package.json` file:

```bash
npm init -y
```

#### **1.3 Create the `index.js` File**

Create an `index.js` file for your Node.js application:

```bash
vim index.js
```

Add the following simple **Express.js** server code:

```javascript
const express = require('express');
const app = express();
const port = process.env.PORT || 3000;

app.get('/', (req, res) => res.send('Hello, Docker Hub!'));

app.listen(port, () => {
  console.log(`App running at http://localhost:${port}`);
});
```

This will create a simple web server that listens on port 3000 and responds with "Hello, Docker Hub!".

#### **1.4 Install Express**

Now install **Express.js** by running:

```bash
npm install express --save
```

---

### **Step 2: Create a Dockerfile**

We will now create a **Dockerfile** to containerize the Node.js application.

#### **2.1 Create the Dockerfile**

In your project directory, create a Dockerfile:

```bash
vim Dockerfile
```

Add the following content to define the Docker environment for your application:

```dockerfile
FROM node:14
WORKDIR /app
COPY . .
RUN npm install
EXPOSE 3000
CMD ["node", "index.js"]
```

This Dockerfile pulls the Node.js 14 image, sets up the app directory, installs the necessary dependencies, and runs the application.

---

### **Step 3: Set Up Docker Compose**

#### **3.1 Create a `docker-compose.yml` File**

Create a `docker-compose.yml` file to manage the Docker container.

```bash
vim docker-compose.yml
```

Add the following configuration to define the application service:

```yaml
version: '3'
services:
  app:
    image: $DOCKER_HUB_USERNAME/jenkins-node-docker-app:latest
    build:
      context: .
    ports:
      - "3000:3000"
```

This file defines a service called `app` that uses a Docker image pulled from Docker Hub and exposes port **3000**.

---

### **Step 4: Set Up Jenkins Job**

#### **4.1 Log in to Jenkins**

Open Jenkins in your browser:

```
http://your-server-ip:8080
```

Log in with your Jenkins credentials.

#### **4.2 Create a New Jenkins Pipeline Job**

1. On the Jenkins dashboard, click **New Item**.
2. Enter a name for your project (e.g., `Node-Docker-Project`).
3. Select **Pipeline** and click **OK**.

![Jenkins Create Pipeline Job](https://jenkins.io/doc/book/resources/screenshots/setup/jenkins-create-pipeline-job.png)  
*(Example Screenshot)*

---

### **Step 5: Configure Jenkins Pipeline Script (Jenkinsfile)**

Next, we will create a **Jenkinsfile** to automate the build, push, and deploy processes.

#### **5.1 Create a Jenkinsfile**

In your project directory, create a **Jenkinsfile**:

```bash
vim Jenkinsfile
```

Add the following content:

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
                    sh 'docker build -t $DOCKER_HUB_USERNAME/jenkins-node-docker-app:latest .'
                }
            }
        }
        stage('Push to Docker Hub') {
            steps {
                script {
                    sh 'echo $DOCKER_HUB_PASSWORD | docker login -u $DOCKER_HUB_USERNAME --password-stdin'
                    sh 'docker push $DOCKER_HUB_USERNAME/jenkins-node-docker-app:latest'
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

#### **Explanation**:
- The **Build Docker Image** stage builds the Docker image.
- The **Push to Docker Hub** stage pushes the image to Docker Hub.
- The **Deploy with Docker Compose** stage runs the container using Docker Compose.
- The **post** section always shows running containers after the pipeline finishes.

---

### **Step 6: Secure Docker Hub Credentials in Jenkins**

To securely store and use your Docker Hub credentials in Jenkins:

1. Go to **Manage Jenkins > Manage Credentials**.
2. Click **Add Credentials** and select **Username with password**.
3. Enter your **Docker Hub username** and **password**, and give the credential an ID, e.g., `docker_hub_credentials`.

This will allow Jenkins to securely access your Docker Hub account during the build process.

---

### **Step 7: Push Changes and Trigger the Jenkins Build**

#### **7.1 Push Your Code to Git**

Once your Jenkinsfile and project files are ready, commit and push the code to your repository:

```bash
git add .
git commit -m "Initial Node.js app with Docker and Jenkins pipeline"
git push origin main
```

#### **7.2 Trigger the Jenkins Build**

Go to your Jenkins dashboard, open your **Node-Docker-Project** job, and click **Build Now** to trigger the Jenkins pipeline.

---

### **Step 8: Monitor Jenkins Build and Verify the Pipeline**

#### **8.1 Monitor the Jenkins Build**

You can monitor the progress of the build by clicking on the build number (e.g., `#1`) and viewing the **Console Output**. Jenkins will display the logs as it builds, pushes, and deploys the Docker image.

![Jenkins Console Output](https://jenkins.io/doc/book/resources/screenshots/setup/jenkins-console-output.png)  
*(Example Screenshot)*

#### **8.2 Verify the Deployment**

Once the build is complete, Jenkins will display the running Docker containers. To check the status of your application, access the Node.js app by opening the following URL in your browser:

```bash
http://your-server-ip:3000
```

You should see the message: **"Hello, Docker Hub!"**

---

## **Conclusion**

In this lab, you learned how to:
1. Create a **Node.js** project.
2. Build and deploy the project using **Docker** and **Jenkins Pipelines**.
3. Push Docker images to **Docker Hub**.
4. Automate the deployment process using **Jenkins** and **Docker Compose**.

By the end of this tutorial, you should have a complete CI/CD pipeline that builds and deploys your Node.js application using Docker and Jenkins.

Congratulations on completing **Lab 3.4: Integrating Jenkins with Docker Hub**!

Happy Building!
