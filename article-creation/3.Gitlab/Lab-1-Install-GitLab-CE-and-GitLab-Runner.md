# Lab 1: Install GitLab CE and GitLab Runner

In this lab, you will learn how to install **GitLab Community Edition (CE)** and **GitLab Runner** on your server. GitLab is a popular platform for version control and CI/CD pipelines, while GitLab Runner is a tool that works with GitLab CI/CD to run jobs.

---

## Table of Contents
1. **Introduction**
2. **Step-by-Step Instructions**
    1. Install GitLab CE
    2. Configure GitLab CE
    3. Install Docker
    4. Install GitLab Runner
    5. Register GitLab Runner with Docker Executor
3. **Verification**
4. **Conclusion**

---

## Introduction

In this lab, we will:
- Install **GitLab CE** for managing repositories and CI/CD pipelines.
- Install **GitLab Runner**, which is used to run GitLab jobs.
- Configure **Docker** as an executor for GitLab Runner to run builds inside isolated Docker containers.

---

## Step-by-Step Instructions

### Step 1: Install GitLab CE

GitLab CE (Community Edition) is the open-source version of GitLab.

#### 1.1 Update the System

Start by updating the system:

```bash
sudo apt update && sudo apt upgrade -y
```

#### 1.2 Install Required Packages

Install the required packages like **curl**, **OpenSSH**, and **Postfix**:

```bash
sudo apt install -y curl openssh-server ca-certificates tzdata postfix
```

During **Postfix** installation, choose **Internet Site** for sending email notifications.

#### 1.3 Install GitLab CE

Now, run the following commands to install GitLab CE:

```bash
curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh | sudo bash
sudo apt install gitlab-ce
```

---

### Step 2: Configure GitLab CE

#### 2.1 Update the GitLab Configuration

Edit the GitLab configuration file:

```bash
sudo nano /etc/gitlab/gitlab.rb
```

Look for the line with `external_url` and set it to the IP address of your VM or `http://localhost` if you are running it locally:

```ruby
external_url 'http://your-vm-ip'
```

Save and close the file.

#### 2.2 Reconfigure GitLab

After updating the configuration, run the following command to reconfigure GitLab:

```bash
sudo gitlab-ctl reconfigure
```

---

### Step 3: Install Docker

Docker will be used as the executor for GitLab Runner. You will need Docker CE (Community Edition) for this setup.

#### 3.1 Add Docker GPG Key and Repository

Add Docker's official GPG key and repository to your system:

```bash
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

#### 3.2 Install Docker

Update the package list and install Docker:

```bash
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y
```

#### 3.3 Set Docker Permissions

Make sure Docker is accessible by the current user:

```bash
sudo chown $USER /var/run/docker.sock
sudo usermod -aG docker $USER
sudo systemctl restart docker
```

#### 3.4 Install Docker Compose

Download and install Docker Compose:

```bash
VERSION=$(curl --silent https://api.github.com/repos/docker/compose/releases/latest | jq .name -r)
DESTINATION=/usr/local/bin/docker-compose
sudo curl -L https://github.com/docker/compose/releases/download/${VERSION}/docker-compose-$(uname -s)-$(uname -m) -o $DESTINATION
sudo chmod 755 $DESTINATION
```

Check if Docker Compose is installed:

```bash
docker-compose --version
```

---

### Step 4: Install GitLab Runner

GitLab Runner is a tool used to run CI/CD jobs for GitLab.

#### 4.1 Download and Install GitLab Runner

Download and install GitLab Runner:

```bash
sudo wget -O /usr/bin/gitlab-runner https://gitlab-runner-downloads.s3.amazonaws.com/latest/binaries/gitlab-runner-linux-amd64
sudo chmod +x /usr/bin/gitlab-runner
```

#### 4.2 Create GitLab Runner User

Create a new system user for GitLab Runner:

```bash
sudo useradd --comment 'GitLab Runner' --create-home gitlab-runner --shell /bin/bash
```

#### 4.3 Install and Start GitLab Runner

Install GitLab Runner with the newly created user:

```bash
sudo gitlab-runner install --user=gitlab-runner --working-directory=/home/gitlab-runner
```

Start GitLab Runner:

```bash
sudo gitlab-runner start
```

Check the status:

```bash
sudo gitlab-runner status
```

---

### Step 5: Register GitLab Runner with Docker Executor

To run CI/CD jobs inside Docker containers, you will configure the GitLab Runner with Docker as the executor.

#### 5.1 Set Docker Permissions for GitLab Runner

Add the **gitlab-runner** user to the Docker group:

```bash
sudo usermod -aG docker gitlab-runner
sudo systemctl restart docker
```

#### 5.2 Verify Docker Access for GitLab Runner

Verify that the **gitlab-runner** user has access to Docker:

```bash
sudo -u gitlab-runner -H docker info
```

---

## Verification

You can verify the successful installation of GitLab and GitLab Runner by:
1. Accessing the GitLab web interface by navigating to `http://your-vm-ip` in a browser.
2. Verifying that the GitLab Runner is running by using the command:

```bash
sudo gitlab-runner status
```

3. Running a test CI/CD job to ensure that the GitLab Runner can execute jobs inside Docker containers.

---

## Conclusion

In this lab, you successfully:
- Installed **GitLab CE** and configured it with the correct `external_url`.
- Installed **Docker** and **Docker Compose** for running jobs inside containers.
- Installed and configured **GitLab Runner** and registered it with Docker as the executor.

Now, you have a fully functioning GitLab instance with a GitLab Runner ready to execute CI/CD jobs in Docker containers.

### Example Output:

```
GitLab Runner: running
Docker Compose: version 2.10.2
```

Congratulations on completing **Lab 1: Install GitLab CE and GitLab Runner**! You now have the skills to install and configure GitLab with Docker-based GitLab Runners for CI/CD. 

Happy Automating!
