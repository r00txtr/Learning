# **Lab 2.5: Integrating Jenkins CI/CD with Vercel**

In this lab, you will learn how to integrate **Jenkins CI/CD** with **Vercel** to automate the deployment of a simple web application. Jenkins will be used to manage the build pipeline, while Vercel will host and deploy the web application. The following steps guide you through setting up a project in Jenkins, configuring the build pipeline, and automating the deployment to Vercel.

---

## **Table of Contents**

1. **Introduction**
2. **Step-by-Step Instructions**
    1. Create a New Jenkins Job
    2. Set Up the Application Files Locally
    3. Install Vercel CLI and Configure Environment
    4. Create Jenkinsfile for CI/CD
    5. Configure Jenkins to Use the Pipeline
    6. Trigger the Build and Monitor Pipeline
    7. Verify Deployment on Vercel
3. **Conclusion**

---

## **Introduction**

In this lab, you will:
- Set up a Jenkins job to automate the deployment process.
- Use **Vercel** as the deployment platform.
- Define a pipeline in Jenkins to handle build, testing, and deployment.
- Automate deployments to Vercel using **Vercel CLI**.

By the end of this lab, you will have a CI/CD pipeline in Jenkins that automatically deploys a simple web application to Vercel whenever changes are pushed to the repository.

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
2. Enter a project name (e.g., `Vercel-Deployment`).
3. Select **Pipeline** and click **OK**.

![Jenkins Create Pipeline Job](https://jenkins.io/doc/book/resources/screenshots/setup/jenkins-create-pipeline-job.png)  
*(Example Screenshot)*

---

### **Step 2: Set Up the Application Files Locally**

In this step, you will create the necessary files for the web application.

#### **2.1 Create the Project Directory**

Create a directory for your project and navigate into it:

```bash
mkdir ~/vercel-app
cd ~/vercel-app
```

#### **2.2 Create an `index.html` File**

Create a simple `index.html` file that will be deployed to Vercel:

```bash
vim index.html
```

Add the following content:

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Vercel CI/CD Deployment</title>
  </head>
  <body>
    <h1>Hello from Jenkins CI/CD with Vercel!</h1>
    <p>This is a simple web app deployed using Jenkins and Vercel.</p>
  </body>
</html>
```

---

### **Step 3: Install Vercel CLI and Configure Environment**

To deploy the application to Vercel, you'll need to install the **Vercel CLI** and configure it in your local environment.

#### **3.1 Install Node.js and Vercel CLI**

First, install **Node.js** and **Vercel CLI**:

```bash
curl -sL https://deb.nodesource.com/setup_16.x | sudo -E bash -
sudo apt-get install -y nodejs
npm install -g vercel
```

#### **3.2 Log in to Vercel**

Log in to your Vercel account:

```bash
vercel login
```

Follow the on-screen instructions to complete the login process.

#### **3.3 Link the Project to Vercel**

Link your project to Vercel:

```bash
vercel link
```

This will connect your local project to a Vercel project, allowing you to deploy it.

---

### **Step 4: Create Jenkinsfile for CI/CD**

You will now create a **Jenkinsfile** that defines the steps of your CI/CD pipeline, including deploying to Vercel.

#### **4.1 Create a Jenkinsfile**

In your project directory, create a **Jenkinsfile**:

```bash
vim Jenkinsfile
```

Add the following content:

```groovy
pipeline {
    agent any
    environment {
        VERCEL_TOKEN = credentials('vercel_token')
    }
    stages {
        stage('Install Dependencies') {
            steps {
                sh 'npm install -g vercel'
            }
        }
        stage('Deploy to Vercel') {
            steps {
                sh 'vercel --prod --token=$VERCEL_TOKEN'
            }
        }
    }
}
```

- The **VERCEL_TOKEN** environment variable is used to authenticate with Vercel.
- The pipeline installs the Vercel CLI and deploys the project to production.

---

### **Step 5: Configure Jenkins to Use the Pipeline**

#### **5.1 Set Up the Jenkins Pipeline**

In Jenkins, go to your **Vercel-Deployment** project configuration:

1. Scroll down to the **Pipeline** section.
2. In the **Definition** field, select **Pipeline script from SCM**.
3. Set the **SCM** to **Git** and enter the repository URL (e.g., `https://github.com/your-username/vercel-app.git`).
4. In the **Script Path** field, enter `Jenkinsfile`.

#### **5.2 Add Vercel Token to Jenkins Credentials**

In Jenkins, go to **Manage Jenkins > Manage Credentials**:

1. Add a new credential of type **Secret text**.
2. Name the credential `vercel_token` and paste your Vercel token, which you can obtain from the [Vercel dashboard](https://vercel.com/account/tokens).

This will allow the pipeline to use the Vercel token securely during deployment.

---

### **Step 6: Trigger the Build and Monitor Pipeline**

#### **6.1 Trigger the Build**

To run the pipeline:

1. Go to your Jenkins dashboard.
2. Click on your **Vercel-Deployment** project.
3. Click **Build Now** to start the pipeline.

Jenkins will execute the steps in the Jenkinsfile, deploying the application to Vercel.

#### **6.2 Check the Build Logs**

To view the logs:

1. Click on the build number (e.g., `#1`).
2. Select **Console Output** to see detailed logs of the build and deployment process.

![Jenkins Console Output](https://jenkins.io/doc/book/resources/screenshots/setup/jenkins-console-output.png)  
*(Example Screenshot)*

---

### **Step 7: Verify Deployment on Vercel**

Once the build is complete, check your Vercel dashboard to verify the deployment:

1. Go to the [Vercel dashboard](https://vercel.com/dashboard).
2. You should see your project listed, and a unique Vercel URL will be generated for the deployed application.

Open the URL in your browser, and you should see your `index.html` content:

```
Hello from Jenkins CI/CD with Vercel!
```

---

## **Conclusion**

In this lab, you learned how to:
- Set up a Jenkins pipeline to automate the deployment of a web application.
- Use **Vercel** as a cloud hosting platform.
- Integrate **Vercel CLI** into a Jenkins pipeline for continuous deployment.
- Deploy a simple web application using Jenkins and Vercel.

This setup enables seamless and automated deployments to Vercel, streamlining your CI/CD workflow.

Congratulations on completing **Lab 2.5: Integrating Jenkins CI/CD with Vercel**!

Happy Automating!
