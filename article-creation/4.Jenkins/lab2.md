### **How to Install Jenkins and Set Up Docker Agent Jenkins: A Beginner-Friendly Guide**

**Introduction**

Jenkins is a highly flexible and scalable tool that helps automate building, testing, and deploying software projects. You can enhance Jenkins by using agents to distribute tasks, speeding up processes like builds and tests. Using Docker as a Jenkins agent can be especially powerful, as it allows you to isolate environments and run jobs in containers. This guide will walk you through installing Jenkins and setting up a Docker-based Jenkins agent.

---

### **Table of Contents**
1. What is Jenkins?
2. What is a Jenkins Agent?
3. Installing Jenkins
4. Setting Up Jenkins Agent
5. Setting Up Docker as a Jenkins Agent
6. Verifying the Setup
7. Conclusion

---

### **1. What is Jenkins?**

Jenkins is an open-source automation server widely used for continuous integration and continuous deployment (CI/CD). It automates tasks such as code builds, tests, and deployments, freeing developers from repetitive tasks.

### **2. What is a Jenkins Agent?**

A Jenkins agent is an additional machine (physical or virtual) used to run jobs under the direction of the Jenkins master (now referred to as the controller). Agents help distribute the workload and can be tailored to run specific tasks such as builds, testing, or deployments.

---

### **3. Installing Jenkins**

Let’s start by installing Jenkins on your system.

#### Step 1: Update Your System

Before installing Jenkins, make sure your system is up-to-date:

```bash
sudo apt update && sudo apt upgrade -y
```

#### Step 2: Install Java

Jenkins requires Java to run. Install the necessary Java version with the following command:

```bash
sudo apt install openjdk-11-jdk -y
```

#### Step 3: Add Jenkins Repository

To install Jenkins, add its official repository:

```bash
curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
```

Add Jenkins to the package sources:

```bash
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
```

#### Step 4: Install Jenkins

Now, install Jenkins:

```bash
sudo apt update
sudo apt install jenkins -y
```

#### Step 5: Start Jenkins

Start and enable Jenkins to ensure it runs on boot:

```bash
sudo systemctl start jenkins
sudo systemctl enable jenkins
```

#### Step 6: Access Jenkins Dashboard

Once Jenkins is installed, open a web browser and navigate to:

```
http://your-server-ip:8080
```

Unlock Jenkins using the admin password, which you can retrieve with:

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

After entering the password, Jenkins will prompt you to install plugins. Choose **Install Suggested Plugins** for a basic setup.

---

### **4. Setting Up Jenkins Agent**

Agents allow Jenkins to distribute workloads. In this section, we’ll set up a Jenkins agent.

#### Step 1: Create a New Node in Jenkins

1. Go to **Manage Jenkins** → **Manage Nodes and Clouds** → **New Node**.
2. Name the new node (e.g., `Docker-Agent`), choose **Permanent Agent**, and click **OK**.

#### Step 2: Fill in the Node Information

1. **Remote root directory**: `/home/jenkins` (or another directory where Jenkins will store job-related files).
2. **Launch method**: Choose **Launch agent by connecting it to the controller**.

#### Step 3: Save and Generate Launch Command

After saving the configuration, Jenkins will provide you with a launch command that you'll need to run on the agent machine to connect it to Jenkins. This command will look something like:

```bash
java -jar agent.jar -jnlpUrl http://your-server-ip/computer/docker-agent/slave-agent.jnlp -secret random-secret -workDir "/home/jenkins"
```

---

### **5. Setting Up Docker as a Jenkins Agent**

Now, let’s set up a Docker agent that Jenkins can use to run jobs inside Docker containers.

#### Step 1: Install Docker

To set up Docker, first install Docker CE (Community Edition) on the agent machine or the machine where Jenkins is running.

##### 1.1 Install Docker on Linux

Run the following commands to install Docker:

```bash
sudo apt update
sudo apt install -y apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt update
sudo apt install -y docker-ce
```

##### 1.2 Add Jenkins User to Docker Group

To allow Jenkins to run Docker commands, add the Jenkins user to the Docker group:

```bash
sudo usermod -aG docker jenkins
sudo systemctl restart docker
```

#### Step 2: Install Docker Plugin for Jenkins

Next, install the Docker plugin on Jenkins:

1. Go to **Manage Jenkins** → **Manage Plugins**.
2. Search for the **Docker** plugin and install it.

#### Step 3: Configure Jenkins to Use Docker as an Agent

Now, configure Jenkins to use Docker containers as agents.

1. Go to **Manage Jenkins** → **Configure Clouds** → **Add New Cloud**.
2. Select **Docker** from the list.
3. Set up the Docker host URL, typically `unix:///var/run/docker.sock`.

#### Step 4: Configure a Docker Agent Template

In the same **Configure Clouds** section, add a **Docker Agent Template**. Set the following:

- **Labels**: Give it a label like `docker-agent`.
- **Docker Image**: Specify the Docker image you want to use (for example, `jenkins/slave:latest` or any custom Docker image with the necessary tools).
- **Remote FS Root**: `/home/jenkins` (or any directory that will serve as the workspace).
- **Usage**: Choose **Only build jobs with label expressions matching this node** so that Jenkins only uses this Docker agent for jobs that specifically request it.

#### Step 5: Test Docker Agent Setup

Now, create a new Jenkins job to test the Docker agent setup:

1. Go to **New Item** and create a **Freestyle project**.
2. In the **Restrict where this project can be run** field, enter the label `docker-agent` (or whatever label you assigned earlier).
3. Add a simple build step, like running a shell command:

```bash
echo "Hello from Docker Agent"
```

4. Save and run the job. If everything is set up correctly, Jenkins will launch a Docker container for the agent and execute the job inside that container.

---

### **6. Verifying the Setup**

You can verify the Docker agent is working by checking the job logs. The output should indicate that the job was run on a Docker container, and you should see something like:

```
Started by user admin
Running as SYSTEM
Building remotely on docker-agent in workspace /home/jenkins/workspace/Test Build
Hello from Docker Agent
Finished: SUCCESS
```

---

### **7. Conclusion**

Congratulations! You’ve successfully installed Jenkins, set up a Jenkins agent, and configured a Docker-based Jenkins agent. By using Docker, you can isolate your build environments and avoid dependency conflicts, ensuring your Jenkins jobs run in clean, reproducible environments. 

With this setup, you now have the flexibility to scale your CI/CD pipelines using Docker agents and leverage containerization to streamline your builds and tests.

Happy automating!
