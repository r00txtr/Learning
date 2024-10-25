# **Lab 3.2: Integrating Jenkins with Docker**

In this lab, you will integrate **Jenkins CI/CD** with **Docker** to automate the deployment of a **Canvas Tetris** game. By the end of this lab, you will have the **Canvas Tetris** game running inside a Docker container, deployed via **Jenkins** pipelines. You will learn how to configure a Dockerfile, set up a Jenkins pipeline, and monitor the deployment process.

---

## **Table of Contents**

1. **Introduction**
2. **Step-by-Step Instructions**
    1. Cloning the Canvas Tetris Repository
    2. Set Up a New Jenkins Job
    3. Create a Dockerfile for the Tetris Game
    4. Set Up Docker Compose
    5. Create a Jenkins Pipeline for Deployment
    6. Push Changes to Git and Trigger the Jenkins Build
    7. Monitor the Pipeline and Access Deployed Game
3. **Conclusion**

---

## **Introduction**

In this lab, you will:
- Clone the **Canvas Tetris** repository.
- Set up a Jenkins job and pipeline for automated deployments.
- Use **Docker** and **Docker Compose** to containerize and deploy the Tetris game.
- Manage Docker images through **Docker Hub**.
- Automate the build and deployment process using **Jenkins**.

---

## **Step-by-Step Instructions**

### **Step 1: Cloning the Canvas Tetris Repository**

#### **1.1 Clone the Canvas Tetris Repository**

First, clone the **Canvas Tetris** repository from GitHub to your local environment:

```bash
cd ~
git clone https://github.com/dionyziz/canvas-tetris.git canvas-tetris-username
```

This will create a local copy of the Tetris game in a directory named `canvas-tetris-username`.

---

### **Step 2: Set Up a New Jenkins Job**

#### **2.1 Log in to Jenkins**

Open Jenkins in your browser:

```
http://your-server-ip:8080
```

Enter your Jenkins credentials to log in.

#### **2.2 Create a New Pipeline Job**

1. On the Jenkins dashboard, click **New Item**.
2. Enter a project name (e.g., `Tetris-Deployment`).
3. Select **Pipeline** and click **OK**.

![Jenkins Create Pipeline Job](https://jenkins.io/doc/book/resources/screenshots/setup/jenkins-create-pipeline-job.png)  
*(Example Screenshot)*

---

### **Step 3: Create a Dockerfile for the Tetris Game**

In this step, you will containerize the Tetris game using a Dockerfile.

#### **3.1 Organize Tetris Game Files**

Move the Tetris game files to a dedicated directory:

```bash
mkdir ~/canvas-tetris-username/tetris
mv ~/canvas-tetris-username/index.html ~/canvas-tetris-username/js ~/canvas-tetris-username/sound ~/canvas-tetris-username/style.css ~/canvas-tetris-username/tetris/
```

#### **3.2 Create a Dockerfile**

Create a Dockerfile to define the environment for your containerized game:

```bash
vim ~/canvas-tetris-username/Dockerfile
```

Add the following content to the Dockerfile:

```dockerfile
FROM nginx:latest

ADD ./tetris /usr/share/nginx/html/
```

This Dockerfile uses the **NGINX** server to host the Tetris game, serving the files from the `/usr/share/nginx/html/` directory.

---

### **Step 4: Set Up Docker Compose**

You will now set up **Docker Compose** to manage the deployment of the Tetris game container.

#### **4.1 Create a `docker-compose.yml` File**

Create a `docker-compose.yml` file in your project directory:

```bash
vim ~/canvas-tetris-username/docker-compose.yml
```

Add the following content:

```yaml
version: '3'
services:
  tetris-game:
    image: canvas-tetris-username:1.0
    build:
      context: .
    ports:
      - "8080:80"
```

This Docker Compose file defines a service named `tetris-game` that builds the Tetris container and exposes it on port **8080**.

---

### **Step 5: Create a Jenkins Pipeline for Deployment**

In this step, you will automate the deployment process using Jenkins by creating a **Jenkinsfile**.

#### **5.1 Create a Jenkinsfile**

In your project directory, create a **Jenkinsfile** to define the pipeline:

```bash
vim ~/canvas-tetris-username/Jenkinsfile
```

Add the following content:

```groovy
pipeline {
    agent any
    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t canvas-tetris-username:1.0 .'
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

This pipeline will:
- Build the Docker image for the Tetris game.
- Use Docker Compose to deploy the container.
- Display the running Docker containers after deployment.

#### **5.2 Configure Jenkins Pipeline**

In Jenkins, go to your **Tetris-Deployment** job configuration:

1. Scroll down to the **Pipeline** section.
2. In the **Definition** field, select **Pipeline script from SCM**.
3. Set **SCM** to **Git** and enter the repository URL (e.g., `https://github.com/your-username/canvas-tetris-username.git`).
4. In the **Script Path** field, enter `Jenkinsfile`.

---

### **Step 6: Push Changes to Git and Trigger the Jenkins Build**

#### **6.1 Commit Changes to Git**

Once the Dockerfile, Docker Compose, and Jenkinsfile are ready, add and commit the changes:

```bash
cd ~/canvas-tetris-username
git add .
git commit -m "Initial commit for Tetris game with Docker and Jenkins pipeline"
```

#### **6.2 Push Changes to Remote Repository**

Push the committed changes to your GitHub or GitLab repository:

```bash
git push origin main
```

#### **6.3 Trigger the Jenkins Build**

1. Go to your Jenkins dashboard and click on your **Tetris-Deployment** job.
2. Click **Build Now** to trigger the Jenkins pipeline.

---

### **Step 7: Monitor the Pipeline and Access Deployed Game**

#### **7.1 Monitor the Jenkins Build**

Once the pipeline is running, monitor the progress by clicking on the build number (e.g., `#1`) and viewing the **Console Output** for detailed logs.

#### **7.2 Access the Deployed Tetris Game**

After the pipeline finishes, your Tetris game will be deployed. You can access it by navigating to:

```
http://your-server-ip:8080
```

You should see the Tetris game running in your browser!

---

## **Conclusion**

In this lab, you learned how to:
1. Clone a **Canvas Tetris** project and set it up for deployment.
2. Create a **Dockerfile** to containerize the project.
3. Use **Docker Compose** to manage the container.
4. Automate the build and deployment process using a Jenkins pipeline.

With this setup, you can automate the deployment of your Canvas Tetris game (or any web application) using Jenkins and Docker.

Congratulations on completing **Lab 3.2: Integrating Jenkins with Docker**!

Happy Building!
