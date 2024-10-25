# Lab 5.1: Managing Roles in Ansible

In this lab, we will learn how to create and manage **roles** in Ansible. Roles are a powerful way to organize your playbooks, allowing you to reuse and maintain your configuration more easily. By the end of this lab, you will have created a role to configure virtual hosts (vhosts) on an Apache web server.

---

## Table of Contents
1. **Introduction**
2. **Step-by-Step Instructions**
    1. Create Directory and Configuration Files
    2. Create and Structure Roles
    3. Define Tasks and Handlers
    4. Create Templates for Virtual Hosts
    5. Write the Playbook to Use the Role
    6. Run the Playbook
    7. Verify the Configuration
3. **Conclusion**

---

## Introduction

In this lab, you will:
1. Create a role that installs and configures **Apache** with two virtual hosts.
2. Use **Jinja2 templates** to define the virtual host configuration files.
3. Ensure the Apache service is running and the virtual hosts are enabled.

You will perform these tasks on the **Ansible controller node**, `pod-[username]-controller`, and manage two hosts: `pod-[username]-managed1` and `pod-[username]-managed2`. Replace `[username]` with your actual username throughout the lab.

---

## Step-by-Step Instructions

### Step 1: Create Directory and Configuration Files

#### 1.1 Create a Working Directory

Start by creating a directory for your project and navigating into it:

```bash
cd ~/
mkdir role-create
cd role-create
```

#### 1.2 Create the `ansible.cfg` File

Next, create the `ansible.cfg` file to define the location of the inventory file:

```bash
vim ansible.cfg
```

Add the following content:

```ini
[defaults]
inventory=./inventory
remote_user=student
```

#### 1.3 Create the Inventory File

Create the inventory file to define the target hosts (`pod-[username]-managed1` and `pod-[username]-managed2`):

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

### Step 2: Create and Structure Roles

#### 2.1 Create the Role Structure

Create the necessary directories for the role:

```bash
mkdir roles
cd roles
ansible-galaxy init myvhost
```

Remove the unnecessary directories:

```bash
rm -rvf myvhost/{defaults,vars,tests}
cd ..
```

#### 2.2 Create HTML Content Directories

Create directories for the HTML content of each virtual host:

```bash
mkdir -p roles/myvhost/files/html-1
mkdir -p roles/myvhost/files/html-2
```

#### 2.3 Add Sample HTML Content

Add sample content for the virtual hosts:

```bash
echo 'simple index vhost1 : pod-username' > roles/myvhost/files/html-1/index.html
echo 'simple index vhost2 : pod-username' > roles/myvhost/files/html-2/index.html
```

---

### Step 3: Define Tasks and Handlers

#### 3.1 Create the Tasks

Define the tasks for the role in the `main.yml` file:

```bash
vim roles/myvhost/tasks/main.yml
```

Add the following content:

```yaml
- name: Ensure apache2 is installed
  apt:
    name: apache2
    state: latest

- name: Ensure apache2 is started and enabled
  service:
    name: apache2
    state: started
    enabled: true

- name: vhost-1 file is installed
  template:
    src: vhost-1.conf.j2
    dest: /etc/apache2/sites-available/vhost-1.conf
    owner: root
    group: root
    mode: 0644

- name: Enable vhost-1
  command: a2ensite vhost-1.conf
  notify:
    - restart apache2

- name: vhost-2 file is installed
  template:
    src: vhost-2.conf.j2
    dest: /etc/apache2/sites-available/vhost-2.conf
    owner: root
    group: root
    mode: 0644

- name: Enable vhost-2
  command: a2ensite vhost-2.conf
  notify:
    - restart apache2

- name: HTML content is installed
  copy:
    src: html-1/
    dest: "/var/www/vhosts/{{ ansible_user }}-1"

- name: HTML content is installed
  copy:
    src: html-2/
    dest: "/var/www/vhosts/{{ ansible_user }}-2"
```

#### 3.2 Create the Handlers

Define the handler to restart Apache:

