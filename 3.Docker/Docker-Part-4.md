### Step 6: Persist the Database Using Bind Mounts

In this step, you'll explore **bind mounts** and how they differ from volume mounts. While volume mounts are ideal for persistent storage of application data, bind mounts are useful for local development, allowing you to share directories from your host machine with the container. This setup lets you make live changes to your source code and see them reflected immediately inside the container, without needing to rebuild the Docker image.

---

### 6.1 Quick Comparison: Volume Mounts vs Bind Mounts

- **Volume Mounts**: Managed by Docker, volumes are stored in Docker-specific locations and are ideal for persisting data.
- **Bind Mounts**: Bind mounts directly map a host directory to a container directory. They are often used for development, as they reflect changes on the host in real-time inside the container.

Example comparison:

- **Volume mount**: `--mount type=volume,src=my-volume,target=/usr/local/data`
- **Bind mount**: `--mount type=bind,src=/path/to/data,target=/usr/local/data`

---

### 6.2 Trying Out Bind Mounts

You can quickly test how bind mounts work by mounting your `getting-started-app` directory from the host into a running container.

#### Step 1: Verify Directory Sharing

Ensure that your `getting-started-app` directory is in a location that Docker can access, based on your Docker Desktop settings for file sharing.

#### Step 2: Start a Container with a Bind Mount

Run an interactive bash session in an Ubuntu container and mount your app directory:

```bash
docker run -it --mount type=bind,src="$(pwd)",target=/src ubuntu bash
```

- The `--mount` flag creates a bind mount from the current directory (`src=$(pwd)`) to `/src` inside the container.

#### Step 3: Explore the Container

After running the command, you'll be inside the container. Check the directory contents:

```bash
cd /src
ls
```

You should see your app files listed (like `Dockerfile`, `package.json`, etc.). This confirms that the files from your host directory are now available inside the container.

#### Step 4: Create and Delete a File

1. Inside the container, create a new file:

   ```bash
   touch myfile.txt
   ls
   ```

   The file `myfile.txt` should appear in the `/src` directory inside the container.

2. Check your `getting-started-app` directory on the host. The file should also be visible there.

3. Now, delete `myfile.txt` from the host directory and check the container again. The file will be gone in both places, showing the real-time synchronization between the host and container.

#### Step 5: Stop the Container

Exit the interactive container session by pressing `Ctrl + D`.

---

### 6.3 Running Your App in a Development Container

In a typical development environment, you can use bind mounts to avoid rebuilding the container every time you make a code change. You'll now set up a development container with a bind mount and use **nodemon**, a tool that watches for changes in your code and restarts the application automatically.

#### Step 1: Start the Development Container

From your `getting-started-app` directory, run the following command to start the container with a bind mount:

```bash
docker run -dp 127.0.0.1:3000:3000 \
    -w /app --mount type=bind,src="$(pwd)",target=/app \
    node:18-alpine \
    sh -c "yarn install && yarn run dev"
```

Here's what the command does:

- `-dp 127.0.0.1:3000:3000`: Runs the container in detached mode and maps port 3000 of the container to port 3000 on your host.
- `-w /app`: Sets `/app` as the working directory inside the container.
- `--mount type=bind,src="$(pwd)",target=/app`: Mounts your current directory into the `/app` directory inside the container.
- `node:18-alpine`: Specifies the Node.js 18 Alpine image.
- `sh -c "yarn install && yarn run dev"`: Runs a shell command that installs the necessary dependencies and starts the development server using `nodemon`.

#### Step 2: Monitor the Container Logs

To watch the application logs and ensure everything is working, use:

```bash
docker logs -f <container-id>
```

You should see output like this:

```
[nodemon] 2.0.20
[nodemon] to restart at any time, enter `rs`
[nodemon] watching path(s): *.*
[nodemon] starting `node src/index.js`
Using sqlite database at /etc/todos/todo.db
Listening on port 3000
```

Once the server is up and running, you're ready to develop!

---

### 6.4 Develop the Application with Live Updates

#### Step 1: Make a Code Change

To see live updates in action, open the `src/static/js/app.js` file and make the following change:

```diff
- {submitting ? 'Adding...' : 'Add Item'}
+ {submitting ? 'Adding...' : 'Add'}
```

Save the file.

#### Step 2: Check the Changes

Open your web browser, refresh the app page, and you should see the button text change to "Add". Since the container is using a bind mount, Nodemon detects the file change and automatically restarts the application inside the container.

If the change isn't immediately reflected, give the container a few seconds to restart the server. You can check the logs with `docker logs <container-id>` to see if Nodemon has restarted the application.

---

### 6.5 Stop the Development Container

Once you're done, you can stop the container with:

```bash
docker stop <container-id>
```

---

### 6.6 Build a New Image

After completing your development and testing, you can build a new Docker image with the changes:

```bash
docker build -t getting-started .
```

This command rebuilds the image with the updated source code, ready for deployment or sharing.

---

### Conclusion

Using bind mounts for local development allows you to easily make changes to your code and see them reflected in real-time within your container. By combining bind mounts with tools like Nodemon, you can develop and debug applications inside containers without having to rebuild or restart them manually after every change.

In this chapter, you’ve learned how to:
- Persist data using bind mounts.
- Set up a development environment using a container.
- Use Nodemon to watch for file changes and automatically restart the server.

This approach enables an efficient and seamless development workflow, especially in containerized environments.
