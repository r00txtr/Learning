# Lab 6.1: Handling Task Failures in Ansible

In this lab, we will explore how to handle task failures in Ansible, including strategies to allow a playbook to continue after a task fails, how to respond to task failures with specific actions, and how to control task success or failure reports with conditional logic.

---

## Table of Contents
1. **Introduction**
2. **Step-by-Step Instructions**
    1. Creating Directory and Configuration Files
    2. Writing the Playbook to Handle Task Failures
    3. Using `ignore_errors` to Proceed After Failures
    4. Using `block`, `rescue`, and `always` for Failure Handling
    5. Controlling Task Status with `changed_when` and `failed_when`
3. **Conclusion**

---

## Introduction

In this lab, you will:
1. Learn how to handle failures in Ansible tasks.
2. Explore strategies to proceed after a task fails using `ignore_errors`.
3. Use the `block`, `rescue`, and `always` keywords to handle task failures and specify recovery steps.
4. Control how Ansible reports task success or failure with `changed_when` and `failed_when`.

You will perform these tasks on the **Ansible controller node**, `pod-[username]-controller`, and manage the host `pod-[username]-managed1`. Replace `[username]` with your actual username throughout the lab.

---

## Step-by-Step Instructions

### Step 1: Creating Directory and Configuration Files

#### 1.1 Create the Working Directory

Start by creating a directory for this lab:

```bash
mkdir managing-task-failure
cd managing-task-failure
```

#### 1.2 Create the `ansible.cfg` File

Create the `ansible.cfg` file to define the location of the inventory file:

```bash
vim ansible.cfg
```

Add the following content:

```ini
[defaults]
inventory=inventory
remote_user=student

[privilege_escalation]
become=True
become_ask_pass=False
```

#### 1.3 Create the Inventory File

Create the inventory file to define the target host:

```bash
vim inventory
```

Add the following content:

```ini
[managed]
pod-[username]-managed1
```

This defines a group called **managed** with `pod-[username]-managed1`.

---

### Step 2: Writing the Playbook to Handle Task Failures

#### 2.1 Create the Playbook

Create a playbook file named `task-failure.yml`:

```bash
vim task-failure.yml
```

Add the following content:

```yaml
---
- name: Lab Task Failure 
  hosts: managed
  vars:
    web_pkg: apache
    db_pkg: mariadb-server
    db_svc: mariadb

  tasks:
    - name: Install {{ web_pkg }} package
      apt:
        name: "{{ web_pkg }}"
        state: present

    - name: Install {{ db_pkg }} package
      apt:
        name: "{{ db_pkg }}"
        state: present
```

This playbook defines two tasks that attempt to install the **apache** (which will fail) and **mariadb-server** packages.

---

### Step 3: Using `ignore_errors` to Proceed After Failures

#### 3.1 Add `ignore_errors` to the Task

To allow the playbook to continue even if the `apache` package installation fails, modify the first task to use `ignore_errors`:

```yaml
- name: Install {{ web_pkg }} package
  apt:
    name: "{{ web_pkg }}"
    state: present
  ignore_errors: yes
```

#### 3.2 Run the Playbook

Run the playbook and observe the output:

```bash
ansible-playbook task-failure.yml
```

You should see that the first task failed but was ignored, allowing the second task to run successfully.

---

### Step 4: Using `block`, `rescue`, and `always` for Failure Handling

#### 4.1 Modify the Playbook to Use `block`, `rescue`, and `always`

Next, we will use the `block`, `rescue`, and `always` keywords to handle the failure and specify actions to take when a task fails:

```yaml
  tasks:
    - name: Set up webserver
      block:
        - name: Install {{ web_pkg }} package
          apt:
            name: "{{ web_pkg }}"
            state: present
      rescue:
        - name: Install {{ db_pkg }} package
          apt:
            name: "{{ db_pkg }}"
            state: present
      always:
        - name: Start {{ db_svc }} service
          service:
            name: "{{ db_svc }}"
            state: started
```

This will attempt to install the web package, but if it fails, it will install the database package instead. The `always` block will ensure that the **mariadb** service is started regardless of the outcome.

#### 4.2 Run the Playbook

Run the playbook and observe the output:

```bash
ansible-playbook task-failure.yml
```

The playbook will attempt to install the `apache` package, fail, then proceed to install the `mariadb-server` package, and finally start the `mariadb` service.

---

### Step 5: Controlling Task Status with `changed_when` and `failed_when`

#### 5.1 Using `changed_when` to Control Task Status

You can control whether a task reports as changed using `changed_when`. Edit the playbook to add two tasks that show how this works:

```yaml
  tasks:
    - name: execute command
      command: hostnamectl
      register: Result
      changed_when: false

    - name: print command
      debug:
        var: Result.stdout_lines
```

The `changed_when: false` line ensures that the command will always report as **ok**, even though it runs.

#### 5.2 Using `failed_when` to Fail a Task Based on a Condition

You can also control when a task reports failure using `failed_when`. Add a condition to fail the task if `web_pkg` has a specific value:

```yaml
- name: Install {{ web_pkg }} package
  apt:
    name: "{{ web_pkg }}"
    state: present
  failed_when: web_pkg == "apache2"
```

This will cause the task to fail if `web_pkg` is set to `apache2`.

#### 5.3 Run the Playbook

Run the playbook and observe the output:

```bash
ansible-playbook task-failure.yml
```

You will see that the playbook fails when `web_pkg` equals `apache2`, but the `mariadb-server` package is installed, and the `mariadb` service is started in the rescue and always blocks.

---

## Conclusion

In this lab, you learned how to:
1. Use the `ignore_errors` keyword to allow playbooks to continue after a task failure.
2. Use `block`, `rescue`, and `always` to handle task failures and specify recovery actions.
3. Control task reporting using the `changed_when` and `failed_when` keywords.

By handling task failures effectively, you can create more resilient and flexible playbooks.

### Example Output:

```
TASK [Install apache2 package] ***********************************************************
fatal: [pod-[username]-managed1]: FAILED! => {"changed": false, "msg": "No package matching 'apache' is available"}
...ignoring

TASK [Install mariadb-server package] ****************************************************
changed: [pod-[username]-managed1]
```

Congratulations on completing **Lab 6.1: Handling Task Failures in Ansible**! You now have the skills to handle task failures gracefully in Ansible playbooks.

Happy Automating!
