# Lab Challenge: Deploying Laravel Application Using Ansible

In this challenge, you will use Ansible to automate the deployment of a Laravel application. You will deploy the **Laravel 8 Ecommerce** source code from GitHub on a managed server using Apache2 as the web server and MySQL as the database. All steps should be automated using Ansible playbooks.

---

## Table of Contents
1. **Introduction**
2. **Step-by-Step Instructions**
    1. Create Directory and Configuration Files
    2. Install Required Packages
    3. Install Composer
    4. Clone Laravel Source Code
    5. Configure Laravel and MySQL Database
6. **Run the Playbook**
7. **Verify the Results**
8. **Conclusion**

---

## Introduction

You are tasked with deploying the Laravel 8 Ecommerce application from the GitHub repository [https://github.com/kunal254/laravel-8-ecommerce](https://github.com/kunal254/laravel-8-ecommerce) using Ansible. The deployment should meet the following criteria:

- Laravel is deployed on `pod-[username]-managed1`.
- The application runs on an **Apache2** web server.
- The database is configured with **MySQL**.
- All processes are handled via a single Ansible playbook.
  
---

## Step-by-Step Instructions

### Step 1: Create Directory and Configuration Files

#### 1.1 Create a Working Directory

Begin by creating a working directory for this challenge:

```bash
cd ~
mkdir challenge-1
cd challenge-1
```

#### 1.2 Create `ansible.cfg` and Inventory File

Create the `ansible.cfg` file to define the configuration and inventory for your playbook:

```bash
vim ansible.cfg
```

Add the following content:

```ini
[defaults]
inventory=inventory
remote_user=student
become=True
```

Create the inventory file that defines your managed host:

```bash
vim inventory
```

Add the following content:

```ini
[managed]
pod-[username]-managed1
```

---

### Step 2: Install Required Packages

The Laravel application requires PHP 8, npm, git, Apache2, and MySQL. You will automate the installation of these packages using Ansible.

#### 2.1 Define the Playbook

Create a playbook file named `laravel.yaml`:

```bash
vim laravel.yaml
```

Add the following content for installing the required packages:

```yaml
---
- name: Deploy Laravel on Managed Server
  hosts: managed
  become: true

  tasks:
    - name: Install required packages
      apt:
        name: 
          - php8.0
          - php8.0-mbstring
          - php8.0-xml
          - php8.0-mysql
          - npm
          - git
          - apache2
          - mysql-server
        state: present
        update_cache: yes
```

This task will ensure that all the necessary packages are installed on the server.

---

### Step 3: Install Composer

Laravel requires **Composer** for managing dependencies. You will use Ansible's **composer** module to automate this.

#### 3.1 Add Composer Installation Task

Add the following task to your `laravel.yaml` playbook:

```yaml
    - name: Install Composer
      get_url:
        url: https://getcomposer.org/installer
        dest: /tmp/composer-installer.php

    - name: Run Composer Installer
      command: php /tmp/composer-installer.php --install-dir=/usr/local/bin --filename=composer
```

This will download and install Composer on the managed server.

---

### Step 4: Clone Laravel Source Code

Now, you'll automate the process of cloning the Laravel 8 Ecommerce application from GitHub.

#### 4.1 Add Git Clone Task

Add the following task to your playbook:

```yaml
    - name: Git Clone Laravel
      git:
        repo: https://github.com/kunal254/laravel-8-ecommerce.git
        dest: /var/www/laravel-app
        force: true
```

This will clone the Laravel application into the `/var/www/laravel-app` directory.

---

### Step 5: Configure Laravel and MySQL Database

After cloning the Laravel source code, you need to configure the application, set up the `.env` file, and set the correct permissions.

#### 5.1 Set Up Laravel Dependencies

Add the following task to install Laravel dependencies using Composer:

```yaml
    - name: Install Laravel Dependencies
      composer:
        command: install
        working_dir: /var/www/laravel-app
```

#### 5.2 Set Up the Environment File

Copy the `.env.example` file to `.env` and generate the application key:

```yaml
    - name: Copy .env file
      copy:
        src: /var/www/laravel-app/.env.example
        dest: /var/www/laravel-app/.env

    - name: Generate application key
      command: php artisan key:generate
      args:
        chdir: /var/www/laravel-app
```

#### 5.3 Configure the MySQL Database

Set up the MySQL database and grant the necessary permissions:

```yaml
    - name: Create MySQL Database
      mysql_db:
        name: laravel_db
        state: present

    - name: Create MySQL User
      mysql_user:
        name: laravel_user
        password: password123
        priv: 'laravel_db.*:ALL'
        state: present

    - name: Update .env file with MySQL details
      lineinfile:
        path: /var/www/laravel-app/.env
        regexp: '^DB_'
        line: |
          DB_CONNECTION=mysql
          DB_HOST=127.0.0.1
          DB_PORT=3306
          DB_DATABASE=laravel_db
          DB_USERNAME=laravel_user
          DB_PASSWORD=password123
```

#### 5.4 Set Permissions

Ensure the correct permissions for the Laravel directory:

```yaml
    - name: Set ownership and permissions
      file:
        path: /var/www/laravel-app
        state: directory
        owner: www-data
        group: www-data
        recurse: yes
```

---

### Step 6: Run the Playbook

Once you have completed the playbook, run it to deploy the Laravel application.

#### 6.1 Run the Playbook

```bash
ansible-playbook laravel.yaml
```

This will install all the necessary packages, configure the Laravel application, and set up the MySQL database.

---

### Step 7: Verify the Results

#### 7.1 Access the Application

After running the playbook, you can verify that the Laravel application is up and running by accessing the URL:

```bash
curl http://<your-server-ip>/laravel-app/public
```

You should see the Laravel default page.

#### 7.2 Login to the Admin Panel

Use the credentials provided in the GitHub repository:

- **Username**: admin@gmail.com
- **Password**: admin123

Access the admin login page at:

```bash
http://<your-server-ip>/laravel-app/public/admin/login
```

---

## Conclusion

In this lab challenge, you learned how to:
1. Deploy a Laravel application using Ansible playbooks.
2. Install and configure all necessary components, including Apache2, PHP, MySQL, and Composer.
3. Use Ansible modules such as `git`, `composer`, and `mysql_user` to automate the deployment process.

By automating the Laravel deployment, you ensure consistency and save time by avoiding manual steps.

### Example Output:

```bash
PLAY RECAP *********************************************************************
pod-[username]-managed1 : ok=15   changed=10   unreachable=0   failed=0
```

Congratulations on completing **Lab Challenge: Exploring Playbook Ansible**! You now have the skills to automate complex application deployments with Ansible.

Happy Automating!
