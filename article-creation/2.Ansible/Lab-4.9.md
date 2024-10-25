# Lab 4.9: Quiz 3 - Using Jinja2 Templates in Ansible Playbooks

In this quiz, you will use **Jinja2 templates** in an Ansible playbook to manage repositories and services on your managed hosts. You will learn how to dynamically create repository files and ensure specific versions of software packages are installed and services are running.

---

## Table of Contents
1. **Introduction**
2. **Step-by-Step Instructions**
    1. Create the Working Directory and Configuration Files
    2. Write the Playbook and Jinja2 Templates
    3. Run the Playbook
    4. Verify the Results
3. **Conclusion**

---

## Introduction

In this quiz, you will:
1. Create Jinja2 templates for **nginx** and **mariadb** repository files.
2. Use a playbook to deploy these repositories on two managed hosts (`pod-[username]-managed1` and `pod-[username]-managed2`).
3. Install specific versions of **nginx** and **mariadb**.
4. Ensure that the services are running and enabled.

You will perform these tasks on the **Ansible controller node**, `pod-[username]-controller`, and manage the hosts `pod-[username]-managed1` and `pod-[username]-managed2`. Replace `[username]` with your actual username throughout the lab.

---

## Step-by-Step Instructions

### Step 1: Create the Working Directory and Configuration Files

#### 1.1 Create the Working Directory

First, create a directory for the quiz and navigate into it:

```bash
cd ~
mkdir quiz-3
cd quiz-3
```

This directory will contain the Ansible configuration, inventory, playbook, and Jinja2 templates.

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

Now, create the inventory file to define the target hosts (`pod-[username]-managed1` and `pod-[username]-managed2`):

```bash
vim inventory
```

Add the following content:

```ini
[webservers]
pod-[username]-managed1
pod-[username]-managed2
```

This defines a group called **webservers** with both managed hosts.

---

### Step 2: Write the Playbook and Jinja2 Templates

#### 2.1 Create the Playbook

Create a new playbook file to define the tasks and Jinja2 templates:

```bash
vim quiz-3_j2template.yml
```

Add the following content to the playbook:

```yaml
---
- name: Configure webservers
  hosts: webservers
  become: true

  tasks:
    - name: Add nginx repository using Jinja2 template
      template:
        src: nginx.list.j2
        dest: /etc/apt/sources.list.d/nginx.list

    - name: Add mariadb repository using Jinja2 template
      template:
        src: mariadb.list.j2
        dest: /etc/apt/sources.list.d/mariadb.list

    - name: Update the repository
      apt:
        update_cache: yes

    - name: Install specific versions of nginx and mariadb
      apt:
        name:
          - nginx=1.23.1-1~jammy
          - mariadb-server-10.9
          - mariadb-client-10.9
        state: present

    - name: Ensure nginx service is running and enabled
      service:
        name: nginx
        state: started
        enabled: yes

    - name: Ensure mariadb service is running and enabled
      service:
        name: mariadb
        state: started
        enabled: yes
```

Explanation:
- **template**: Uses Jinja2 templates to create repository files for nginx and mariadb.
- **apt**: Updates the package list and installs the specified versions of nginx and mariadb.
- **service**: Ensures the `nginx` and `mariadb` services are started and enabled.

#### 2.2 Create the Nginx Jinja2 Template

Create the Jinja2 template file for the nginx repository:

```bash
vim nginx.list.j2
```

Add the following content:

```
deb http://nginx.org/packages/ubuntu/ jammy nginx
deb-src http://nginx.org/packages/ubuntu/ jammy nginx
```

#### 2.3 Create the Mariadb Jinja2 Template

Create the Jinja2 template file for the mariadb repository:

```bash
vim mariadb.list.j2
```

Add the following content:

```
deb [arch=amd64,arm64,ppc64el] https://downloads.mariadb.com/MariaDB/mariadb-10.9/repo/ubuntu jammy main
deb-src https://downloads.mariadb.com/MariaDB/mariadb-10.9/repo/ubuntu jammy main
```

---

### Step 3: Run the Playbook

#### 3.1 Syntax Check

Before running the playbook, check the syntax to ensure there are no errors:

```bash
ansible-playbook --syntax-check quiz-3_j2template.yml
```

#### 3.2 Run the Playbook

Execute the playbook to configure the repositories, install the required packages, and start the services:

```bash
ansible-playbook quiz-3_j2template.yml
```

---

### Step 4: Verify the Results

#### 4.1 Check the Installed Packages

You can manually verify the installed packages by SSHing into `pod-[username]-managed1` or `pod-[username]-managed2`:

```bash
ssh pod-[username]-managed1
```

Run the following command to check the installed versions of **nginx** and **mariadb**:

```bash
dpkg -l | grep nginx
dpkg -l | grep mariadb
```

You should see **nginx 1.23.1-1~jammy** and **mariadb-server-10.9** installed.

#### 4.2 Check the Repository Files

Verify that the repository files were created in the correct location:

```bash
cat /etc/apt/sources.list.d/nginx.list
cat /etc/apt/sources.list.d/mariadb.list
```

You should see the content from the Jinja2 templates in these files.

#### 4.3 Check the Running Services

Ensure that the nginx and mariadb services are running and enabled:

```bash
sudo systemctl status nginx
sudo systemctl status mariadb
```

Both services should be running and enabled to start at boot.

---

## Conclusion

In this quiz, you learned how to:
1. Use **Jinja2 templates** to dynamically create repository files on managed hosts.
2. Install specific versions of **nginx** and **mariadb** using Ansible.
3. Ensure that services are running and enabled.
4. Deploy and manage software using Jinja2 templates in Ansible playbooks.

By using templates, you can dynamically generate configuration files and ensure consistency across your managed hosts.

### Example Output for Checking Installed Packages:

```bash
nginx 1.23.1-1~jammy
mariadb-server 10.9
mariadb-client 10.9
```

Congratulations on completing **Lab 4.9: Quiz 3 - Using Jinja2 Templates**! You now have the skills to use Jinja2 templates in Ansible playbooks to configure and manage repositories and services on multiple hosts.

Happy Automating!
