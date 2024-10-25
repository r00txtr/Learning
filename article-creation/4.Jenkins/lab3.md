# **How to Create and Manage a Jenkins Project**

In this guide, you will learn how to create and manage a Jenkins project, configure your build settings, and manage code from a Git repository. Jenkins is an open-source automation tool that makes it easy to build, test, and deploy software in a continuous integration and continuous deployment (CI/CD) pipeline.

---

## **Table of Contents**

1. **Introduction**
2. **Step-by-Step Instructions**
    1. Create a New Project in Jenkins
    2. Configure a Git Repository in Jenkins
    3. Configure Build Steps
    4. Trigger a Build Manually
    5. Automate Builds Using Webhooks
    6. View Build History and Logs
3. **Conclusion**

---

## **Introduction**

Jenkins is a powerful automation tool used for continuous integration and deployment in software development. It allows teams to streamline tasks like building, testing, and deploying code to different environments.

In this tutorial, we will:
- Create a Jenkins project.
- Configure it to use a Git repository.
- Set up build steps and triggers.

By the end, you'll have a basic Jenkins project that can automatically build code from a Git repository.

---

## **Step-by-Step Instructions**

### **Step 1: Create a New Project in Jenkins**

#### 1.1 Login to Jenkins

To start, log in to your Jenkins instance. You can usually access Jenkins on port `8080` of your server or local machine by going to:

```
http://your-server-ip:8080
```

Enter your credentials to access the Jenkins dashboard.

#### 1.2 Create a New Project

Once logged in, you can create a new project in Jenkins:

1. From the Jenkins dashboard, click on **New Item** on the left-hand menu.
2. In the **Enter an item name** field, give your project a descriptive name, such as `My-Jenkins-Project`.
3. Select **Freestyle project** and click **OK**.

   ![Jenkins Create Project](https://jenkins.io/doc/book/resources/screenshots/setup/jenkins-create-freestyle-project.png) *(Example Screenshot)*

---

### **Step 2: Configure a Git Repository in Jenkins**

To build and deploy code from a Git repository, you need to configure Jenkins to connect to the repository.

#### 2.1 Specify the Git Repository URL

1. In the project configuration screen, scroll down to the **Source Code Management** section.
2. Select **Git** as the repository type.
3. Enter the URL of your Git repository. For example:

   ```bash
   https://github.com/your-username/your-repository.git
   ```

   ![Jenkins Git Config](https://jenkins.io/doc/book/resources/screenshots/setup/jenkins-git-config.png) *(Example Screenshot)*

#### 2.2 Add Repository Credentials

If your repository is private, you’ll need to provide Jenkins with the necessary credentials:

1. Click **Add** next to **Credentials**.
2. Select the appropriate credential type (such as SSH or username/password), enter your credentials, and click **OK**.

---

### **Step 3: Configure Build Steps**

Now that your Git repository is linked to Jenkins, it’s time to configure what Jenkins should do with the code it pulls.

#### 3.1 Add a Build Step

1. In the project configuration page, scroll down to the **Build** section.
2. Click **Add build step** and select **Execute shell**.
3. In the shell command field, add the commands needed to build your project. For example, if you are using a simple Java application, you might run:

   ```bash
   echo "Building the project..."
   mvn clean install
   ```

   ![Jenkins Build Step](https://jenkins.io/doc/book/resources/screenshots/setup/jenkins-build-steps.png) *(Example Screenshot)*

#### 3.2 Save the Configuration

Once you’ve set up your build steps, click **Save** at the bottom of the page to save your project configuration.

---

### **Step 4: Trigger a Build Manually**

Now that your Jenkins project is configured, it’s time to run a build manually to ensure everything is working as expected.

#### 4.1 Start a Build

1. Go to your project’s page by clicking on its name in the Jenkins dashboard.
2. Click **Build Now** on the left-hand menu to trigger a manual build.

   ![Jenkins Build Now](https://jenkins.io/doc/book/resources/screenshots/setup/jenkins-build-now.png) *(Example Screenshot)*

#### 4.2 View Build Progress

After starting the build, you’ll see a progress indicator in the build history. You can click on the build number (e.g., `#1`) to see detailed logs and progress of the build.

---

### **Step 5: Automate Builds Using Webhooks**

Jenkins can automatically trigger builds when changes are pushed to your Git repository by setting up webhooks.

#### 5.1 Set Up Git Webhook

1. Go to your Git repository settings (e.g., on GitHub or GitLab).
2. Navigate to the **Webhooks** section and click **Add Webhook**.
3. For the **Payload URL**, use the URL of your Jenkins server followed by `/github-webhook/` or `/gitlab-webhook/`. For example:

   ```
   http://your-server-ip:8080/github-webhook/
   ```

4. Set the content type to `application/json` and click **Add Webhook**.

#### 5.2 Configure Jenkins to Trigger Builds Automatically

In Jenkins, go to your project configuration:

1. Scroll down to the **Build Triggers** section.
2. Check the box that says **GitHub hook trigger for GITScm polling** (for GitHub) or **Build when a change is pushed to GitLab** (for GitLab).
3. Save the configuration.

Now, any changes pushed to your Git repository will automatically trigger a Jenkins build.

---

### **Step 6: View Build History and Logs**

You can monitor your project’s progress by checking the build history and logs.

#### 6.1 View Build History

On the project page, you’ll see a **Build History** section that lists all previous builds, along with their status (e.g., success or failure).

#### 6.2 View Build Logs

To view the logs for a specific build:

1. Click on the build number (e.g., `#1`) from the build history.
2. In the build’s details page, click **Console Output** to view detailed logs of the build process.

---

## **Conclusion**

In this guide, you learned how to create and manage a Jenkins project from scratch. You successfully:
- Created a Jenkins project.
- Linked a Git repository.
- Set up build steps.
- Triggered builds both manually and automatically.

Now, you're ready to set up continuous integration and deployment pipelines for your software projects in Jenkins!

---

Happy Building!
