# Getting Started with Docker: A Beginner-Friendly Guide

Docker is a powerful platform that simplifies building, deploying, and running applications inside containers. In this guide, you'll learn how to install Docker on Ubuntu using the `apt` repository, create a simple app with a Dockerfile, and run it in a Docker container.

## Step 1: Installing Docker Using the `apt` Repository

Before installing Docker, you need to set up Docker’s official repository and install the required packages.

### 1.1 Set Up Docker's apt Repository

#### Step 1: Update the Package List
Open your terminal and run the following command to ensure your package list is up to date:

```bash
sudo apt-get update
```

#### Step 2: Install Required Packages
You'll need some additional packages before installing Docker:

```bash
sudo apt-get install ca-certificates curl
```

#### Step 3: Add Docker’s GPG Key
Add Docker's GPG key, which helps verify the software's integrity:

```bash
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
```

#### Step 4: Add Docker’s Repository to apt Sources
Now, add Docker’s repository to your `apt` sources list:

```bash
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

### 1.2 Install Docker Engine

Once the repository is set up, install Docker Engine by running the following commands:

```bash
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin docker-compose -y
```

### 1.3 Fix Docker Permission Issues

To avoid permission errors when running Docker, adjust the permissions with the following commands:

```bash
sudo chown $USER /var/run/docker.sock
sudo usermod -aG docker $USER
sudo systemctl restart docker
```

### 1.4 Verify Docker Installation

To verify Docker is installed correctly, run the `hello-world` container:

```bash
sudo docker run hello-world
```

This will download a test image, run it in a container, and display a confirmation message if the installation is successful.

---

## Step 2: Creating a Simple Application with Docker

In this section, you'll create a simple app, define a Dockerfile, and build a Docker image.

### 2.1 Clone the Application Repository

First, you need to clone an example app from GitHub:

```bash
git clone https://github.com/docker/getting-started-app.git
```

Navigate to the cloned repository:

```bash
cd getting-started-app
```

You should see the following files and folders:

```
├── getting-started-app/
│   ├── .dockerignore
│   ├── package.json
│   ├── README.md
│   ├── spec/
│   ├── src/
│   └── yarn.lock
```

### 2.2 Creating the Dockerfile

A **Dockerfile** is a script containing a set of instructions that Docker uses to build an image. In this case, you'll create a Dockerfile for the app.

1. Inside the `getting-started-app` directory, create a new file called `Dockerfile`:

   ```bash
   touch Dockerfile
   ```

2. Open the Dockerfile in a text editor and add the following content:

   ```dockerfile
   # Use an official Node.js runtime as a parent image
   FROM node:18-alpine

   # Set the working directory in the container
   WORKDIR /app

   # Copy the current directory contents into the container
   COPY . .

   # Install dependencies specified in package.json
   RUN yarn install --production

   # Expose port 3000 to the host machine
   EXPOSE 3000

   # Start the application
   CMD ["node", "src/index.js"]
   ```

#### Dockerfile Breakdown:

- `FROM` sets the base image, in this case, Node.js.
- `WORKDIR` sets the working directory inside the container.
- `COPY` copies the source code into the container.
- `RUN` installs necessary dependencies using Yarn.
- `EXPOSE` opens port 3000 to allow outside access.
- `CMD` specifies the command to start the app when the container is launched.

### 2.3 Build and Run the Docker Container

Now that you have the Dockerfile, you can build and run your containerized app.

#### Step 1: Build the Docker Image

Run the following command to build the Docker image, naming it `getting-started`:

```bash
docker build -t getting-started .
```

#### Step 2: Run the Docker Container

After building the image, run the container using the following command:

```bash
docker run -p 3000:3000 getting-started
```

This command maps port 3000 on your local machine to port 3000 inside the container. Your app will now be accessible at `http://localhost:3000`.

---

### Conclusion

Congratulations! You’ve successfully installed Docker, created a Dockerfile, and built a containerized app. Docker makes it easier to manage applications, ensuring that they run the same regardless of the environment. Feel free to explore further by trying more Docker features like multi-container applications with Docker Compose!
