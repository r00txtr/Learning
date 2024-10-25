### **How to Install Jenkins and Jenkins Agent: A Beginner-Friendly Guide**

**Introduction**

Jenkins is a popular open-source automation server used for building, testing, and deploying software projects. Its main strength lies in automating repetitive tasks in the software development lifecycle, from building code to deploying it into production. In this guide, you will learn how to install Jenkins and set up a Jenkins agent (sometimes called a Jenkins slave) to distribute jobs across multiple machines. This setup helps you scale your CI/CD pipelines efficiently.

---

### **Table of Contents**
1. What is Jenkins?
2. What is a Jenkins Agent?
3. Installing Jenkins
4. Setting Up Jenkins Agent
5. Verifying the Setup
6. Conclusion

---

### **1. What is Jenkins?**

Jenkins is a powerful automation tool that continuously integrates code changes, runs tests, and deploys updates. It works by triggering jobs (tasks) like building a software project when code is pushed to a repository.

### **2. What is a Jenkins Agent?**

A Jenkins agent is a separate machine that works under the direction of the Jenkins server (called the master or controller). The agent can be configured to run specific jobs, which helps in distributing workloads and speeding up tasks when working on large-scale projects.

For example, while Jenkins is installed on one server, you can have multiple agents (computers) running jobs in parallel, greatly reducing build times.

---

### **3. Installing Jenkins**

Here, we'll install Jenkins on an Ubuntu server, but the steps are similar for other platforms.

#### Step 1: Update the system

First, ensure your system is up to date:

```bash
sudo apt update && sudo apt upgrade -y
```

#### Step 2: Install Java

Jenkins requires Java to run. Install Java by running:

```bash
sudo apt install fontconfig openjdk-17-jdk -y
```

#### Step 3: Add Jenkins Repository

To install Jenkins, you need to add its repository and GPG key:

```bash
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
```

Next, add the repository to your sources list:

```bash
echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
```

#### Step 4: Install Jenkins

Now, update the package list and install Jenkins:

```bash
sudo apt update
sudo apt install jenkins -y
```

#### Step 5: Start Jenkins

After installation, start Jenkins using the following command:

```bash
sudo systemctl start jenkins
```

To ensure Jenkins starts at boot, enable it:

```bash
sudo systemctl enable jenkins
```

#### Step 6: Access Jenkins Dashboard

Now, open your browser and navigate to your server's IP address or `localhost` on port 8080:

```
http://your-server-ip:8080
```

You should see the Jenkins unlock screen. Jenkins provides a secret key for unlocking, which you can retrieve by running:

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Copy the password and paste it into the unlock field in the Jenkins UI.

#### Step 7: Install Suggested Plugins

Once logged in, Jenkins will prompt you to install plugins. Click on **Install Suggested Plugins** to set up the basic environment.

#### Example:

Here’s what the Jenkins dashboard looks like after installation:

![Jenkins Dashboard Screenshot](https://www.jenkins.io/doc/book/resources/screenshots/setup/jenkins-welcome-dashboard.png)

---

### **4. Setting Up Jenkins Agent**

A Jenkins agent allows Jenkins to delegate jobs to other machines, which can be useful for speeding up builds or testing across multiple environments.

#### Step 1: Install Java on the Agent Machine

Since Jenkins agents require Java to run, start by installing Java on the agent machine:

```bash
sudo apt install openjdk-17-jdk -y
```

#### Step 2: Configure the Agent in Jenkins

Go to the Jenkins dashboard, and under **Manage Jenkins**, select **Manage Nodes and Clouds**. Here, click on **New Node**.

1. Give the node a name, select **Permanent Agent**, and click **OK**.
2. Fill in the node details:
   - **Remote root directory**: The directory on the agent where Jenkins files will be stored.
   - **Launch method**: Choose **Launch agent by connecting it to the master**.

#### Step 3: Launch Agent on Remote Machine

After setting up the agent in Jenkins, you will get a command to run on the agent machine. This command will look something like this:

```bash
java -jar agent.jar -jnlpUrl http://your-server-ip/computer/agent-name/jenkins-agent.jnlp -secret random-generated-secret -workDir "/home/jenkins"
```

Run this command on the agent machine to connect it to Jenkins.

#### Step 4: Verify the Agent

Once the agent is connected, you should see it listed as online under **Manage Jenkins** > **Manage Nodes and Clouds**.

Example:

![Jenkins Agent Node Screenshot](https://jenkins.io/doc/book/resources/screenshots/setup/jenkins-agent-node-setup.png)

---

### **5. Verifying the Setup**

Now that Jenkins and the agent are installed, let’s verify the setup by running a job on the agent.

1. Go to **New Item** on the Jenkins dashboard.
2. Create a **Freestyle project** and name it something like “Test Build.”
3. In the **Build Environment** section, select **Restrict where this project can be run** and specify the agent's name.
4. Add a simple build step, like running a shell command:

```bash
echo "Hello from Jenkins Agent"
```

5. Save and run the job. The console output should show that the job is running on the agent.

---

### **6. Conclusion**

You’ve successfully installed Jenkins and set up an agent to distribute jobs! This basic configuration helps you get started with CI/CD pipelines that can run across multiple machines, allowing you to scale your workflows.

With Jenkins and agents set up, you can now automate builds, tests, and deployments more efficiently. Happy automating!
