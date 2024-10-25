# Lab 6.6: Build & Push Image from Portainer

In this lab, you will learn how to build and push a Docker image using the **Portainer** web interface. Portainer simplifies the process of managing Docker images and allows you to build images directly from a Dockerfile, tag them, and push them to a Docker registry such as Docker Hub. By the end of this lab, you will have successfully built a custom Nginx image, pushed it to Docker Hub, and verified that the image can be pulled and used.

---

## Table of Contents
1. **Introduction**
2. **Step-by-Step Instructions**
    1. Build a Docker Image from a Dockerfile in Portainer
    2. Push the Docker Image to Docker Hub
3. **Verification**
4. **Conclusion**

---

## Introduction

In this lab, you will:
1. Build a Docker image from a Dockerfile using Portainer’s **Web Editor**.
2. Push the built image to **Docker Hub**.
3. Verify the image and confirm it can be pulled and run.

Portainer provides a graphical interface to create images, manage containers, and interact with Docker registries, making it a useful tool for both beginners and advanced users.

---

## Step-by-Step Instructions

### Step 1: Build a Docker Image from a Dockerfile in Portainer

In this section, you will create a custom Nginx image using a Dockerfile in the Portainer web interface.

#### 1.1 Access the Image Creation Interface

1. Log in to the **Portainer** dashboard using your web browser:
   ```
   http://10.10.10.11:9000
   ```

2. From the Portainer dashboard, go to the **Images** menu.
3. Click on **+ Build a new Image** to begin creating a new image.

#### 1.2 Configure the Image Build

1. In the **Name** column, enter your Docker image name using the format:
   ```
   {your-docker-hub-username}/nginx:lab76
   ```
   Replace `{your-docker-hub-username}` with your actual Docker username (e.g., `Muhammad/nginx:lab76`).

2. Under **Build Method**, select **Web Editor**. This allows you to directly write and edit your Dockerfile in the Portainer interface.

#### 1.3 Write the Dockerfile

In the Web Editor, write the following Dockerfile:

```Dockerfile
FROM nginx:latest

ARG USERNAME=Muhammad

RUN echo "nginx running on image $USERNAME/nginx:lab76" > /usr/share/nginx/html/index.html

EXPOSE 80
```

- **FROM**: Specifies the base image (Nginx) from the latest version.
- **ARG**: Defines a build argument (`USERNAME`) that will be replaced with your Docker username.
- **RUN**: Creates a custom message in the Nginx default page (`index.html`).
- **EXPOSE**: Opens port 80 to serve Nginx.

Replace `{your-username}` with your actual username.

#### 1.4 Deploy the Image to Node01

1. Under the **Node** section, select `pod-[username]-node01` to specify the node where the image will be built.
2. Click **Build the Image** to begin the build process.
3. Monitor the output to ensure that the image is built successfully.
4. After the build completes, go to the **Images** menu to verify that the image was created. You should see your custom image listed.

---

### Step 2: Push the Docker Image to Docker Hub

Once the image is built, you will push it to **Docker Hub** so that it can be shared and pulled from anywhere.

#### 2.1 Add Docker Hub as a Registry

1. In the Portainer dashboard, click on the **Registries** menu.
2. Click **+ Add Registry** to configure a new Docker registry.
   
3. In the **Registry Provider** field, select **DockerHub**.
   
4. Fill in the details:
   - **Name**: `repo-{your-username}` (e.g., `repo-Muhammad`).
   - **Username**: Enter your Docker Hub username.
   - **Password**: Enter your Docker Hub password.
   
5. Click **Add Registry** to save the registry configuration.

#### 2.2 Tag the Image and Push it to Docker Hub

1. Return to the **Images** menu in Portainer and locate the image you just built (`{your-username}/nginx:lab76`).
2. Click on the image name to open the image details.
3. In the **Registry** column, select the registry you added earlier (`repo-{your-username}`).
4. In the **Image** field, enter the new image tag:
   ```
   {username docker}/nginx:lab76
   ```
   Replace `{username docker}` with your actual Docker Hub username.
   
5. Click **Tag** to apply the new tag to the image.

#### 2.3 Push the Image to Docker Hub

1. After tagging the image, click **Push to Registry** to upload the image to Docker Hub.
2. Monitor the output to ensure that the image is pushed successfully.
3. Once the image is pushed, you can verify the upload by checking your Docker Hub repository or by pulling the image from another machine.

---

## Verification

To ensure that the lab was completed successfully, verify the following:

### 1. Verify the Image is Created and Pushed

Check that the image is listed in Docker Hub by visiting your Docker Hub repository at `https://hub.docker.com/r/{username docker}`. The image `nginx:lab76` should be visible.

### 2. Verify the Dockerfile Build Arguments

Run the following command on any Docker host to check the image history and verify that the `ARG` variable was set correctly:

```bash
docker history {username docker}/nginx:lab76
```

Look for the line containing `ARG USERNAME={your-username}`, which confirms that the build argument was applied during the image creation.

### 3. Run the Image in the Background

To ensure the image is functional, run the image in detached mode using the following command:

```bash
docker run -d -p 8080:80 {username docker}/nginx:lab76
```

Open your browser and navigate to `http://localhost:8080`. You should see the custom message in the Nginx welcome page:

```
nginx running on image {username docker}/nginx:lab76
```

This confirms that the custom image was built and is running successfully.

---

## Conclusion

In this lab, you learned how to:
1. Build a Docker image from a Dockerfile using Portainer’s **Web Editor**.
2. Push the custom image to **Docker Hub** using Portainer.
3. Verify the image and run it to confirm that it works as expected.

Portainer simplifies the Docker image build and push process, making it easier to manage images and share them across environments. By leveraging Portainer’s web interface, you can streamline container management and reduce the need for command-line interactions.

### Example Output of Nginx:

When you visit the running container at `http://localhost:8080`, you should see the following message:

```
nginx running on image {username docker}/nginx:lab76
```

Congratulations on completing **Lab 6.6: Build & Push Image from Portainer**! You now have a solid understanding of how to build, tag, and push Docker images using Portainer.

Happy Dockering!
