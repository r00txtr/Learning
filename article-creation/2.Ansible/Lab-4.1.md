# Lab 4.1: Running Ad-hoc Commands in Ansible

In this lab, we will learn how to execute **ad-hoc commands** using Ansible. Ad-hoc commands allow you to perform quick tasks across your managed nodes without the need to write a playbook. This is useful for one-time tasks like checking system information, managing files, or running basic commands across multiple servers.

By the end of this lab, you will:
1. Run commands on all nodes to gather system information.
2. Update content on a managed node.
3. Verify the changes using SSH.

---

## Table of Contents
1. **Introduction**
2. **Step-by-Step Instructions**
    1. Show Hostname on All Nodes
    2. Show System Facts for a Specific Node
    3. Check Information on Localhost
    4. Update the Content of a File on a Managed Node
    5. Verify the Update on the Managed Node
3. **Verification**
4. **Conclusion**

---

## Introduction

In Ansible, ad-hoc commands are simple, one-liner commands that allow you to perform quick tasks across multiple nodes. These commands can be used to run shell commands, copy files, gather information, and more. You can run ad-hoc commands from the Ansible controller to your managed nodes.

You will run the commands from `pod-[username]-controller` to interact with `pod-[username]-managed1` and other nodes. Replace `[username]` with your actual username throughout the lab.

---

## Step-by-Step Instructions

### Step 1: Show Hostname on All Nodes

You can use Ansible to retrieve the hostname of all nodes in your inventory.

#### 1.1 Run the Ad-hoc Command

To show the hostname of all nodes, use the **command** module in Ansible:

```bash
ansible all -m command -a "hostname"
```

Explanation:
- **all**: Targets all nodes in your inventory.
- **-m command**: Specifies the `command` module to run shell commands.
- **-a "hostname"**: Runs the `hostname` command on all nodes.

You should see output displaying the hostname of each node.

---

### Step 2: Show System Facts for Managed Node

Ansible can collect and display system facts such as hardware information, OS details, and more.

#### 2.1 Gather Facts from Managed1

To gather system facts from the node `pod-[username]-managed1`, run:

```bash
ansible pod-[username]-managed1 -m setup
```

Explanation:
- **pod-[username]-managed1**: Targets the specific node.
- **-m setup**: Uses the `setup` module to gather and display system facts.

This will display detailed information about the managed node, such as its IP address, OS version, and more.

---

### Step 3: Check Information on Localhost

You can also run commands on the **localhost** (the controller node) to gather information or perform tasks.

#### 3.1 Run Commands on Localhost

To check user information on the localhost, run:

```bash
ansible localhost -m command -a 'id'
```

This will display the user ID and group information of the current user.

#### 3.2 Run Commands as a Specific User

If you want to run a command as a specific user (e.g., `student`), run:

```bash
ansible localhost -u student -m command -a 'id'
```

Explanation:
- **-u student**: Specifies the user (`student`) under which the command will be executed.

---

### Step 4: Update the Content of a File on a Managed Node

Ansible can also be used to modify files on managed nodes. In this step, we will update the content of the **/etc/motd** file on `pod-[username]-managed1`.

#### 4.1 Copy New Content to `/etc/motd`

To update the content of the **/etc/motd** file with a custom message, use the **copy** module:

```bash
ansible pod-[username]-managed1 --become -u student -m copy -a "content='Executed by Ansible\n' dest=/etc/motd"
```

Explanation:
- **--become**: Runs the command with elevated privileges (similar to `sudo`).
- **-u student**: Specifies the user `student` to run the command.
- **-m copy**: Uses the `copy` module to copy content to a file.
- **"content='...' dest=/etc/motd"**: Replaces the content of the file `/etc/motd` with the provided string.

#### 4.2 Verify the Change

To verify that the content has been updated, run:

```bash
ansible pod-[username]-managed1 -u student -m command -a 'cat /etc/motd'
```

This will display the new content of the **/etc/motd** file on `pod-[username]-managed1`.

---

### Step 5: Verify the Update on the Managed Node

To verify the changes made to the **/etc/motd** file, SSH into the managed node and check the file manually.

#### 5.1 SSH into Managed Node

Log in to the `pod-[username]-managed1` node via SSH:

```bash
ssh pod-[username]-managed1
```

Once logged in, you should see the message **Executed by Ansible** displayed as part of the login message (this is the content of the **/etc/motd** file).

#### 5.2 Exit the Managed Node

After verifying the update, exit the SSH session:

```bash
exit
```

---

## Verification

To ensure that the lab was completed successfully, verify the following:
1. **Hostnames**: The hostname of all nodes should be displayed using `ansible all -m command -a "hostname"`.
2. **System Facts**: The facts about `pod-[username]-managed1` should be displayed using `ansible pod-[username]-managed1 -m setup`.
3. **File Update**: The content of **/etc/motd** on `pod-[username]-managed1` should be updated to say **Executed by Ansible** and verified using SSH.

---

## Conclusion

In this lab, you learned how to:
1. Run **ad-hoc commands** with Ansible to gather information and make changes across managed nodes.
2. Use the **command** and **copy** modules to interact with nodes and manage files.
3. Verify changes by SSHing into managed nodes.

Ad-hoc commands are a powerful way to perform quick tasks across your infrastructure without writing detailed playbooks.

### Example Output of `ansible all -m command -a "hostname"`:

```
pod-[username]-managed1 | CHANGED | rc=0 >>
pod-username-managed1

pod-[username]-managed2 | CHANGED | rc=0 >>
pod-username-managed2
```

Congratulations on completing **Lab 4.1: Running Ad-hoc Commands in Ansible**! You now have the skills to use Ansible for quick tasks across multiple nodes.

Happy Automating!
