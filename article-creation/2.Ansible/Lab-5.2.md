# Lab 5.2: Quiz 4 - Managing Roles in Ansible

In this quiz, you will create and use Ansible roles to automate the configuration of web servers. You will create a role that installs Apache, configures virtual hosts, deploys web content, and ensures the web service is running. By the end of this quiz, you will have written a playbook that uses a role to perform these tasks on two managed hosts.

---

## Table of Contents
1. **Introduction**
2. **Step-by-Step Instructions**
    1. Create Directory and Configuration Files
    2. Create the Role and Directory Structure
    3. Define Tasks and Handlers
    4. Create Templates and Files
    5. Write the Playbook to Use the Role
    6. Run the Playbook
    7. Verify the Results
3. **Conclusion**

---

## Introduction

In this quiz, you will:
1. Create an Ansible role that installs **Apache** and configures a virtual host.
2. Deploy a custom HTML file to the web server.
3. Ensure the Apache service is running and the virtual host is enabled.

You will perform these tasks on the **Ansible controller node**, `pod-[username]-controller`, and manage two hosts: `pod-[username]-managed1` and `pod-[username]-managed2`. Replace `[username]` with your actual username throughout the lab.

---

## Step-by-Step Instructions

### Step 1: Create Directory and Configuration Files

#### 1.1 Create a Working Directory

Start by creating a directory for your project:

```bash
cd ~
mkdir quiz-4
cd quiz-4
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

Now, create the inventory file to define the target hosts (`pod-[username]-managed1` and `pod-[username]-managed2`):

```bash
vim inventory
```

Add the following content:

```ini
[managed]
pod-[username]-managed1
pod-[username]-managed2
```

This defines a group called **managed** with both managed hosts.

---

### Step 2: Create the Role and Directory Structure

#### 2.1 Create the Role Directory

Create the directory structure for the role:

```bash
mkdir -p roles/quiz-roles/{tasks,handlers,files/html-quiz,templates}
```

This creates the necessary subdirectories for the role, including:
- **tasks**: To store the playbook tasks.
- **handlers**: To store handlers that restart services.
- **files/html-quiz**: To store the HTML files.
- **templates**: To store Jinja2 templates for virtual host configuration.

---

### Step 3: Define Tasks and Handlers

#### 3.1 Define the Tasks

Create the **main.yml** file for the tasks:

```bash
vim roles/quiz-roles/tasks/main.yml
```

Add the following content:

```yaml
- name: Install apache2 package
  apt:
    name: apache2
    state: present
    update_cache: yes

- name: Ensure apache2 service is started and enabled
  service:
    name: apache2
    state: started
    enabled: yes

- name: Install quiz-roles.conf for virtual host
  template:
    src: quiz-roles.conf.j2
    dest: /etc/apache2/sites-available/quiz-roles.conf
    owner: root
    group: root
    mode: 0644

- name: Enable webserver configuration
  command: a2ensite quiz-roles.conf
  notify:
    - restart apache2

- name: Copy HTML content
  copy:
    src: html-quiz/
    dest: /var/www/quiz-roles/{{ ansible_hostname }}
```

Explanation:
- The first two tasks install Apache and ensure the service is running and enabled.
- The third task uses the **template** module to install the virtual host configuration file.
- The fourth task enables the site using the `a2ensite` command and triggers a handler to restart Apache.
- The fifth task copies the HTML files from the local directory to the web server.

#### 3.2 Define the Handlers

Create the **main.yml** file for the handler:

```bash
vim roles/quiz-roles/handlers/main.yml
```

Add the following content:

```yaml
- name: restart apache2
  service:
    name: apache2
    state: restarted
```

This handler restarts Apache whenever the virtual host configuration is changed.

---

### Step 4: Create Templates and Files

#### 4.1 Create the HTML File

Create the HTML file for the quiz:

```bash
vim roles/quiz-roles/files/html-quiz/index.html
```

Add the following content:

```html
adinusa lab quiz roles - [username]
```

This will be the content served by the virtual host.

#### 4.2 Create the Virtual Host Template

Create the Jinja2 template for the virtual host configuration:

```bash
vim roles/quiz-roles/templates/quiz-roles.conf.j2
```

Add the following content:

```html
<VirtualHost *:80>
    ServerAdmin webmaster@quiz-roles.{{ ansible_user }}
    ServerName quiz-roles.{{ ansible_user }}
    ErrorLog /var/log/apache2/quiz-roles-error.log
    CustomLog /var/log/apache2/quiz-roles-access.log combined
    DocumentRoot /var/www/quiz-roles/{{ ansible_hostname }}

    <Directory /var/www/quiz-roles/{{ ansible_hostname }}>
        Options +Indexes +FollowSymlinks
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>
```

Explanation:
- **ServerName**: Uses the Jinja2 variable `{{ ansible_user }}` to generate the server name.
- **DocumentRoot**: Points to the directory where the HTML file will be stored.

---

### Step 5: Write the Playbook to Use the Role

Create the playbook that uses the role:

```bash
vim quiz-4_roles.yml
```

Add the following content:

```yaml
---
- name: Configure web servers with quiz-roles
  hosts: managed
  become: true

  roles:
    - quiz-roles
```

---

### Step 6: Run the Playbook

#### 6.1 Syntax Check

Before running the playbook, check for syntax errors:

```bash
ansible-playbook quiz-4_roles.yml --syntax-check
```

#### 6.2 Run the Playbook

Run the playbook to configure the web servers:

```bash
ansible-playbook quiz-4_roles.yml
```

---

### Step 7: Verify the Results

#### 7.1 Check the Installed Packages and Running Services

You can manually verify the installed packages and services by SSHing into `pod-[username]-managed1` or `pod-[username]-managed2`:

```bash
ssh pod-[username]-managed1
```

Run the following commands to check the Apache installation and service status:

```bash
dpkg -l | grep apache2
sudo systemctl status apache2
```

#### 7.2 Verify the HTML File

Check that the HTML file exists:

```bash
cat /var/www/quiz-roles/pod-[username]-managed1/index.html
```

You should see the following content:

```
adinusa lab quiz roles - [username]
```

#### 7.3 Test the Web Server

Use the `curl` command to check that the web server is serving the correct content:

```bash
curl http://pod-[username]-managed1
curl http://pod-[username]-managed2
```

You should see the output:

```
adinusa lab quiz roles - [username]
```

---

## Conclusion

In this quiz, you learned how to:
1. Create and use an Ansible role to configure Apache with a custom virtual host.
2. Deploy a web page using the **copy** module.
3. Ensure the Apache service is running and the virtual host is enabled.
4. Test the web server to ensure the configuration is correct.

By using roles, you can organize your playbooks into reusable components, making them easier to maintain and scale.

### Example Output of `curl`:

```
adinusa lab quiz roles - [username]
```

Congratulations on completing **Lab 5.2: Quiz 4 - Using Roles in Ansible**! You now have the skills to create and use roles to manage configurations across multiple servers.

Happy Automating!
