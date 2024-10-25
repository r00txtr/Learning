# Lab 4.2: Exploring Dockerfile for Flask Apps

In this lab, we will create a simple Flask application, containerize it using Docker, and upload the image to DockerHub. Flask is a lightweight Python web framework that is commonly used to build simple web applications. By the end of this lab, you will know how to:
1. Create a basic Flask application.
2. Write a Dockerfile to containerize the Flask app.
3. Push the Docker image to DockerHub.
4. Run and test the Flask application in a Docker container.

---

## Table of Contents
1. **Introduction**
2. **Step-by-Step Instructions**
    1. Setting Up the Environment
    2. Writing the Flask Application
    3. Creating the Dockerfile
    4. Building, Tagging, and Pushing the Image
    5. Running the Container and Testing the Application
3. **Verification**
4. **Conclusion**

---

## Introduction

In this lab, we will create a simple Flask web application and use Docker to run it inside a container. Flask will serve a basic web page, and you will learn how to:
- Write a **Dockerfile** to define how Docker should build the image.
- Build and tag the Docker image.
- Push the image to **DockerHub** so it can be shared with others.
- Run the image as a container and test the application.

We will be executing these commands on `pod-[username]-node01`. Replace `[username]` and `[usernamedocker]` with your actual usernames.

---

## Step-by-Step Instructions

### Step 1: Setting Up the Environment

Before we start, ensure that you have a DockerHub account. If you don't, create one at [DockerHub Signup](https://hub.docker.com/signup).

#### 1.1 Log in to DockerHub

Log in to DockerHub with your Docker ID:

```bash
docker login
```

You will be prompted to enter your Docker username and password. Successful login allows you to push Docker images to your DockerHub repository.

#### 1.2 Create a Working Directory

We will create a new directory for this practice:

```bash
cd $HOME
mkdir latihan03
cd latihan03
```

This creates a directory named `latihan03` where we will place our Flask application and Dockerfile.

---

### Step 2: Writing the Flask Application

We will now create the files needed to run a simple Flask application.

#### 2.1 Create the Flask Application

Use the `vim` text editor to create a Python file named `app.py`:

```bash
vim app.py
```

Add the following code to the `app.py` file:

```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hey everyone! We have successfully set up Flask in a Docker container as part of our Docker learning journey!'

if __name__ == '__main__':
    app.run(debug=True, host='0.0.0.0')
```

This code defines a basic Flask application that returns a message when you access the root URL (`/`).

#### 2.2 Create the Requirements File

Next, create a `requirements.txt` file that lists the Python dependencies needed for the Flask app:

```bash
vim requirements.txt
```

Add the following dependencies to the `requirements.txt` file:

```
Flask==0.10.1
Werkzeug==1.0.0
Jinja2==2.8.1
MarkupSafe==1.1.1
itsdangerous==1.1.0
```

These dependencies are necessary for running the Flask application.

---

### Step 3: Creating the Dockerfile

Now, we will create a **Dockerfile** that specifies how to build the Docker image for the Flask application.

#### 3.1 Create the Dockerfile

Create a file named `Dockerfile` using `vim`:

```bash
vim Dockerfile
```

Add the following content to the Dockerfile:

```Dockerfile
# Use Ubuntu 16.04 as the base image
FROM ubuntu:16.04

# Create an application directory
RUN mkdir /app

# Install Python and Pip
RUN apt-get update -y && \
    apt-get install python-pip python-dev -y

# Copy the requirements file to the container
COPY ./requirements.txt /app

# Copy the application code to the container
COPY . /app

# Set the working directory
WORKDIR /app

# Install Python dependencies
RUN pip install -r requirements.txt

# Specify the entry point for the container
ENTRYPOINT ["python"]

# Specify the default command
CMD ["app.py"]
```

Let’s break down this Dockerfile:
- `FROM ubuntu:16.04`: Uses the Ubuntu 16.04 image as the base image.
- `RUN mkdir /app`: Creates a directory named `/app` inside the container to hold the application files.
- `RUN apt-get install`: Installs Python and pip, which are needed to run the Flask application.
- `COPY`: Copies the `requirements.txt` and the application code from the local directory to the `/app` directory inside the container.
- `WORKDIR /app`: Sets `/app` as the working directory for the container.
- `RUN pip install -r requirements.txt`: Installs the Python dependencies listed in the `requirements.txt` file.
- `ENTRYPOINT ["python"]`: Specifies that Python will be used to run the application.
- `CMD ["app.py"]`: Specifies that `app.py` should be executed when the container starts.

---

### Step 4: Building, Tagging, and Pushing the Image

#### 4.1 Build the Docker Image

Now, let’s build the Docker image for the Flask app. Use the following command:

```bash
docker build -t flask-latihan03 .
```

- `-t flask-latihan03`: Tags the image with the name `flask-latihan03`.
- `.`: Specifies that Docker should look for the Dockerfile in the current directory.

#### 4.2 Tag the Image with Your DockerHub Username

Tag the image with your DockerHub username so you can push it to your DockerHub repository:

```bash
docker tag flask-latihan03 [usernamedocker]/flask-latihan03:latest
```

Replace `[usernamedocker]` with your DockerHub username.

#### 4.3 Push the Image to DockerHub

Push the image to DockerHub using the following command:

```bash
docker push [usernamedocker]/flask-latihan03:latest
```

This uploads the image to your DockerHub repository so others can use it.

---

### Step 5: Running the Container and Testing the Application

#### 5.1 Run the Docker Container

Now, run the Docker container using the image you just built and pushed:

```bash
docker run -d -p 5000:5000 --name flask03 [usernamedocker]/flask-latihan03
```

- `-d`: Runs the container in detached mode (in the background).
- `-p 5000:5000`: Maps port 5000 on the host to port 5000 inside the container.
- `--name flask03`: Names the container `flask03`.

#### 5.2 Test the Flask Application

To verify that the Flask application is running, use `curl` to access it:

```bash
curl http://localhost:5000
```

You should see the following message as a response:

```
Hey, we have Flask in a Docker container!
```

---

## Verification

To ensure that the lab is completed successfully, check the following:
1. The **`flask-latihan03`** image is built and tagged with your DockerHub username.
2. The image is successfully pushed to DockerHub.
3. The Flask application is running inside the container and responds correctly when accessed via `curl` on port 5000.

---

## Conclusion

In this lab, you learned how to:
1. Build a simple Flask application and containerize it using Docker.
2. Write a **Dockerfile** that installs dependencies and runs the application.
3. Tag and push the Docker image to **DockerHub** for sharing.
4. Run the container and test the Flask application.

This lab gives you a solid foundation for building and deploying web applications using Docker. You can expand on this by adding more functionality to the Flask app or optimizing the Dockerfile for production environments.

### Example Output for `curl` Command:

```
$ curl http://localhost:5000
Hey, we have Flask in a Docker container!
```

Congratulations on completing **Lab 4.2: Exploring Dockerfile for Flask Apps**!

Happy Dockering!
