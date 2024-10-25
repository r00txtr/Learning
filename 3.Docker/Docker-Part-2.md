## Step 3: Update the Application

In part 2, you containerized a simple todo application. Now, you will update the application, rebuild the Docker image, and replace the running container with the updated version. You'll also learn how to share your Docker image on Docker Hub, run it on a different instance, and troubleshoot issues.

### 3.1 Update the Source Code

First, let's update the text displayed when there are no todo items in the list.

1. Open the `src/static/js/app.js` file in a text editor.
2. Navigate to line 56 and update the text as follows:

**Before:**

```javascript
<p className="text-center">No items yet! Add one above!</p>
```

**After:**

```javascript
<p className="text-center">You have no todo items yet! Add one above!</p>
```

This change updates the "empty state" message shown when there are no todo items.

### 3.2 Build the Updated Docker Image

Now that the code is updated, you need to rebuild the Docker image to include the changes.

1. Open the terminal and make sure you're in the `getting-started-app` directory.
2. Run the following command to rebuild the Docker image:

```bash
docker build -t getting-started .
```

This command will build the updated image with the new "empty text."

### 3.3 Remove the Old Container

Before running the updated container, you should remove the old one.

1. List all running containers to find the container ID:

```bash
docker ps
```

2. Stop and remove the old container using its ID:

```bash
docker stop <container_id>
docker rm <container_id>
```

### 3.4 Run the Updated Container

Now, start a new container with the updated code using the command below. This will map port 3000 inside the container to port 3000 on your local machine:

```bash
docker run -dp 127.0.0.1:3000:3000 getting-started
```

Visit `http://127.0.0.1:3000` in your browser to see the updated application with the new "empty state" message.

---

## Step 4: Share the Application

Now that your Docker image is ready, you can share it using Docker Hub, a public Docker registry. Follow the steps below to push your Docker image to Docker Hub and run it on a different instance.

### 4.1 Push the Image to Docker Hub

#### Step 1: Log In to Docker Hub

If you haven’t logged in yet, you need to sign in using the following command. Replace `YOUR-USER-NAME` with your Docker ID:

```bash
docker login -u YOUR-USER-NAME
```

Enter your password when prompted.

#### Step 2: Tag the Image

To push the image to Docker Hub, tag it with your Docker username. Replace `YOUR-USER-NAME` with your Docker Hub username:

```bash
docker tag getting-started YOUR-USER-NAME/getting-started
```

This command adds a new tag to your Docker image.

#### Step 3: Push the Image

Now, push the tagged image to Docker Hub:

```bash
docker push YOUR-USER-NAME/getting-started
```

You’ll see a message indicating that Docker is pushing the image to the Docker Hub repository.

---

## Step 5: Run the Image on a New Instance

Once the image is on Docker Hub, you can run it on a new instance or system that doesn't have the image stored locally. To try this out, you can use **Play with Docker**, an online platform that allows you to run Docker instances for free.

### 5.1 Using Play with Docker

To run the app on Play with Docker, follow these steps:

1. Visit the [Play with Docker](https://labs.play-with-docker.com/) website and log in with your Docker credentials.
2. Start a new session by clicking the **Start** button.
3. In the terminal provided by Play with Docker, pull and run your image from Docker Hub:

```bash
docker run -dp 0.0.0.0:3000:3000 YOUR-USER-NAME/getting-started
```

Now your app is running on Play with Docker!

### 5.2 Running on ARM (Apple Silicon) Devices

If you are using an ARM-based system like a Mac with Apple Silicon, you will need to build your Docker image to be compatible with Play with Docker (which uses the `amd64` platform). To do this, use the following command:

```bash
docker build --platform linux/amd64 -t YOUR-USER-NAME/getting-started .
```

Then, push this new image to Docker Hub using the same `docker push` command.

---

### Conclusion

Congratulations! You've successfully updated your Dockerized application, rebuilt the image, removed the old container, and deployed the new one. You also learned how to share your Docker image on Docker Hub and run it on different instances, like Play with Docker.

Docker makes it easy to build, share, and run containerized applications across different environments. Now, you can continue experimenting with Docker by building more complex applications or exploring Docker Compose for managing multi-container setups.