```bash
vim roles/myvhost/handlers/main.yml
```

Add the following content:

```yaml
- name: restart apache2
  service:
    name: apache2
    state: restarted
```

---

### Step 4: Create Templates for Virtual Hosts

#### 4.1 Create the Template for Virtual Host 1

Create the Jinja2 template for the first virtual host:

```bash
vim roles/myvhost/templates/vhost-1.conf.j2
```

Add the following content:

```html
<VirtualHost *:80>
    ServerAdmin webmaster@vhost-1.{{ ansible_user }}
    ServerName vhost-1.{{ ansible_user }}
    ErrorLog /var/log/apache2/vhost.{{ ansible_user }}-1-error.log
    CustomLog /var/log/apache2/vhost.{{ ansible_user }}-1-common.log common
    DocumentRoot /var/www/vhosts/{{ ansible_user }}-1/

    <Directory /var/www/vhosts/{{ ansible_user }}-1/>
        Options +Indexes +FollowSymlinks +Includes
        Order allow,deny
        Allow from all
    </Directory>
</VirtualHost>
```

#### 4.2 Create the Template for Virtual Host 2

Create the Jinja2 template for the second virtual host:

```bash
vim roles/myvhost/templates/vhost-2.conf.j2
```

Add the following content:

```html
<VirtualHost *:80>
    ServerAdmin webmaster@vhost-2.{{ ansible_user }}
    ServerName vhost-2.{{ ansible_user }}
    ErrorLog /var/log/apache2/vhost.{{ ansible_user }}-2-error.log
    CustomLog /var/log/apache2/vhost.{{ ansible_user }}-2-common.log common
    DocumentRoot /var/www/vhosts/{{ ansible_user }}-2/

    <Directory /var/www/vhosts/{{ ansible_user }}-2/>
        Options +Indexes +FollowSymlinks +Includes
        Order allow,deny
        Allow from all
    </Directory>
</VirtualHost>
```

---

### Step 5: Write the Playbook to Use the Role

Create the playbook that will use the role to configure the web servers:

```bash
vim use-vhost-role.yml
```

Add the following content:

```yaml
- name: Use myvhost role playbook
  hosts: webservers
  become: true

  pre_tasks:
    - name: pre_tasks message
      debug:
        msg: 'Ensure web server configuration.'

  roles:
    - myvhost

  post_tasks:
    - name: post_tasks message
      debug:
        msg: 'Web server is configured.'
```

---

### Step 6: Run the Playbook

#### 6.1 Syntax Check

Before running the playbook, perform a syntax check:

```bash
ansible-playbook use-vhost-role.yml --syntax-check
```

#### 6.2 Run the Playbook

Execute the playbook to configure the web servers and virtual hosts:

```bash
ansible-playbook use-vhost-role.yml
```

---

### Step 7: Verify the Configuration

#### 7.1 Test the Virtual Hosts

To verify that the virtual hosts are configured correctly, use the `curl` command to simulate requests:

```bash
curl -H "Host: vhost-1.student" http://pod-[username]-managed1
curl -H "Host: vhost-2.student" http://pod-[username]-managed1
curl -H "Host: vhost-1.student" http://pod-[username]-managed2
curl -H "Host: vhost-2.student" http://pod-[username]-managed2
```

You should see the content of the respective virtual hosts.

---

## Conclusion

In this lab, you learned how to:
1. Create and manage roles in Ansible to organize tasks and configurations.
2. Use **Jinja2 templates** to configure virtual hosts for an Apache web server.
3. Ensure services are running and enabled using the **service** module.
4. Test the configuration by making HTTP requests to the virtual hosts.

By using roles, you can organize your playbooks into reusable and maintainable components, making it easier to manage complex configurations.

### Example Output of `curl`:

```
simple index vhost1 : pod-username
simple index vhost2 : pod-username
```

Congratulations on completing **Lab 5.1: Managing Roles in Ansible**! You now have the skills to create and use roles to manage configurations across multiple servers.



Happy Automating!
