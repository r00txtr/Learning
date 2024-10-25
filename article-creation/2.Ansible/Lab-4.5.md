# Lab 4.5: Managing Variables in Ansible

In this lab, we will explore how to use **variables** in Ansible to make your playbooks more flexible and reusable. Variables allow you to parameterize values such as package names, service names, and content, so you can easily modify them without changing the entire playbook. By the end of this lab, you will have written a playbook that installs Apache, starts the service, and verifies that the web server is accessible using Ansible variables.

---

## Table of Contents
1. **Introduction**
2. **Step-by-Step Instructions**
    1. Create Directory and Configuration Files
    2. Write the Playbook with Variables
    3. Run the Playbook
    4. Verify the Web Server
3. **Verification**
4. **Conclusion**

---

## Introduction

In Ansible, **variables** allow you to dynamically replace values in your playbooks, making them more modular and easier to maintain. In this lab, you will:
1. Create a playbook that installs Apache on a managed server using variables.
2. Ensure the web server is running, and place content in the default HTML file.
3. Verify that the web server is accessible by checking the HTTP response.

You will perform these tasks on the **Ansible controller node**, `pod-[username]-controller`, and manage the host `pod-[username]-managed2`. Replace `[username]` with your actual username throughout the lab.

---

## Step-by-Step Instructions

### Step 1: Create Directory and Configuration Files

#### 1.1 Create a Working Directory

Start by creating a directory for your project and navigating into it:

```bash
cd ~
mkdir data-variables/
cd data-variables/
```

This directory will contain your Ansible configuration, inventory, and playbook files.

#### 1.2 Create the Ansible Configuration File

Next, create the `ansible.cfg` file, which defines default settings for Ansible:

```bash
vim ansible.cfg
```

Add the following content:

```ini
[defaults]
inventory = ./inventory
remote_user = student
host_key_checking = False
```

Explanation:
- **inventory = ./inventory**: Specifies that the inventory file is in the current directory.
- **remote_user = student**: Sets the default user (`student`) for running the playbook.
- **host_key_checking = False**: Disables SSH host key checking to simplify connecting to new hosts.

---

### Step 2: Write the Playbook with Variables

Variables make playbooks flexible by allowing you to define key values, such as package names and services, at the top of the playbook. This way, you can easily change the values when necessary without modifying the entire playbook.

#### 2.1 Create the Inventory File

First, define the hosts in your inventory file:

```bash
vim inventory
```

Add the following content:

```ini
[webserver]
pod-[username]-managed2
```

This defines a group called **webserver** with `pod-[username]-managed2` as the host.

#### 2.2 Write the Playbook

Now, create the playbook that installs Apache and ensures it is running. It will also verify that the web server is accessible:

```bash
vim playbook.yml
```

Add the following content:

```yaml
---
- name: Install and Ensure the Apache2 service started
  hosts: webserver
  become: true
  vars:
    web_pkg: apache2
    web_service: apache2
    python_pkg: python3-urllib3

  tasks:
    - name: Required packages are installed and up to date
      apt:
        update_cache: yes
        force_apt_get: yes
        name:
          - "{{web_pkg}}"
          - "{{python_pkg}}"
        state: latest

    - name: The {{web_service}} service is started and enabled
      service:
         name: "{{web_service}}"
         enabled: true
         state: started

    - name: Web content is in place
      copy:
        content: "Hello World! ansible is fun."
        dest: /var/www/html/index.html

- name: Verify the web server is accessible
  hosts: localhost
  tasks:
    - name: Testing web server
      uri:
        url: http://pod-[username]-managed2
        status_code: 200
        return_content: yes
      register: Result

    - name: Print Output web server
      debug:
        var: Result.content
```

Explanation:
- **vars**: Defines the variables used in the playbook, such as `web_pkg` for the Apache package and `web_service` for the service name.
- **tasks**:
  - Installs the required packages (`apache2` and `python3-urllib3`).
  - Starts and enables the Apache service.
  - Copies a custom **index.html** file to the web server's document root.
- The second part of the playbook verifies that the web server is accessible by making an HTTP request to `pod-[username]-managed2`.

---

### Step 3: Run the Playbook

#### 3.1 Syntax Check

Before running the playbook, it's a good idea to perform a syntax check to ensure there are no errors:

```bash
ansible-playbook --syntax-check playbook.yml
```

If the syntax is correct, you can proceed to run the playbook.

#### 3.2 Run the Playbook

Now, run the playbook to install Apache, deploy the HTML file, and verify the web server:

```bash
ansible-playbook playbook.yml
```

Ansible will execute the tasks defined in the playbook, applying them to the host `pod-[username]-managed2`.

---

### Step 4: Verify the Web Server

To confirm that the playbook executed successfully, we will check if the web server is serving the custom HTML page.

#### 4.1 Verify the Web Server with `curl`

Use the `curl` command to check the web server on `pod-[username]-managed2`:

```bash
curl pod-[username]-managed2
```

You should see the following output:

```
Hello World! ansible is fun.
```

This confirms that Apache is installed, the service is running, and the custom HTML content has been successfully deployed.

---

## Verification

To ensure that the lab was completed successfully, verify the following:
1. **Apache Installed**: The Apache web server should be installed on `pod-[username]-managed2`.
2. **Service Started**: The Apache service should be running and enabled.
3. **Web Content Deployed**: The custom **index.html** file should be in place, and the content should be served by the web server.
4. **Web Server Verified**: The playbook should confirm that the web server is accessible, and you should see the content when accessing the server.

---

## Conclusion

In this lab, you learned how to:
1. Write an Ansible playbook that uses variables to install and configure an Apache web server.
2. Ensure that the Apache service is started and enabled.
3. Deploy a custom HTML file to the web server's document root.
4. Verify that the web server is accessible using Ansible’s `uri` module.

By using variables in your playbooks, you make your Ansible automation more flexible and maintainable.

### Example Output of `curl pod-[username]-managed2`:

```
Hello World! ansible is fun.
```

Congratulations on completing **Lab 4.5: Managing Variables in Ansible**! You now have a better understanding of how to use variables in playbooks to automate server configurations efficiently.

Happy Automating!
