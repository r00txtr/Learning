# Lab 4.7: Quiz 1 - Writing and Running an Ansible Playbook

In this quiz, you will configure and perform administrative tasks on a managed host using an Ansible playbook. This task involves installing software packages, ensuring services are running, deploying a simple web page, and testing the web service from the control node.

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
1. Create a new working directory and set up the Ansible configuration.
2. Write a playbook that installs the required software packages on a managed host (`pod-[username]-managed2`).
3. Ensure the services are running and deploy a simple web page.
4. Test the web service from the control node (`pod-[username]-controller`).

You will perform these tasks on the **Ansible controller node**, `pod-[username]-controller`, and manage the host `pod-[username]-managed2`. Replace `[username]` with your actual username throughout the lab.

---

## Step-by-Step Instructions

### Step 1: Create the Working Directory and Configuration Files

#### 1.1 Create the Working Directory

First, create a directory for the quiz and navigate into it:

```bash
cd ~
mkdir quiz-1
cd quiz-1
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

Create a new playbook file to define the tasks:

```bash
vim quiz-1_playbook.yml
```

Add the following content to the playbook:

```yaml
---
- name: Quiz Playbook
  hosts: webservers
  become: true
  tasks:
    - name: Install apache2, mariadb-server, php, and php-mysql
      apt:
        name:
          - apache2
          - mariadb-server
          - php
          - php-mysql
        state: latest
        update_cache: yes

    - name: Ensure apache2 service is running and enabled
      service:
        name: apache2
        state: started
        enabled: yes

    - name: Ensure mariadb service is running and enabled
      service:
        name: mariadb
        state: started
        enabled: yes

    - name: Generate web content
      copy:
        content: "Adinusa quiz Playbook - [username]"
        dest: /var/www/html/index.php

- name: Test webserver from control node
  hosts: localhost
  tasks:
    - name: Check web server response
      uri:
        url: http://pod-[username]-managed2/index.php
        status_code: 200
        return_content: yes
      register: web_result

    - name: Display web server content
      debug:
        var: web_result.content
```

Explanation:
- **Quiz Playbook**: The first playbook runs tasks on the `webservers` group (which contains `pod-[username]-managed2`).
  - It installs the required packages (`apache2`, `mariadb-server`, `php`, and `php-mysql`).
  - It ensures that the `apache2` and `mariadb` services are started and enabled.
  - It copies the text "Adinusa quiz Playbook - [username]" to the `/var/www/html/index.php` file.
- **Test webserver from control node**: The second playbook runs on `localhost` to test the web service on `pod-[username]-managed2` using the `uri` module. It checks for an HTTP status code of 200 and displays the content of the web page.

Replace `[username]` with your actual username throughout the playbook.

---

### Step 3: Run the Playbook

#### 3.1 Syntax Check

Before running the playbook, it’s a good practice to check the syntax:

```bash
ansible-playbook --syntax-check quiz-1_playbook.yml
```

#### 3.2 Run the Playbook

Execute the playbook to install the necessary packages, start the services, deploy the web content, and verify the web server:

```bash
ansible-playbook quiz-1_playbook.yml
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
   dpkg -l | grep mariadb-server
   dpkg -l | grep php
   dpkg -l | grep php-mysql
   ```

- **Running services**:

   ```bash
   sudo systemctl status apache2
   sudo systemctl status mariadb
   ```

#### 4.2 Verify the Web Page

To verify that the web page was deployed correctly, use the `curl` command to access the page:

```bash
curl pod-[username]-managed2/index.php
```

You should see the following output:

```
Adinusa quiz Playbook - [username]
```

---

## Conclusion

In this quiz, you learned how to:
1. Write an Ansible playbook that installs required packages (`apache2`, `mariadb-server`, `php`, and `php-mysql`) on a managed host.
2. Ensure that the services are running and enabled.
3. Deploy a web page using the **copy** module.
4. Test the web service from the control node using the **uri** module.

By completing this quiz, you have gained hands-on experience in writing and executing playbooks that automate administrative tasks across managed hosts.

### Example Output of `curl pod-[username]-managed2/index.php`:

```
Adinusa quiz Playbook - [username]
```

Congratulations on completing **Lab 4.7: Quiz 1 - Writing and Running a Playbook**! You now have the skills to create complex Ansible playbooks to manage infrastructure efficiently.

Happy Automating!
