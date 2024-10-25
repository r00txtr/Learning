# **Lab 2.3: Setting Up Jenkins with a Shell Executor**

In this beginner-friendly lab, you will learn how to set up Jenkins to execute jobs using the **Shell Executor**. We’ll create a Jenkins pipeline to build a Docker image, configure Jenkins to execute shell commands, and verify that your pipeline runs successfully.

---

## **Table of Contents**

1. **Introduction**
2. **Step-by-Step Instructions**
    1. Create a Jenkins Job
    2. Configure Git and GitHub Repository in Jenkins
    3. Create Dockerfile and HTML File in the Repository
    4. Configure Jenkins Pipeline Script
    5. Verify Jenkins Pipeline
3. **Conclusion**

---

## **Introduction**

**Jenkins** is a powerful automation server that helps developers build, test, and deploy their projects. The **Shell Executor** allows Jenkins to run shell commands in jobs and pipelines, making it useful for automating tasks like building Docker images or running scripts. In this lab, we will create a simple project to automate the build of a Docker image containing an HTML file using a Jenkins pipeline.

---

## **Step-by-Step Instructions**

### **Step 1: Create a Jenkins Job**

#### **1.1 Log In to Jenkins**

First, access your Jenkins server by navigating to your server’s IP address or `localhost` in your web browser:

```
http://your-server-ip:8080
```

Enter your credentials to log in to Jenkins.

#### **1.2 Create a New Freestyle Job**

Once logged in, follow these steps to create a new project:

1. On the Jenkins dashboard, click on **New Item** in the left-hand menu.
2. Enter a name for your project (e.g., `Docker-Build-Project`).
3. Select **Freestyle project** and click **OK**.

![Create Jenkins Project](https://jenkins.io/doc/book/resources/screenshots/setup/jenkins-create-freestyle-project.png)  
*(Example Screenshot)*

#### **1.3 Configure the Job**

In the project configuration screen:

1. Under the **General** tab, add a description of the project if needed.
2. Scroll down to **Source Code Management**.

---

### **Step 2: Configure Git and GitHub Repository in Jenkins**

Jenkins needs access to your GitHub or GitLab repository where the project files are located.

#### **2.1 Add Git Repository**

In the **Source Code Management** section:

1. Select **Git**.
2. Enter the **Repository URL** (e.g., `https://github.com/your-username/docker-build-repo.git`).
3. If the repository is private, you can provide your credentials by clicking **Add** next to the **Credentials** field.

#### **2.2 Specify Branch**

In the **Branches to build** section, set the branch you want Jenkins to use (e.g., `main` or `master`).

---

### **Step 3: Create Dockerfile and HTML File in the Repository**

Before running the pipeline, make sure your repository contains the necessary files.

#### **3.1 Create an `index.html` File**

In your GitHub repository, create a simple `index.html` file with the following content:

```html
Hi, I'm [your-username]. I'm learning Jenkins.
```

Replace `[your-username]` with your own name or username.

#### **3.2 Create a `Dockerfile`**

Create a `Dockerfile` in your repository with the following content:

```Dockerfile
FROM nginx:latest
ADD index.html /usr/share/nginx/html/
```

This `Dockerfile` will use the official NGINX image and copy the `index.html` file to the default directory served by NGINX.

---

### **Step 4: Configure Jenkins Pipeline Script**

Now we’ll configure Jenkins to automate the process of building the Docker image using a pipeline.

#### **4.1 Add a Build Step**

In the Jenkins project configuration page:

1. Scroll down to the **Build** section.
2. Click on **Add build step** and select **Execute shell**.
3. Add the following script to build the Docker image:

```bash
docker build -t my-docker-image:1.0 .
docker images
```

This script will build the Docker image with the tag `my-docker-image:1.0` and list all Docker images to verify the build.

#### **4.2 Configure Post-Build Actions**

If you want Jenkins to send notifications after a build, you can set up **Post-build Actions**. For example, you can add email notifications or archive build artifacts.

#### **4.3 Save the Configuration**

After configuring the build steps, scroll down and click **Save** to apply the changes.

---

### **Step 5: Verify Jenkins Pipeline**

#### **5.1 Trigger the Build**

To run the job:

1. Go to your Jenkins dashboard.
2. Click on your project (`Docker-Build-Project`).
3. Click **Build Now** in the left-hand menu.

Jenkins will start running the build process. You can monitor the progress in the **Build History** section.

#### **5.2 Check the Build Logs**

To view the output of the build:

1. Click on the build number (e.g., `#1`) in the **Build History**.
2. Click **Console Output** to see the detailed log of the build process.

![Jenkins Console Output](https://jenkins.io/doc/book/resources/screenshots/setup/jenkins-console-output.png)  
*(Example Screenshot)*

The logs should show the Docker image being built successfully, and you should see the image listed at the end of the log.

#### **5.3 View Docker Images**

To verify the Docker image has been built:

1. Log in to the server where Jenkins is installed.
2. Run the following command to list all Docker images:

```bash
docker images
```

You should see an image with the tag `my-docker-image:1.0`.

---

## **Conclusion**

In this lab, you successfully set up Jenkins with a **Shell Executor** and created a Jenkins pipeline that builds a Docker image. You learned how to:

1. Set up a Jenkins job with Git as the source code repository.
2. Create a Dockerfile and build the Docker image using Jenkins.
3. Trigger a Jenkins build and view the results.

By automating the process of building Docker images, you can now use Jenkins to streamline more complex workflows and integrate them into your CI/CD pipeline.

### Example Output

```bash
$ docker images
REPOSITORY          TAG         IMAGE ID       CREATED         SIZE
my-docker-image     1.0         a1b2c3d4e5     2 minutes ago   133MB
```

This setup can now be extended to include testing and deploying applications. Keep experimenting and happy automating!
