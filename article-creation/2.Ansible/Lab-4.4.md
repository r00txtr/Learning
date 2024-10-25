# Lab 4.4: Writing and Running Ansible Playbooks

In this lab, we will explore how to write and run **Ansible playbooks** to automate common system administration tasks. A playbook is a YAML file that contains a series of **tasks** to be executed on remote hosts. By the end of this lab, you will have written a playbook that installs and configures an Apache web server, deploys an HTML page, and starts the service on a managed host.

---

## Table of Contents
1. **Introduction**
2. **Step-by-Step Instructions**
    1. Create Directory and Configuration Files
    2. Create the Inventory File
    3. Write the Playbook
    4. Run the Playbook
    5. Verify the Web Server
3. **Verification**
4. **Conclusion**

---

## Introduction

Ansible **playbooks** are files that define a series of tasks to automate the configuration of servers. Playbooks allow you to describe your infrastructure in code, making it easier to maintain and scale. In this lab, you will:
1. Create an Ansible playbook to install and configure **Apache** on a managed host.
2. Deploy a custom **index.html** file to the web server.
3. Ensure that the Apache service is running and enabled.

We will use `pod-[username]-controller` as the Ansible controller, and `pod-[username]-managed1` as the managed host. Replace `[username]` with your actual username throughout the lab.

---

## Step-by-Step Instructions

### Step 1: Create Directory and Configuration Files

#### 1.1 Create a Working Directory

First, create a directory for your playbook and navigate into it:

```bash
cd ~/
mkdir -p playbook-basic/files
cd playbook-basic
```

This directory will store your Ansible configuration, inventory, and playbook files.

#### 1.2 Create the Ansible Configuration File

Create the `ansible.cfg` file to define default settings for your playbook:

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
- **inventory = ./inventory**: Specifies that the inventory file is in the current directory.
- **remote_user = student**: Sets the default user (`student`) for running the playbook.

---

### Step 2: Create the Inventory File

The inventory file defines the hosts that Ansible will manage.

#### 2.1 Create the Inventory File

Open the inventory file for editing:

```bash
vim inventory
```

Add the following content to define your managed hosts:

```ini
[web]
pod-[username]-managed1
```

Explanation:
- **[web]**: A group containing `pod-[username]-managed1`, which represents the web server where Apache will be installed.

---

### Step 3: Write the Playbook

A playbook is a YAML file that contains a series of tasks to be executed on the remote host.

#### 3.1 Create the HTML File

First, create a simple HTML file that will be deployed to the web server:

```bash
echo "This is a test page." > files/index.html
```

This creates a file called **index.html** with the content **This is a test page.**

#### 3.2 Create the Playbook File

Next, create the Ansible playbook that will install Apache, copy the HTML file, and start the Apache service:

```bash
vim site.yml
```

Add the following content to the playbook:

```yaml
---
- name: Install and start Apache 2
  hosts: web
  become: true
  tasks:
    - name: apache2 package is present
      apt:
        name: apache2
        state: present

    - name: correct index.html is present
      copy:
        src: ./files/index.html
        dest: /var/www/html/index.html

    - name: Apache 2 is started
      service:
        name: apache2
        state: started
        enabled: true
```

Explanation:
- **name**: Provides a description of the play.
- **hosts**: Specifies the group of hosts (in this case, `web`) where the tasks will run.
- **become: true**: Runs the tasks with elevated privileges (like `sudo`).
- **tasks**:
  - **Install Apache**: Installs the `apache2` package using the `apt` module.
  - **Copy index.html**: Copies the custom HTML file to `/var/www/html/index.html`.
  - **Start Apache**: Ensures the Apache service is started and enabled to run on boot.

---

### Step 4: Run the Playbook

Now that the playbook is written, you can run it using the **ansible-playbook** command.

#### 4.1 Run the Playbook

Execute the following command to run the playbook and apply the tasks to the managed host:

```bash
ansible-playbook site.yml
```

Ansible will install Apache, copy the `index.html` file, and start the web server on `pod-[username]-managed1`.

---

### Step 5: Verify the Web Server

To confirm that the playbook executed successfully, we will check the web server.

#### 5.1 Verify the Web Server with `curl`

Use the `curl` command to check if the web server is serving the custom HTML page:

```bash
curl pod-[username]-managed1
```

You should see the following output:

```
This is a test page.
```

This confirms that Apache was installed and the HTML file was successfully deployed.

---

## Verification

To ensure that the lab was completed successfully, verify the following:
1. **Apache Installed**: Apache should be installed on `pod-[username]-managed1`.
2. **HTML Page**: The custom **index.html** file should be deployed and accessible via the web server.
3. **Apache Service Running**: The Apache service should be running and enabled to start on boot.

---

## Conclusion

In this lab, you learned how to:
1. Write an Ansible playbook to install and configure an Apache web server.
2. Deploy a custom HTML page to the web server using the **copy** module.
3. Start and enable the Apache service using the **service** module.

By writing playbooks, you can automate complex configuration tasks and ensure that your infrastructure is consistently managed.

### Example Output of `curl pod-[username]-managed1`:

```
This is a test page.
```

Congratulations on completing **Lab 4.4: Writing and Running Playbooks**! You now have the skills to write playbooks and automate server configurations with Ansible.

Happy Automating!
