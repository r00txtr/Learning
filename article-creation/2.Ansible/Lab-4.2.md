# Lab 4.2: Managing Ansible Inventory

In this lab, we will learn how to create and manage an **Ansible inventory**. The inventory file is crucial in Ansible because it defines the nodes (hosts) that you want to manage. By organizing hosts into groups, you can apply tasks to multiple servers with a single command. You will create a custom inventory file, organize hosts into various groups, and verify the hosts using different Ansible commands.

---

## Table of Contents
1. **Introduction**
2. **Step-by-Step Instructions**
    1. Creating a Custom Inventory File
    2. Verifying the Managed Hosts in the Inventory
3. **Verification**
4. **Conclusion**

---

## Introduction

An Ansible **inventory** is a file that lists all the systems you want to manage. The inventory can define individual hosts or groups of hosts, allowing you to manage multiple systems more efficiently. In this lab, you will:
1. Create a custom inventory file and organize hosts into groups like **Bogor**, **Jakarta**, **WebServers**, **Testing**, and **Development**.
2. Verify the hosts in the inventory by using Ansible commands to list the hosts in different groups.

You will perform these actions on the **Ansible controller node**, `pod-[username]-controller`, and manage the nodes `pod-[username]-managed1` and `pod-[username]-managed2`. Replace `[username]` with your actual username throughout the lab.

---

## Step-by-Step Instructions

### Step 1: Creating a Custom Inventory File

#### 1.1 Create a Working Directory

First, create a new directory to store your inventory file and navigate into it:

```bash
mkdir ~/managing-inventory
cd ~/managing-inventory
```

This directory will contain your custom inventory file.

#### 1.2 Create the Inventory File

Next, create a new file called `inventory` inside the working directory:

```bash
vim inventory
```

Add the following content to define your hosts and groups:

```ini
pod-[username]-controller

[Bogor]
pod-[username]-managed1

[Jakarta]
pod-[username]-managed2

[WebServers]
pod-[username]-managed[1:2]

[Testing]
pod-[username]-managed1

[Development]
pod-[username]-managed2

[Indonesia:children]
Jakarta
Bogor
```

Explanation:
- **pod-[username]-controller**: This is the ungrouped controller node.
- **[Bogor]** and **[Jakarta]**: These groups represent hosts based on their location.
- **[WebServers]**: A group that includes both `managed1` and `managed2`.
- **[Testing]** and **[Development]**: Separate groups for testing and development environments.
- **[Indonesia:children]**: This group combines the **Bogor** and **Jakarta** groups.

Replace `[username]` with your actual username.

---

### Step 2: Verifying the Managed Hosts in the Inventory

Once the inventory file is created, we will use various Ansible commands to verify the hosts and groups.

#### 2.1 List All Hosts

To see the list of all hosts in the custom inventory file, run the following command:

```bash
ansible all -i inventory --list-hosts
```

You should see the following output:

```
hosts (3):
  pod-[username]-controller
  pod-[username]-managed1
  pod-[username]-managed2
```

This lists all hosts defined in the inventory.

#### 2.2 List Ungrouped Hosts

To list the hosts that are not part of any group, use the **ungrouped** keyword:

```bash
ansible ungrouped -i inventory --list-hosts
```

This will display:

```
hosts (1):
  pod-[username]-controller
```

The controller node is ungrouped because it is not part of any defined group.

#### 2.3 List Specific Hosts

You can also list specific hosts or groups of hosts. For example, to list **pod-[username]-managed1**, run:

```bash
ansible pod-[username]-managed1 -i inventory --list-hosts
```

The output should show:

```
hosts (1):
  pod-[username]-managed1
```

#### 2.4 List Hosts in the Development Group

To list the hosts in the **Development** group:

```bash
ansible Development -i inventory --list-hosts
```

The output should show:

```
hosts (1):
  pod-[username]-managed2
```

This group only contains the `managed2` node.

#### 2.5 List Hosts in the Testing Group

To list the hosts in the **Testing** group, run:

```bash
ansible Testing -i inventory --list-hosts
```

You should see:

```
hosts (1):
  pod-[username]-managed1
```

This group contains the `managed1` node.

#### 2.6 List Hosts in the Indonesia Group

The **Indonesia** group is a combined group that contains hosts from both the **Bogor** and **Jakarta** groups. To list the hosts in the **Indonesia** group, run:

```bash
ansible Indonesia -i inventory --list-hosts
```

The output should be:

```
hosts (2):
  pod-[username]-managed1
  pod-[username]-managed2
```

Both nodes are listed because the **Indonesia** group includes both **Bogor** and **Jakarta**.

---

## Verification

To ensure that the lab was completed successfully, verify the following:
1. **All Hosts**: Running `ansible all -i inventory --list-hosts` should show all defined hosts (controller, managed1, managed2).
2. **Ungrouped Hosts**: The controller node should appear as an ungrouped host.
3. **Development and Testing Groups**: You should be able to list the hosts in the **Development** and **Testing** groups, confirming that they are correctly assigned.

---

## Conclusion

In this lab, you learned how to:
1. Create a custom **Ansible inventory** file.
2. Organize hosts into multiple groups such as **WebServers**, **Testing**, and **Development**.
3. Verify hosts and groups using Ansible commands.

Managing the inventory is a crucial part of working with Ansible, as it allows you to easily manage large numbers of servers by organizing them into logical groups.

### Example Output of `ansible all -i inventory --list-hosts`:

```
hosts (3):
  pod-[username]-controller
  pod-[username]-managed1
  pod-[username]-managed2
```

Congratulations on completing **Lab 4.2: Managing Ansible Inventory**! You now have the skills to organize and manage hosts efficiently in Ansible.

Happy Automating!
