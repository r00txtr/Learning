# Lab 4.8: Quiz 2 - Using Variables in Ansible Playbooks

In this quiz, you will define and use variables in an Ansible playbook to perform administrative tasks on a managed host. By the end of this quiz, you will have written a playbook that installs necessary packages, ensures services are running, and deploys a custom HTML file to a web server. You will also test the web server from the control node.

---

## Table of Contents
1. **Introduction**
2. **Step-by-Step Instructions**
    1. Create the Working Directory and Configuration Files
    2. Write the Playbook
    3. Test the Web Server from the Control Node
    4. Run the Playbook
    5. Verify the Results
3. **Conclusion**

---

## Introduction

In this quiz, you will:
1. Use variables to define important settings such as package names, service names, and file content.
2. Ensure the required packages and services are installed and running.
3. Deploy a simple HTML page using the **copy** module.
4. Test the web service from the control node.

You will perform these tasks on the **Ansible controller node**, `pod-[username]-controller`, and manage the host `pod-[username]-managed2`. Replace `[username]` with your actual username throughout the lab.

---

## Step-by-Step Instructions

### Step 1: Create the Working Directory and Configuration Files

#### 1.1 Create the Working Directory

First, create a directory for the quiz and navigate into it:

```bash
cd ~
mkdir quiz-2
cd quiz-2
```

This directory will contain the Ansible configuration, inventory, and playbook files.

#### 1.2 Create the `ansible.cfg` File

Next, create the `ansible.cfg` file to define the location of the inventory file:

```bash
vim ansible.cfg
```

Add the following content:

```ini
[defaults]
inventory = ./inventory
remote_user = student
```

Explanation:
- **inventory = ./inventory**: Specifies the inventory file location.
- **remote_user = student**: Sets the default remote user to `student` for running playbooks.

#### 1.3 Create the Inventory File

Now, create the inventory file to define the target host (`pod-[username]-managed2`):

```bash
vim inventory
```

Add the following content:

```ini
[webservers]
pod-[username]-managed2
```

This defines a group called **webservers**, with `pod-[username]-managed2` as the host.

---

### Step 2: Write the Playbook

#### 2.1 Create the Playbook

Create a new playbook file to define the tasks and variables:

```bash
vim quiz-2_variables.yml
```

Add the following content to the playbook:

```yaml
---
- name: Install and configure web server
  hosts: webservers
  become: true
  vars:
    required_Pkg:
      - apache2
      - python3-urllib3
    web_Service: apache2
    content_File: "adinusa lab quiz variables - [username]"
    dest_File: /var/www/html/index.html

  tasks:
    - name: Install required packages
      apt:
        name: "{{ required_Pkg }}"
        state: latest
        update_cache: yes

    - name: Ensure apache2 service is started and enabled
      service:
        name: "{{ web_Service }}"
        state: started
        enabled: yes

    - name: Ensure specific content is in place
      copy:
        content: "{{ content_File }}"
        dest: "{{ dest_File }}"

- name: Test web server from control node
  hosts: localhost
  tasks:
    - name: Check web server response
      uri:
        url: http://pod-[username]-managed2/index.html
        status_code: 200
        return_content: yes
      register: web_result

    - name: Display web server content
      debug:
        var: web_result.content
```

Explanation:
- **vars**: Defines the variables:
  - **required_Pkg**: A list of packages (`apache2`, `python3-urllib3`) to install.
  - **web_Service**: The name of the web service to start and enable.
  - **content_File**: The content to be copied to the HTML file.
  - **dest_File**: The destination file where the content will be copied.
- **tasks**:
  - The first task installs the required packages.
  - The second task ensures the Apache service is started and enabled.
  - The third task copies the content to `/var/www/html/index.html`.
- The second play tests the web service from the control node using the **uri** module and checks for a status code of 200.

Replace `[username]` with your actual username throughout the playbook.

---

### Step 3: Run the Playbook

#### 3.1 Syntax Check

Before running the playbook, it’s a good practice to check the syntax:

```bash
ansible-playbook --syntax-check quiz-2_variables.yml
```

#### 3.2 Run the Playbook

Execute the playbook to install the necessary packages, start the services, deploy the web content, and verify the web server:

```bash
ansible-playbook quiz-2_variables.yml
```

---

### Step 4: Verify the Results

#### 4.1 Check the Installed Packages and Running Services

You can manually verify the installed packages and services by SSHing into `pod-[username]-managed2`:

```bash
ssh pod-[username]-managed2
```

Run the following commands to check:
- **Installed packages**:

   ```bash
   dpkg -l | grep apache2
   dpkg -l | grep python3-urllib3
   ```

- **Running services**:

   ```bash
   sudo systemctl status apache2
   ```

#### 4.2 Verify the Web Page

To verify that the web page was deployed correctly, use the `curl` command to access the page:

```bash
curl pod-[username]-managed2/index.html
```

You should see the following output:

```
adinusa lab quiz variables - [username]
```

---

## Conclusion

In this quiz, you learned how to:
1. Define and use variables in an Ansible playbook to configure a web server.
2. Install required packages (`apache2` and `python3-urllib3`) using a variable.
3. Ensure that the Apache service is started and enabled.
4. Deploy a web page using the **copy** module.
5. Test the web service from the control node using the **uri** module.

By using variables in your playbooks, you can make your automation more flexible and reusable.

### Example Output of `curl pod-[username]-managed2/index.html`:

```
adinusa lab quiz variables - [username]
```

Congratulations on completing **Lab 4.8: Quiz 2 - Using Variables in Playbooks**! You now have the skills to effectively use variables in Ansible playbooks to manage servers.

Happy Automating!
