# Lab 4.6: Using Jinja2 Templates with Ansible

In this lab, we will learn how to use **Jinja2 templates** in Ansible. Jinja2 is a powerful templating engine that allows you to dynamically generate files, such as configuration files or web pages, by embedding variables. By the end of this lab, you will have written a playbook that uses a Jinja2 template to create an HTML page and deploy it to a web server.

---

## Table of Contents
1. **Introduction**
2. **Step-by-Step Instructions**
    1. Create Directory and Inventory
    2. Write the Playbook
    3. Create the Jinja2 Template
    4. Run the Playbook
    5. Verify the Web Page
3. **Verification**
4. **Conclusion**

---

## Introduction

In this lab, you will:
1. Install Apache on a web server.
2. Create a Jinja2 template that generates an HTML file.
3. Deploy the HTML file to the web server using Ansible.

You will perform these tasks on the **Ansible controller node**, `pod-[username]-controller`, and manage the host `pod-[username]-managed1`. Replace `[username]` with your actual username throughout the lab.

---

## Step-by-Step Instructions

### Step 1: Create Directory and Inventory

#### 1.1 Create a Working Directory

Start by creating a directory for your project and navigating into it:

```bash
cd ~
mkdir jinja2-template
cd ~/jinja2-template
```

This directory will contain your Ansible inventory, playbook, and Jinja2 template files.

#### 1.2 Create the Inventory File

Next, create an inventory file to define the hosts that Ansible will manage:

```bash
vim inventory
```

Add the following content:

```ini
[webservers]
pod-[username]-managed1
```

Explanation:
- **[webservers]**: A group containing `pod-[username]-managed1`, which represents the web server where Apache will be installed and the HTML file will be deployed.

---

### Step 2: Write the Playbook

A playbook is a YAML file that defines a series of tasks for Ansible to execute. In this case, the playbook will install Apache, restart the service, and deploy an HTML file using a Jinja2 template.

#### 2.1 Create the Playbook

Open the playbook file for editing:

```bash
vim site.yml
```

Add the following content to the playbook:

```yaml
---
- name: install and start apache2
  hosts: webservers
  become: true

  tasks:
    - name: ensure apache2 package is present
      apt:
        name: apache2
        state: present
        update_cache: yes
        force_apt_get: yes

    - name: restart apache2 service
      service:
        name: apache2
        state: restarted
        enabled: yes

    - name: copy index.html
      template:
        src: <username>.html.j2
        dest: /var/www/html/<username>.html
```

Explanation:
- **name**: Provides a description of each task.
- **hosts**: Specifies the group of hosts (`webservers`) where the tasks will run.
- **become: true**: Runs the tasks with elevated privileges (like `sudo`).
- **apt**: Installs the `apache2` package.
- **service**: Ensures the Apache service is restarted and enabled to start on boot.
- **template**: Uses the Jinja2 template to generate the HTML file and deploy it to `/var/www/html/<username>.html` on the web server.

Replace `<username>` with your actual username throughout the playbook.

---

### Step 3: Create the Jinja2 Template

Jinja2 templates are files with embedded variables or logic that are dynamically rendered by Ansible.

#### 3.1 Create the Jinja2 Template File

Create the Jinja2 template file for the HTML page:

```bash
vim <username>.html.j2
```

Add the following content:

```html
Hello World!
This is <username> site.
```

Explanation:
- The `<username>` variable in the template will be dynamically replaced by the actual username when the playbook is executed.

Replace `<username>` with your actual username in the filename.

---

### Step 4: Run the Playbook

Once the playbook and template are ready, you can run the playbook to install Apache and deploy the HTML file.

#### 4.1 Run the Playbook

Execute the following command to run the playbook:

```bash
ansible-playbook -i inventory site.yml
```

Ansible will install Apache on `pod-[username]-managed1`, restart the service, and deploy the HTML file generated from the Jinja2 template.

---

### Step 5: Verify the Web Page

To confirm that the playbook executed successfully, we will check the web server.

#### 5.1 Verify the Web Page with `curl`

Use the `curl` command to access the web page deployed on `pod-[username]-managed1`:

```bash
curl pod-[username]-managed1/<username>.html
```

You should see the following output:

```
Hello World!
This is <username> site.
```

This confirms that the HTML file was successfully generated and deployed using the Jinja2 template.

---

## Verification

To ensure that the lab was completed successfully, verify the following:
1. **Apache Installed**: Apache should be installed on `pod-[username]-managed1`.
2. **Service Started**: The Apache service should be running and enabled.
3. **HTML File Deployed**: The HTML file should be deployed to `/var/www/html/<username>.html`.
4. **Web Page Accessible**: The web page should be accessible via a browser or the `curl` command, and it should display the message from the Jinja2 template.

---

## Conclusion

In this lab, you learned how to:
1. Use **Jinja2 templates** to dynamically generate files in Ansible.
2. Install and configure an Apache web server using a playbook.
3. Deploy an HTML file to the web server using the **template** module in Ansible.

Jinja2 templates allow you to generate dynamic content, making your playbooks more flexible and adaptable to different environments.

### Example Output of `curl pod-[username]-managed1/<username>.html`:

```
Hello World!
This is <username> site.
```

Congratulations on completing **Lab 4.6: Using Jinja2 Templates with Ansible**! You now have the skills to use templates to dynamically generate configuration files or content for your servers.

Happy Automating!
