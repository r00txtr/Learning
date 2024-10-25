### Lab 3.4: Quiz Lab 1: Integrate GitLab with Docker III (Using Docker Hub)

In this guide, we will explore how to further integrate GitLab with Docker by extending what we worked on in Lab 3.3. We will create a new branch, set up GitLab runners, update the GitLab CI/CD pipeline, and automate the deployment process with Docker. This step-by-step tutorial is beginner-friendly, ensuring you understand the core concepts with examples along the way.

---

### Step 1: Create a New Branch - `feature-project`

Let’s begin by creating a new branch called `feature-project` from the existing `develop` branch.

1. Navigate to your terminal and ensure you're inside the project directory.
2. Run the following commands to create and switch to the new branch:

```bash
$ git checkout develop
$ git checkout -b feature-project
```

This creates a new branch, `feature-project`, where you will make your changes.

---

### Step 2: Edit the `index.js` File

Next, you will edit the existing `index.js` file to output a custom message.

1. Open the `index.js` file in your favorite text editor.
2. Replace the current content with the following code:

```javascript
let express = require('express');
let app = express();
var port = process.env.PORT || 3000;

app.get('/', (req, res) => res.send('Hello World! - username'));

app.listen(port, function () {
    console.log("App running on port " + port);
});
```

This code updates the output to `"Hello World! - username"` when the application is accessed.

---

### Step 3: Add a New GitLab Runner

You will now register a new GitLab runner, which will execute the tasks in the pipeline using Docker. Follow these steps:

1. Run the following command to register the runner:

```bash
sudo gitlab-runner register -n \
  --url https://your-gitlab.com \
  --registration-token REGISTRATION_TOKEN \
  --executor docker \
  --description "node01-docker-runner" \
  --docker-image "node:8.10" \
  --docker-volumes /var/run/docker.sock:/var/run/docker.sock \
  --tag-list "docker-build,node01-runner,pod-username-node01"
```

Replace `REGISTRATION_TOKEN` with the actual token from your GitLab project (found in **Settings** > **CI/CD** > **Runners**).

This step registers a new runner with tags `docker-build`, `node01-runner`, and `pod-username-node01`, which we will use in the pipeline configuration.

---

### Step 4: Update the GitLab CI/CD Pipeline

Now, let’s configure the `.gitlab-ci.yml` file to ensure the Docker image is automatically built and pushed to Docker Hub, and then deployed on the server.

1. Open the `.gitlab-ci.yml` file in your text editor.
2. Add the following configuration:

```yaml
stages:
  - compile
  - deploy

compile-express:
  image: node:8.10
  stage: compile
  script:
    - npm install
  tags:
    - node01-runner
  only:
    - feature-project

build-docker-image:
  image: docker:latest
  services:
    - docker:dind
  stage: deploy
  environment: staging
  variables:
    CONTAINER_IMAGE: docker.io/$DOCKER_HUB_USERNAME/simple-node-js-username
  script:
    - 'echo "$DOCKER_HUB_PASSWORD" | docker login -u "$DOCKER_HUB_USERNAME" --password-stdin'
    - 'ls'
    - 'docker build -t $CONTAINER_IMAGE:latest .'
    - 'docker push $CONTAINER_IMAGE:latest'
    - 'docker-compose down --rmi all'
    - 'docker-compose up -d'
    - 'docker container ls '
  tags:
    - node01-runner
  only:
    - feature-project
```

#### Explanation:

- The **`compile-express`** job installs dependencies using Node.js.
- The **`build-docker-image`** job builds and pushes the Docker image to Docker Hub, then runs it on the server using `docker-compose`.
- We are using tags `node01-runner` to ensure the jobs are executed by the appropriate runner.
- The **`CONTAINER_IMAGE`** variable points to the Docker Hub repository for the project.

---

### Step 5: Securely Save Credentials for Git

To securely store Git credentials, follow the StackOverflow guide [here](https://stackoverflow.com/questions/35942754/how-can-i-save-username-and-password-in-git).

You can save your GitLab username and password locally using the following command:

```bash
$ git config credential.helper store
```

After that, you will be prompted to enter your credentials when pushing to the remote repository for the first time.

---

### Step 6: Push Changes to GitLab

Now that everything is set up, push your changes to the `feature-project` branch.

```bash
$ git add .
$ git commit -m "Updated index.js and .gitlab-ci.yml"
$ git push origin feature-project
```

This will trigger the CI/CD pipeline on GitLab, which will execute the jobs in the `.gitlab-ci.yml` file.

---

### Step 7: Merge `feature-project` into `develop`

Once the pipeline passes successfully, merge the `feature-project` branch into the `develop` branch.

1. Go to your GitLab project on the web.
2. Create a merge request from `feature-project` to `develop`.
3. Once approved, merge the branch.
4. Ensure that the `feature-project` branch has been deleted after the merge process.

---

### Step 8: Verify the Pipeline

After merging, verify that the pipeline has completed successfully and that the Docker container is running correctly.

#### Verifications:
1. **Pipeline Status**: Check that the pipeline passed in GitLab under the **Pipelines** section.
2. **Container Update**: Ensure the content inside the container is updated.
3. **Check Running Container**: Verify that the container is running on the server with the following command:

```bash
$ docker-compose ps
```

You should see the container status as `Up` and active on port 3000.

---

### Conclusion

Congratulations! You have successfully completed Lab 3.4 by creating a new branch, configuring GitLab runners, updating the CI/CD pipeline, and automating the deployment of your Docker image. This guide has walked you through the process step by step, ensuring you understand how to integrate GitLab with Docker for real-world applications.

Let me know if you need further assistance or if you'd like to explore more advanced topics!