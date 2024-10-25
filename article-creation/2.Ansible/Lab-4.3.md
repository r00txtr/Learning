# Lab 4.3: Managing Ansible Configuration Files

In this lab, we will learn how to manage **Ansible configuration files** to simplify the process of running Ansible commands. The Ansible configuration file (`ansible.cfg`) allows you to define various settings, such as inventory location, user privileges, and security settings, making your Ansible tasks more efficient and consistent.

By the end of this lab, you will:
1. Create a custom Ansible configuration file.
2. Set up an inventory file to define managed hosts.
3. Run ad-hoc commands to manage your servers and verify the changes.

---

## Table of Contents
1. **Introduction**
2. **Step-by-Step Instructions**
    1. Create the Directory and Configuration Files
    2. Create an Inventory File
    3. Run Ad-hoc Commands Using the Configuration
    4. Verify the Changes
3. **Verification**
4. **Conclusion**

---

## Introduction

Ansible's configuration file, `ansible.cfg`, is used to define default settings for Ansible. You can configure options such as inventory location, remote users, host key checking, and more. This lab will show you how to create an Ansible configuration file, define your inventory, and run ad-hoc commands on your managed nodes.

You will perform these tasks on the **Ansible controller node**, `pod-[username]-controller`, and manage two nodes: `pod-[username]-managed1` and `pod-[username]-managed2`. Replace `[username]` with your actual username throughout the lab.

---

## Step-by-Step Instructions

### Step 1: Create the Directory and Configuration Files

#### 1.1 Create a Working Directory

First, create a directory for your project and navigate into it:

```bash
cd ~/
mkdir -p deploy-review
cd deploy-review
```

This directory will contain your Ansible configuration and inventory files.

#### 1.2 Create the `ansible.cfg` Configuration File

Next, create the `ansible.cfg` file, which will hold your Ansible configuration settings:

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
- **inventory = ./inventory**: Specifies the location of the inventory file in the current directory.
- **remote_user = student**: Defines the default remote user as `student` for all connections.
- **host_key_checking = False**: Disables SSH host key checking, which simplifies connections to new hosts.

---

### Step 2: Create an Inventory File

Now, create the inventory file that will list your managed hosts.

#### 2.1 Create the Inventory File

Open the `inventory` file for editing:

```bash
vim inventory
```

Add the following content to define your hosts:

```ini
[servers]
pod-[username]-managed1
pod-[username]-managed2
```

Explanation:
- **[servers]**: A group that includes `pod-[username]-managed1` and `pod-[username]-managed2`, which are the hosts being managed by Ansible.

---

### Step 3: Run Ad-hoc Commands Using the Configuration

Now that the configuration and inventory files are set up, you can run Ansible ad-hoc commands to manage your servers.

#### 3.1 Check the User ID on the Servers

Run the following ad-hoc command to check the user ID on all the servers in the `servers` group:

```bash
ansible servers -m command -a 'id'
```

Explanation:
- **servers**: Refers to the group defined in the inventory.
- **-m command**: Uses the `command` module to run a command on the remote hosts.
- **-a 'id'**: Runs the `id` command to check the user ID on the servers.

#### 3.2 Update the `/etc/motd` File on the Servers

Use the **copy** module to update the **/etc/motd** file (message of the day) on the servers:

```bash
ansible servers -m copy -a "content='This server is managed by Ansible.\n' dest=/etc/motd" --become
```

Explanation:
- **-m copy**: Uses the `copy` module to copy text into a file on the remote hosts.
- **content='...'**: Specifies the content to be copied into the file.
- **dest=/etc/motd**: Specifies the destination file path on the remote hosts.
- **--become**: Runs the command with elevated privileges (like `sudo`), as modifying `/etc/motd` requires root access.

#### 3.3 Verify the Updated `/etc/motd` File

Run the following ad-hoc command to check the contents of the **/etc/motd** file on all servers:

```bash
ansible servers -m command -a 'cat /etc/motd'
```

This will display the contents of the **/etc/motd** file, showing the message **"This server is managed by Ansible."**

---

### Step 4: Verify the Changes

#### 4.1 SSH into the Managed Nodes

You can manually verify the changes by SSHing into the managed nodes and checking the **/etc/motd** file.

1. SSH into `pod-[username]-managed1` and run the following command:

   ```bash
   ssh pod-[username]-managed1 "whoami; cat /etc/motd"
   ```

   You should see the output:
   ```
   student
   This server is managed by Ansible.
   ```

2. SSH into `pod-[username]-managed2` and run the same command:

   ```bash
   ssh pod-[username]-managed2 "whoami; cat /etc/motd"
   ```

   The output should be the same, confirming that the `/etc/motd` file has been updated on both servers.

---

## Verification

To ensure that the lab was completed successfully, verify the following:
1. **Configuration**: The `ansible.cfg` file correctly points to the inventory file and disables host key checking.
2. **Inventory**: The inventory file contains the correct hosts, grouped under `[servers]`.
3. **Ad-hoc Commands**: The `/etc/motd` file on both `pod-[username]-managed1` and `pod-[username]-managed2` should display the message **"This server is managed by Ansible."**

---

## Conclusion

In this lab, you learned how to:
1. Create and manage an Ansible configuration file (`ansible.cfg`) to simplify running Ansible commands.
2. Set up an inventory file to define managed hosts.
3. Use ad-hoc commands to manage servers and update files.
4. Verify the changes on the managed servers.

By using the Ansible configuration file, you can streamline your workflow and manage servers more efficiently.

### Example Output of `/etc/motd` After Update:

```
This server is managed by Ansible.
```

Congratulations on completing **Lab 4.3: Managing Ansible Configuration Files**! You now have a better understanding of how to configure Ansible for efficient server management.

Happy Automating!
