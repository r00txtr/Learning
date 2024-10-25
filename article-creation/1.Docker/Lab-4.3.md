# Lab 4.3: Exploring Dockerfile (Quiz)

In this lab, we will create and run a Docker container for a web application using Nginx. The challenge involves building a Dockerfile and image, running the container, and ensuring the correct web page is served. This exercise will test your understanding of Dockerfiles, Docker images, and containers.

By the end of this lab, you will:
1. Clone a web application repository.
2. Create a Dockerfile to containerize the application using Nginx.
3. Build a Docker image from the Dockerfile.
4. Run the container and verify that it serves the web application, not the default Nginx page.

---

## Table of Contents
1. **Introduction**
2. **Step-by-Step Instructions**
    1. Setting Up the Environment
    2. Cloning the Web Application Repository
    3. Creating the Dockerfile
    4. Building and Running the Docker Image
    5. Testing the Web Application
3. **Verification**
4. **Conclusion**

---

## Introduction

In this lab, you will work with an existing web application hosted on GitHub and use Docker to containerize it. You will configure Nginx to serve the web application, instead of the default Nginx welcome page, and test it by browsing to the containerized app. We will use `pod-[username]-node01` for this lab, and all steps will be performed within a specific directory.

---

## Step-by-Step Instructions

### Step 1: Setting Up the Environment

#### 1.1 Create a Working Directory

First, create a new directory named `latihan-quiz01` where we will clone the web application repository and place our Dockerfile.

```bash
cd $HOME
mkdir latihan-quiz01
cd latihan-quiz01
```

This creates a directory named `latihan-quiz01` and navigates into it.

---

### Step 2: Cloning the Web Application Repository

#### 2.1 Clone the Repository

Next, we will clone the web application repository from GitHub into the `latihan-quiz01` directory.

```bash
git clone https://github.com/rivawahyuda/mywebsite.git
```

This command clones the repository into a subdirectory named `mywebsite`. The repository contains the files for a basic web application that we will serve using Nginx.

---

### Step 3: Creating the Dockerfile

Now that we have the web application files, we need to create a **Dockerfile** to configure Nginx to serve this application.

#### 3.1 Create the Dockerfile

Use the `vim` editor to create a Dockerfile in the `latihan-quiz01` directory:

```bash
vim Dockerfile
```

Add the following content to the Dockerfile:

```Dockerfile
# Use the latest version of the Nginx image
FROM nginx:latest

# Remove the default Nginx website files
RUN rm -rf /usr/share/nginx/html/*

# Copy the contents of the cloned repository to the Nginx web directory
COPY ./mywebsite /usr/share/nginx/html

# Expose port 80 to serve the website
EXPOSE 80
```

Explanation of the Dockerfile:
- `FROM nginx:latest`: This uses the latest Nginx image as the base image.
- `RUN rm -rf /usr/share/nginx/html/*`: This removes the default Nginx web page files.
- `COPY ./mywebsite /usr/share/nginx/html`: This copies the web application files from the cloned repository to the Nginx web directory.
- `EXPOSE 80`: This tells Docker to expose port 80 so that the Nginx server can serve the application over HTTP.

---

### Step 4: Building and Running the Docker Image

#### 4.1 Build the Docker Image

Next, build the Docker image using the Dockerfile you just created. We will name the image `latihan-quiz01-image`.

```bash
docker build -t latihan-quiz01-image .
```

This command:
- `-t latihan-quiz01-image`: Tags the image with the name `latihan-quiz01-image`.
- `.`: Instructs Docker to look for the Dockerfile in the current directory.

Docker will read the instructions in the Dockerfile and build the image.

#### 4.2 Run the Docker Container

Once the image is built, we will run a container from the image. We will expose port 80 and name the container `quiz01`.

```bash
docker run -d --name quiz01 -p 80:80 latihan-quiz01-image
```

This command:
- `-d`: Runs the container in detached mode.
- `--name quiz01`: Names the container `quiz01`.
- `-p 80:80`: Maps port 80 on the host to port 80 on the container.
- `latihan-quiz01-image`: The name of the image to run.

---

### Step 5: Testing the Web Application

#### 5.1 Test the Application in the Browser

Now that the container is running, we will test if the web application is being served correctly. Run the following `curl` command to test browsing:

```bash
curl http://localhost
```

If the setup is correct, the web application from the `mywebsite` repository should be displayed, instead of the default Nginx welcome page.

You should see the content of the web page that the cloned repository provides, indicating that Nginx is serving the custom website.

---

## Verification

To ensure that everything is set up correctly, verify the following:
1. The **`latihan-quiz01-image`** Docker image has been built successfully.
2. The **`quiz01`** container is running and exposing port 80.
3. When you access `http://localhost` using `curl`, the web application from the `mywebsite` repository is displayed, not the default Nginx web page.

---

## Conclusion

In this lab, you successfully:
1. Cloned a web application from GitHub.
2. Created a Dockerfile to configure Nginx to serve the web application.
3. Built and ran a Docker container to host the web application.
4. Tested the setup by accessing the application via HTTP.

This lab gives you a practical understanding of how to containerize web applications using Docker and Nginx, a common combination in modern web development.

### Example Output for `curl` Command:

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My Webpage</title>
</head>
<body>
    <h1>Welcome to my website!</h1>
    <p>This is a simple website served by Nginx in a Docker container.</p>
</body>
</html>
```

Congratulations on completing **Lab 4.3: Exploring Dockerfile (Quiz)**! You now have hands-on experience in building and running Docker containers for web applications.

Happy Dockering!
