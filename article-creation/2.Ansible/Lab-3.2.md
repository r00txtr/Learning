# Lab 3.2: Installing Ansible

In this lab, you will learn how to install and configure **Ansible** on a controller node and set up an inventory to manage other nodes in your network. Ansible is a powerful automation tool that allows you to manage servers and applications at scale using simple YAML configuration files. By the end of this lab, you will have installed Ansible, set up an inventory, and verified communication with your managed nodes.

---

## Table of Contents
1. **Introduction**
2. **Step-by-Step Instructions**
    1. Installing Ansible
    2. Configuring the Ansible Inventory
    3. Verifying the Setup
3. **Conclusion**

---

## Introduction

In this lab, you will:
1. Install Ansible on a controller node.
2. Set up the Ansible inventory file to define managed nodes.
3. Use Ansible to verify that you can communicate with your managed nodes using a simple ping command.

You will perform these actions on the controller node, `pod-[username]-controller`, and manage the nodes `pod-[username]-managed1` and `pod-[username]-managed2`.

---

## Step-by-Step Instructions

### Step 1: Installing Ansible

#### 1.1 Update the System Packages

Start by updating the package list to ensure that your system has the latest information about available packages:

```bash
sudo apt update
```

#### 1.2 Install the Required Packages

Install the necessary dependencies to add new repositories:

```bash
sudo apt install -y software-properties-common
```

This installs the **software-properties-common** package, which is required to manage PPAs (Personal Package Archives).

#### 1.3 Add the Ansible PPA and Install Ansible

Next, add the official Ansible PPA to your system and install Ansible:

```bash
sudo add-apt-repository --yes --update ppa:ansible/ansible
sudo apt install ansible
```

The first command adds the PPA and updates the package list. The second command installs Ansible on your system.

#### 1.4 Verify the Installation

To confirm that Ansible has been installed successfully, check the version:

```bash
ansible --version
```

You should see output that shows the installed version of Ansible, similar to:

```
ansible 2.9.6
```

---

### Step 2: Configuring the Ansible Inventory

The **Ansible inventory** is a file where you define the managed nodes that Ansible will interact with. In this lab, you will set up an inventory file to manage two nodes: `pod-[username]-managed1` and `pod-[username]-managed2`.

#### 2.1 Create the Inventory Directory

Create the directory where the Ansible configuration will be stored:

```bash
sudo mkdir -p /etc/ansible
```

#### 2.2 Configure the Ansible Inventory

Open the inventory file for editing:

```bash
sudo vim /etc/ansible/hosts
```

Add the following content to the file:

```ini
pod-[username]-managed1

[webservers]
pod-[username]-managed2
```

Explanation:
- **pod-[username]-managed1**: This is the first node being managed by Ansible.
- **[webservers]**: This is a group called "webservers" that includes `pod-[username]-managed2`. You can use groups to organize nodes with similar roles.

Replace `[username]` with your actual username.

---

### Step 3: Verifying the Setup

#### 3.1 List All Hosts

You can list all the hosts in your Ansible inventory using the following command:

```bash
ansible all --list-hosts
```

The output should list `pod-[username]-managed1` and `pod-[username]-managed2`.

#### 3.2 List Ungrouped Hosts

To list all hosts that are not part of any group, run:

```bash
ansible ungrouped --list-hosts
```

This command shows any nodes that are not part of the "webservers" group.

#### 3.3 List Hosts in a Group

To list the hosts in the "webservers" group, run:

```bash
ansible webservers --list-hosts
```

This should list `pod-[username]-managed2`.

#### 3.4 Ping All Nodes

Finally, use Ansible's **ping** module to check communication with all managed nodes:

```bash
ansible all -m ping
```

If the connection is successful, you will see a **pong** response from each node.

---

## Conclusion

In this lab, you learned how to:
1. Install **Ansible** on a controller node.
2. Set up the Ansible inventory to define managed nodes.
3. Verify that Ansible can communicate with managed nodes using the `ping` module.

Ansible is a powerful tool for automating system administration tasks, and the ability to communicate with managed nodes is the first step in building more complex automation scripts.

### Example Output for `ansible all -m ping`:

```
pod-[username]-managed1 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
pod-[username]-managed2 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

Congratulations on completing **Lab 3.2: Installing Ansible**! You now have the basics of setting up and managing nodes using Ansible.

Happy Automating!
