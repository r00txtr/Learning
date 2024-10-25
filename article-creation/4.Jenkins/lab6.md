# **Lab 2.4: Running a Container with Jenkins**

In this lab, you will learn how to set up a simple web application using **Jenkins** and **Docker**. The application will be deployed using a Jenkins pipeline and run inside a Docker container. We will also use **Docker Compose** to manage the container, and define the steps in a **Jenkinsfile** to automate the entire process.

---

## **Table of Contents**

1. **Introduction**
2. **Step-by-Step Instructions**
    1. Create a New Jenkins Job
    2. Clone the Project Repository
    3. Set Up the Application Files
        - Dockerfile
        - Docker Compose
        - Jenkinsfile
        - HTML File
    4. Configure Jenkins Pipeline
    5. Verify Jenkins Pipeline and Container
    6. Testing and Verifying the Container
3. **Conclusion**

---

## **Introduction**

This lab will guide you through the process of:
- Setting up a Jenkins job to run a containerized application.
- Using Docker and Docker Compose to deploy a simple HTML web application.
- Automating the build and deployment process using a Jenkins pipeline.

By the end of this lab, you will have a Jenkins pipeline that builds and deploys a containerized application.

---

## **Step-by-Step Instructions**

### **Step 1: Create a New Jenkins Job**

#### **1.1 Log in to Jenkins**

Open Jenkins in your browser:

```
http://your-server-ip:8080
```

Enter your Jenkins credentials to log in.

#### **1.2 Create a New Pipeline Job**

1. On the Jenkins dashboard, click on **New Item**.
2. Enter a project name (e.g., `Docker-App`).
3. Select **Pipeline** and click **OK**.

![Jenkins Create Pipeline Job](https://jenkins.io/doc/book/resources/screenshots/setup/jenkins-create-pipeline-job.png)  
*(Example Screenshot)*

---

### **Step 2: Clone the Project Repository**

You will need a local repository to store your project files.

#### **2.1 Clone the Project Locally**

Clone a repository from GitHub or your own GitLab instance to your local environment:

```bash
cd ~/
git clone https://github.com/your-username/docker-app.git
cd docker-app
```

Replace `your-username` with your actual GitHub or GitLab username.

---

### **Step 3: Set Up the Application Files**

In this step, we will set up the necessary files for our Dockerized web application.

#### **3.1 Create a Dockerfile**

Create a `Dockerfile` to define the environment for your application:

```bash
vim Dockerfile
```

Add the following content:

```dockerfile
FROM httpd:latest
ADD . /usr/local/apache2/htdocs/
```

This Dockerfile creates a container using the Apache HTTP Server (httpd) and serves the content from the current directory.

#### **3.2 Create Docker Compose Configuration**

Create a `docker-compose.yml` file to define how Docker will run the container:

```bash
vim docker-compose.yml
```

Add the following content:

```yaml
version: '3'
services:
  app:
    container_name: app
    build: .
    ports:
      - "8080:80"
    restart: always
```

This file defines a service named `app`, built from the Dockerfile, and exposes port **8080** on your local machine.

#### **3.3 Create a Jenkinsfile**

Now, create a `Jenkinsfile` to define the pipeline steps:

```bash
vim Jenkinsfile
```

Add the following content:

```groovy
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                script {
                    sh 'docker-compose down --rmi all'
                    sh 'docker-compose up -d --build'
                }
            }
        }
    }
    post {
        always {
            script {
                sh 'docker ps'
            }
        }
    }
}
```

This Jenkinsfile defines a pipeline with a **Build** stage that uses Docker Compose to build and run the container.

#### **3.4 Create an HTML File**

Finally, create the `index.html` file that will be served by the Apache web server:

```bash
vim index.html
```

Add the following content:

```html
Hello, Jenkins Pipeline!
```

---

### **Step 4: Configure Jenkins Pipeline**

#### **4.1 Link the Pipeline to the Repository**

In the Jenkins dashboard, go to your **Docker-App** project configuration:

1. Scroll down to the **Pipeline** section.
2. In the **Definition** field, select **Pipeline script from SCM**.
3. Set the **SCM** to **Git** and enter the repository URL (e.g., `https://github.com/your-username/docker-app.git`).
4. In the **Script Path** field, enter `Jenkinsfile` (this tells Jenkins to use the `Jenkinsfile` we created).

#### **4.2 Save the Configuration**

Scroll down and click **Save** to save your project configuration.

---

### **Step 5: Verify Jenkins Pipeline and Container**

#### **5.1 Trigger the Build**

1. Go to your project page in Jenkins.
2. Click **Build Now** to start the pipeline.

Jenkins will pull the repository, execute the commands in the `Jenkinsfile`, and build and run the Docker container.

#### **5.2 Check the Build Logs**

To view the logs:

1. Click on the build number (e.g., `#1`).
2. Select **Console Output** to view detailed logs of the build process.

![Jenkins Console Output](https://jenkins.io/doc/book/resources/screenshots/setup/jenkins-console-output.png)  
*(Example Screenshot)*

If successful, Jenkins will build the Docker image and run the container.

---

### **Step 6: Testing and Verifying the Container**

#### **6.1 Check the Running Container**

Log into the machine where Jenkins is running and check the running Docker containers:

```bash
docker ps
```

You should see the `app` container running and bound to port **8080**.

#### **6.2 Access the Web Application**

Open your browser and navigate to `http://your-server-ip:8080`. You should see the content of the `index.html` file:

```
Hello, Jenkins Pipeline!
```

This confirms that your containerized web application is running successfully.

---

### **Step 7: Add and Push a New HTML File**

#### **7.1 Add a New HTML File**

To test further, add a new HTML file called `test.html`:

```bash
cd ~/docker-app
vim test.html
```

Add the following content:

```html
Hello from a new page!
```

#### **7.2 Push the New File to GitHub**

Add, commit, and push the new file to your GitHub repository:

```bash
git add test.html
git commit -m "Add test.html"
git push origin main
```

#### **7.3 Verify the New File**

Once the pipeline has run again, access the new file by going to:

```
http://your-server-ip:8080/test.html
```

You should see:

```
Hello from a new page!
```

---

## **Conclusion**

In this lab, you successfully set up a Jenkins pipeline to:
1. Build a Dockerized web application.
2. Automate the deployment process using a **Jenkinsfile**.
3. Verify the running container and test new changes.

This setup allows you to continuously deploy containerized applications using Jenkins and Docker.

### Example Output:

```bash
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                  NAMES
abc123              app:latest          "httpd-foreground"  5 minutes ago       Up 5 minutes        0.0.0.0:8080->80/tcp   app
```

Congratulations on completing **Lab 2.4: Running a Container with Jenkins**! You now have the skills to deploy applications using Jenkins and Docker.

Happy Building!
