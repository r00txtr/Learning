### Lab 3.3: Integrate GitLab with Docker II (Using Docker Hub)

In this guide, we will walk through integrating GitLab with Docker, focusing on using Docker Hub. You will create a simple Node.js project, set up branches, configure a CI/CD pipeline, use Docker images from Docker Hub, and deploy the application. The steps are beginner-friendly, and we’ll also show how to securely add Docker Hub credentials to your GitLab CI/CD pipeline.

---

### Step 1: Create a New Project on GitLab

1. Open your browser and log in to your GitLab account.
2. Click on **"New Project"**.
3. Use the following specifications:
   - **Project Name**: `simple-node-js-username`
   - **Visibility Level**: Public

This will create a new repository where you'll store your project files.

#### Example Screenshot

![GitLab New Project](https://your-screenshot-url)

---

### Step 2: Clone the Project

Once your GitLab project is created, clone it to your local machine to start working on it. Open your terminal and run the following commands:

```bash
$ cd ~
$ git clone https://your-gitlab.com/USERNAME_GITLAB/simple-node-js-username.git
$ cd simple-node-js-username
```

Replace `USERNAME_GITLAB` with your actual GitLab username.

---

### Step 3: Create a Branch for Development

In Git, it's best practice to work on separate branches. Let’s create a new branch called `develop`:

```bash
$ git checkout -b develop
```

This creates and switches to the `develop` branch, where you’ll make all your changes before merging them into the main branch.

---

### Step 4: Create the `.gitlab-ci.yml` File

Now, we will create a GitLab CI/CD configuration file to automate the build and deployment process. This file defines the stages for your pipeline.

Create the file:

```bash
$ vim .gitlab-ci.yml
```

Add the following content to the `.gitlab-ci.yml` file:

```yaml
stages:
  - compile
  - deploy

compile-express:
  image: "node:8.10"
  stage: compile
  script:
    - npm install
  only:
    - develop

build-docker-image:
  image: docker:latest
  services:
    - docker:dind
  stage: deploy
  environment: staging
  variables:
    CONTAINER_IMAGE: $DOCKER_HUB_USERNAME/simple-node-js-username
  script:
    - 'echo $DOCKER_HUB_PASSWORD | docker login -u $DOCKER_HUB_USERNAME --password-stdin'
    - 'ls'
    - 'docker build -t $CONTAINER_IMAGE:latest .'
    - 'docker push $CONTAINER_IMAGE:latest'
    - 'docker-compose down --rmi all'
    - 'docker-compose up -d'
    - 'docker container ls '
  only:
    - develop
```

This configuration file sets up two stages: `compile` and `deploy`. It uses Docker images from Docker Hub and includes the necessary variables for Docker Hub authentication.

---

### Step 5: Add Docker Hub Credentials to GitLab

To securely store and use your Docker Hub credentials in GitLab, you will need to add them as environment variables in your project settings.

1. Go to your GitLab project.
2. Navigate to **Settings** > **CI/CD**.
3. Expand the **Variables** section.
4. Add the following variables:
   - **DOCKER_HUB_USERNAME**: Your Docker Hub username.
   - **DOCKER_HUB_PASSWORD**: Your Docker Hub password.

These variables will be used in the `.gitlab-ci.yml` file to log into Docker Hub and push your Docker images.

#### Example Screenshot

![GitLab Variables](https://your-screenshot-url)

---

### Step 6: Create the Dockerfile

Next, let’s create a `Dockerfile` for your Node.js application. This file defines the image configuration for the Docker container.

```bash
$ vim Dockerfile
```

Add the following content:

```dockerfile
FROM node:8.10

WORKDIR /app
ADD . .
RUN npm install

EXPOSE 3000
CMD ["node", "index"]
```

This Dockerfile specifies that we are using the Node.js 8.10 base image, copying the app code into the container, and installing dependencies.

---

### Step 7: Create the `docker-compose.yml` File

Create a `docker-compose.yml` file to manage your application services.

```bash
$ vim docker-compose.yml
```

Add the following configuration:

```yaml
services:
  app:
    image: $DOCKER_HUB_USERNAME/simple-node-js-username:latest
    hostname: simple-node-js
    container_name: simple-node-js
    volumes:
      - /simple-node-js
    ports:
      - "3000:3000"
```

This file defines a service called `app`, which uses the Docker image from your Docker Hub repository.

---

### Step 8: Create the `index.js` File

Now, let’s write the application code. Create the `index.js` file:

```bash
$ vim index.js
```

Add this simple Express server code:

```javascript
let express = require('express');
let app = express();
var port = process.env.PORT || 3000;
app.get('/', (req, res) => res.send('Hello, Docker Hub'));
app.listen(port, function () {
    console.log("App running on port " + port);
});
```

This is a basic Express.js application that listens on port 3000 and responds with "Hello, Docker Hub."

---

### Step 9: Create the `package.json` File

Next, initialize the project and create a `package.json` file to manage the project dependencies:

```bash
$ npm init
```

Follow the prompts to fill out the information for your project.

---

### Step 10: Install Express

Now, let’s install the necessary Express module:

```bash
$ npm install express --save
```

---

### Step 11: Register GitLab Runner for Docker

To run your CI/CD pipeline, you need to register a GitLab Runner that can execute Docker commands. Use the following command to register the runner:

```bash
$ sudo gitlab-runner register -n \
  --url https://your-gitlab.com \
  --registration-token REGISTRATION_TOKEN \
  --executor docker \
  --description "Docker Runner" \
  --docker-image "docker:latest" \
  --docker-volumes /var/run/docker.sock:/var/run/docker.sock
```

Replace `REGISTRATION_TOKEN` with the token provided in your GitLab project settings under **Settings** > **CI/CD** > **Runners**.

---

### Step 12: Verify the GitLab Runner

Verify that the GitLab Runner is working properly with the following command:

```bash
$ sudo gitlab-runner verify
```

---

### Step 13: Push Your Code to GitLab

Once everything is set up, you can push your code to GitLab to trigger the pipeline:

```bash
$ git add .
$ git commit -m "First Commit"
$ git push origin develop
```

This will upload your changes to the `develop` branch and start the CI/CD pipeline.

---

### Step 14: Monitor Pipeline Execution

You can monitor the status of your pipeline by visiting your GitLab project’s pipeline page. Use the following URL:

```bash
https://your-gitlab.com/[USERNAME_GITLAB]/simple-node-js-username/-/pipelines/
```

Replace `[USERNAME_GITLAB]` with your actual GitLab username.

---

By following these steps, you’ve successfully set up a GitLab project that integrates with Docker Hub, automating the build, test, and deployment processes using GitLab CI/CD.
