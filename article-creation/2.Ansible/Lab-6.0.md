# Lab 6.0: Quiz 6 - Using Loops in Ansible

In this quiz, you will learn how to use loops and manage user creation in Ansible. You will create two sets of user lists using loops, store encrypted passwords, and automate user creation on two managed hosts. By the end of this quiz, you will have written a playbook that securely creates multiple users on two hosts using Ansible loops.

---

## Table of Contents
1. **Introduction**
2. **Step-by-Step Instructions**
    1. Create Directory and Group Variables
    2. Generate Group Variables Using Loops
    3. Encrypt Passwords with Ansible Vault
    4. Write the Playbook to Create Users
    5. Run the Playbook
    6. Verify the Results
3. **Conclusion**

---

## Introduction

In this quiz, you will:
1. Generate group variable files that contain lists of users for two managed hosts.
2. Encrypt a password using Ansible Vault and use it in the playbook to create users.
3. Use a loop in the playbook to create users on the managed hosts with the specified password.

You will perform these tasks on the **Ansible controller node**, `pod-[username]-controller`, and manage two hosts: `pod-[username]-managed1` and `pod-[username]-managed2`. Replace `[username]` with your actual username throughout the lab.

---

## Step-by-Step Instructions

### Step 1: Create Directory and Group Variables

#### 1.1 Create the Working Directory

Start by creating a directory for your project:

```bash
cd ~
mkdir quiz-6
cd quiz-6
```

#### 1.2 Create the Group Variables Directory

Inside the `quiz-6` directory, create a directory named `group_vars`:

```bash
mkdir group_vars
cd group_vars
```

---

### Step 2: Generate Group Variables Using Loops

You will now create two files inside the `group_vars` directory that contain lists of users for the two managed hosts.

#### 2.1 Create the `managed1-host-<username>.yml` File

Create a file named `managed1-host-<username>.yml`. Replace `<username>` with your actual username:

```bash
vim managed1-host-<username>.yml
```

Add the following content using loops to generate user lists:

```yaml
list_user_ops_managed1:
{% for i in range(1, 51) %}
  - ops{{ i }}
{% endfor %}

list_user_dev_managed1:
{% for i in range(1, 51) %}
  - dev{{ i }}
{% endfor %}
```

#### 2.2 Create the `managed2-host-<username>.yml` File

Similarly, create another file named `managed2-host-<username>.yml`:

```bash
vim managed2-host-<username>.yml
```

Add the following content:

```yaml
list_user_ops_managed2:
{% for i in range(51, 101) %}
  - ops{{ i }}
{% endfor %}

list_user_dev_managed2:
{% for i in range(51, 101) %}
  - dev{{ i }}
{% endfor %}
```

This will generate lists of users `ops[51-100]` and `dev[51-100]` for `pod-[username]-managed2`.

---

### Step 3: Encrypt Passwords with Ansible Vault

You will now create an encrypted file to store the password that will be used to create the users.

#### 3.1 Create the Encrypted File

Create a file named `secret.yml` and encrypt it with **Ansible Vault**:

```bash
ansible-vault create secret.yml
```

You will be prompted to enter a password. Use the password `belajaransible`.

Once inside the file, add the following content:

```yaml
pass: adinusa88
```

#### 3.2 Create a Password File

To automate the decryption of the vault file, create a password file named `vault-pass`:

```bash
echo "belajaransible" > vault-pass
chmod 600 vault-pass
```

---

### Step 4: Write the Playbook to Create Users

Now, you will create a playbook that uses the variables from the group variable files and the encrypted password file to create users.

#### 4.1 Create the Playbook

Create a playbook file named `quiz-6_loop.yml`:

```bash
vim quiz-6_loop.yml
```

Add the following content:

```yaml
---
- name: Create users on pod-username-managed1
  hosts: pod-[username]-managed1
  become: true
  vars_files:
    - secret.yml

  tasks:
    - name: Create ops users on managed1
      user:
        name: "{{ item }}"
        password: "{{ pass | password_hash('sha512') }}"
        state: present
      loop: "{{ list_user_ops_managed1 }}"

    - name: Create dev users on managed1
      user:
        name: "{{ item }}"
        password: "{{ pass | password_hash('sha512') }}"
        state: present
      loop: "{{ list_user_dev_managed1 }}"

- name: Create users on pod-username-managed2
  hosts: pod-[username]-managed2
  become: true
  vars_files:
    - secret.yml

  tasks:
    - name: Create ops users on managed2
      user:
        name: "{{ item }}"
        password: "{{ pass | password_hash('sha512') }}"
        state: present
      loop: "{{ list_user_ops_managed2 }}"

    - name: Create dev users on managed2
      user:
        name: "{{ item }}"
        password: "{{ pass | password_hash('sha512') }}"
        state: present
      loop: "{{ list_user_dev_managed2 }}"
```

Explanation:
- **loop**: Loops through the lists of users defined in the `group_vars` files to create the users.
- **password_hash**: Hashes the password using SHA512 before assigning it to the users.

---

### Step 5: Run the Playbook

You will now run the playbook to create the users on both managed hosts.

#### 5.1 Syntax Check

Before running the playbook, perform a syntax check:

```bash
ansible-playbook quiz-6_loop.yml --syntax-check
```

#### 5.2 Run the Playbook

Run the playbook using the password file to decrypt the secrets:

```bash
ansible-playbook quiz-6_loop.yml --vault-password-file vault-pass
```

---

### Step 6: Verify the Results

#### 6.1 Check the Users on Managed Hosts

You can manually verify that the users were created by SSHing into `pod-[username]-managed1` or `pod-[username]-managed2`:

```bash
ssh pod-[username]-managed1
```

Run the following command to list the created users:

```bash
for i in {1..50}; do id dev$i; id ops$i; done
```

#### 6.2 Test the User Logins

To verify that the password is correct, try logging in as each user:

```bash
for i in {1..50}; do sshpass -p adinusa88 ssh dev$i@pod-[username]-managed1 whoami; done
```

You should see output like this:

```
dev1
dev2
...
dev50
```

---

## Conclusion

In this quiz, you learned how to:
1. Generate lists of users using loops in Ansible.
2. Encrypt passwords using **Ansible Vault** and use them in a playbook.
3. Create users on managed hosts using loops.
4. Verify that the users were created and can log in.

By using loops and encrypted files, you can automate the creation of a large number of users securely.

### Example Output of `sshpass`:

```
dev1
dev2
...
dev50
```

Congratulations on completing **Lab 6.0: Quiz 6 - Using Loops in Ansible**! You now have the skills to use loops and manage sensitive data in Ansible playbooks.

Happy Automating!
