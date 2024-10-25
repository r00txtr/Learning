# Lab 4.1: Exploring Dockerfile

In this lab, we will dive into **Dockerfiles**, which are used to create Docker images. A **Dockerfile** contains a series of instructions that Docker reads and executes to assemble an image. By the end of this lab, you will:
1. Build and run a Docker image from an existing repository.
2. Create a custom Dockerfile to build and run a simple image with a fun command.

---

## Table of Contents
1. **Introduction**
2. **Dockerfile (Practice 01)**
    1. Clone Repository
    2. Build and Run the Image
    3. Test the Container
3. **Dockerfile (Practice 02)**
    1. Create a Custom Dockerfile
    2. Build and Run the Image
4. **Verification**
5. **Conclusion**

---

## Introduction

A **Dockerfile** defines the steps needed to create a Docker image, specifying the base image, commands, and other configuration settings. In this lab, we will:
- Use an existing repository with a Dockerfile to build and run an image.
- Create a custom Dockerfile from scratch to explore how simple commands can be used to create and run custom images.

We will execute these tasks on `pod-[username]-node01`. Replace `[username]` with your actual username.

---

## Dockerfile (Practice 01)

In this first practice, we will use a repository containing a pre-configured Dockerfile and build an image from it. After that, we will run the image and expose the application on port 8080.

### 1.1 Clone the Repository for Training

First, we need to clone the repository that contains the project and its Dockerfile.

```bash
git clone https://github.com/r00txtr/docker-node-hello.git --config core.autocrlf=input latihan01
```

This command clones the repository into a directory named `latihan01`. The repository contains a simple Node.js application with a pre-built Dockerfile.

### 1.2 Enter the Directory

Next, navigate into the cloned directory:

```bash
cd latihan01
```

You should see the project files, including a `Dockerfile` that defines how to build the image for this Node.js application.

### 1.3 Build the Docker Image

Now, we will build the Docker image using the Dockerfile provided in the repository. We will tag the image as `node-latihan01`:

```bash
docker build -t node-latihan01 .
```

- `-t node-latihan01`: Tags the image with the name `node-latihan01`.
- `.`: Refers to the current directory where the Dockerfile is located.

Docker will now go through the instructions in the Dockerfile and build the image.

### 1.4 Run the Container and Expose Port 8080

Once the image is built, we can run the container. We will expose port 8080 to make the application accessible.

```bash
docker run -d --rm --name node-latihan01 -p 8080:8080 node-latihan01
```

This command:
- Runs the container in detached mode (`-d`).
- Removes the container after it stops (`--rm`).
- Exposes port 8080 from the container to port 8080 on the host (`-p 8080:8080`).

### 1.5 Access the Container

Now, let’s verify that the container is running by accessing the application:

```bash
curl http://localhost:8080
```

If everything is set up correctly, you should see the output from the Node.js application, which will be a simple "Hello World!" message.

---

## Dockerfile (Practice 02)

In this second practice, we will create our own Dockerfile from scratch. The goal is to create a fun image that uses the **whalesay** image to display a random fortune.

### 2.1 Create a New Directory

We will create a new directory to hold our custom Dockerfile and related files:

```bash
cd $HOME
mkdir latihan02
cd latihan02
```

### 2.2 Create the Dockerfile

Now, we will create a new Dockerfile using the `vim` editor (or any other text editor you prefer):

```bash
vim Dockerfile
```

Inside the Dockerfile, add the following content:

```Dockerfile
# Use whalesay image as a base image
FROM mwar8205/whalesay:latest

# Install fortunes
RUN apt -y update && apt install -y fortunes

# Execute command
CMD /usr/games/fortune -a | cowsay
```

Let’s break this Dockerfile down:
- `FROM mwar8205/whalesay:latest`: This uses the **whalesay** image as the base image, which contains a talking whale command.
- `RUN apt -y update && apt install -y fortunes`: This installs the `fortune` command, which generates random quotes.
- `CMD /usr/games/fortune -a | cowsay`: This command runs `fortune`, pipes its output to `cowsay` (which makes the whale "speak"), and displays the fortune.

### 2.3 Build the Docker Image

Now, build the image using the Dockerfile you just created:

```bash
docker build -t docker-whale .
```

This command builds the image and tags it as `docker-whale`.

### 2.4 Display the Created Image

You can verify that the image was created by listing all Docker images:

```bash
docker image ls
```

Look for the `docker-whale` image in the list.

### 2.5 Test the Image

Now, run the image to see the whale display a random fortune:

```bash
docker run docker-whale
```

The output should be a random fortune message displayed with the whale, like this:

```
 ________________________________
/ You will have a pleasant surprise. \
 --------------------------------
     \
      \
        .--.
       |o_o |
       |:_/ |
      //   \ \
     (|     | )
    /'\_   _/`\
    \___)=(___/
```

### 2.6 Display Running Containers

You can check if the container is still running (it might have exited after displaying the fortune) by using:

```bash
docker ps
```

If the container has already stopped, list all containers (including stopped ones) using:

```bash
docker container ls -a
```

---

## Verification

To ensure that the lab was completed successfully, verify the following:
1. The **`node-latihan01`** image has been built and successfully run. You should be able to access it by running `curl localhost:8080`.
2. The **`docker-whale`** image has been created, and it successfully displays a random fortune with a talking whale when run.

---

## Conclusion

In this lab, you have explored:
1. How to build a Docker image from a Dockerfile.
2. How to run a Docker container and expose it on a specific port.
3. How to create a simple Dockerfile from scratch and use it to run a fun command.

Dockerfiles are a powerful way to automate the process of building Docker images, and by mastering them, you can create customized images for any project.

### Screenshot Example

Here is an example of what the fortune output might look like:

```
 ______________________________________
/ You will have a pleasant surprise.    \
 --------------------------------------
     \
      \
        .--.
       |o_o |
       |:_/ |
      //   \ \
     (|     | )
    /'\_   _/`\
    \___)=(___/
```

Congratulations on completing **Lab 4.1: Exploring Dockerfile**! You now have a solid foundation in creating and using Dockerfiles.

Happy Dockering!
