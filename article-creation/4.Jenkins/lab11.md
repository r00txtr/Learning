# **Lab 3.3: Integrating Jenkins with Docker Hub**

In this lab, you will learn how to integrate **Jenkins CI/CD** with **Docker Hub** to automate the build and deployment of a simple **Node.js** application. By the end of this lab, you’ll have set up a Jenkins pipeline that automatically builds and pushes Docker images to **Docker Hub**. You will also learn how to securely add Docker Hub credentials to Jenkins.

---

## **Table of Contents**

1. **Introduction**
2. **Step-by-Step Instructions**
    1. Create a Simple Node.js Project
    2. Create the Dockerfile
    3. Set Up Docker Compose
    4. Create a Jenkins Job
    5. Configure Jenkins Pipeline Script (Jenkinsfile)
    6. Add Docker Hub Credentials to Jenkins
    7. Push Changes and Trigger the Jenkins Build
    8. Monitor Jenkins Build
3. **Conclusion**

---

## **Introduction**

In this lab, we will:
- Set up a **Node.js** project.
- Create a **Dockerfile** to containerize the application.
- Integrate **Jenkins** with **Docker Hub** to automate Docker image builds.
- Use **Jenkins Pipelines** to automate the build and deployment process.

By the end of this lab, you’ll understand how to automate Docker-based deployments using Jenkins.

---

## **Step-by-Step Instructions**

### **Step 1: Create a Simple Node.js Project**

First, we’ll create a basic Node.js project.

#### **1.1 Create the Project Directory**

Open your terminal and create a directory for your Node.js project:

```bash
cd ~
mkdir simple-node-js-app
cd simple-node-js-app
```

#### **1.2 Initialize the Project**

Run the following command to initialize the project and create a `package.json` file:

```bash
npm init -y
```

#### **1.3 Create the `index.js` File**

Create an `index.js` file for your Node.js application:

```bash
vim index.js
```

Add the following simple **Express** server code:

```javascript
const express = require('express');
const app = express();
const port = 3000;

app.get('/', (req, res) => res.send('Hello, Docker Hub'));

app.listen(port, () => {
  console.log(`App listening at http://localhost:${port}`);
});
```

This creates a simple Express server that listens on port 3000 and responds with "Hello, Docker Hub."

#### **1.4 Install Express**

Next, install **Express** by running:

```bash
npm install express --save
```

---

### **Step 2: Create the Dockerfile**

We’ll now create a **Dockerfile** to containerize the Node.js application.

#### **2.1 Create the Dockerfile**

In the project directory, create a Dockerfile:

```bash
vim Dockerfile
```

Add the following content:

```dockerfile
FROM node:14
WORKDIR /app
COPY . .
RUN npm install
EXPOSE 3000
CMD ["node", "index.js"]
```

This Dockerfile sets up a Node.js environment, installs the necessary dependencies, and runs the application.

---

### **Step 3: Set Up Docker Compose**

We’ll use Docker Compose to define and run the application as a service.

#### **3.1 Create the `docker-compose.yml` File**

Create a `docker-compose.yml` file to manage your container:

```bash
vim docker-compose.yml
```

Add the following configuration:

```yaml
version: '3'
services:
  app:
    image: $DOCKER_HUB_USERNAME/simple-node-js-app:latest
    build:
      context: .
    ports:
      - "3000:3000"
```

This file defines a service called `app` that uses the Docker image from Docker Hub and exposes port **3000**.

---

### **Step 4: Create a Jenkins Job**

We will now set up a **Jenkins Pipeline Job** to automate the build and deployment process.

#### **4.1 Log in to Jenkins**

Open Jenkins in your browser:

```
http://your-server-ip:8080
```

Enter your Jenkins credentials to log in.

#### **4.2 Create a New Pipeline Job**

1. On the Jenkins dashboard, click **New Item**.
2. Enter a project name (e.g., `Simple-Node-Docker`).
3. Select **Pipeline** and click **OK**.

![Jenkins Create Pipeline Job](https://jenkins.io/doc/book/resources/screenshots/setup/jenkins-create-pipeline-job.png)  
*(Example Screenshot)*

---

### **Step 5: Configure Jenkins Pipeline Script (Jenkinsfile)**

We’ll now create a **Jenkinsfile** to define the pipeline stages.

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
                    sh 'docker build -t $DOCKER_HUB_USERNAME/simple-node-js-app:latest .'
                }
            }
        }
        stage('Push to Docker Hub') {
            steps {
                script {
                    sh 'echo $DOCKER_HUB_PASSWORD | docker login -u $DOCKER_HUB_USERNAME --password-stdin'
                    sh 'docker push $DOCKER_HUB_USERNAME/simple-node-js-app:latest'
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

This Jenkinsfile defines a pipeline with three stages:
1. **Build Docker Image**: Builds the Docker image for the Node.js application.
2. **Push to Docker Hub**: Pushes the image to your Docker Hub repository.
3. **Deploy with Docker Compose**: Deploys the Docker container.

---

### **Step 6: Add Docker Hub Credentials to Jenkins**

To securely store and use Docker Hub credentials, you need to add them to Jenkins.

#### **6.1 Add Docker Hub Credentials to Jenkins**

1. Go to **Manage Jenkins > Manage Credentials**.
2. Click **Add Credentials** and select **Username with password**.
3. Add your **Docker Hub username** and **password**, and give it an ID (e.g., `docker_hub_credentials`).

---

### **Step 7: Push Changes and Trigger the Jenkins Build**

#### **7.1 Push Your Code to GitHub**

Once your Jenkinsfile and project files are ready, push the code to your GitHub or GitLab repository:

```bash
git add .
git commit -m "Initial setup for Node.js Docker Jenkins pipeline"
git push origin main
```

#### **7.2 Trigger the Jenkins Build**

Go to your Jenkins dashboard, open your **Simple-Node-Docker** job, and click **Build Now** to trigger the pipeline.

---

### **Step 8: Monitor Jenkins Build**

#### **8.1 Monitor the Jenkins Build**

After triggering the build, you can monitor its progress by clicking on the build number (e.g., `#1`) and viewing the **Console Output**.

![Jenkins Console Output](https://jenkins.io/doc/book/resources/screenshots/setup/jenkins-console-output.png)  
*(Example Screenshot)*

Once the build completes, Jenkins will display the running Docker containers.

---

## **Conclusion**

In this lab, you learned how to:
1. Set up a simple **Node.js** project.
2. Create a **Dockerfile** to containerize the project.
3. Use **Jenkins Pipelines** to automate the build and deployment process.
4. Integrate **Docker Hub** with Jenkins to push Docker images automatically.

With this setup, you can automate the deployment of your Node.js applications using Jenkins and Docker Hub. Congratulations on completing **Lab 3.3: Integrating Jenkins with Docker Hub**!

Happy Building!
