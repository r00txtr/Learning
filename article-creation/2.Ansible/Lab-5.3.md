# Lab 5.3: Quiz 5 - Managing Secrets in Ansible

In this quiz, you will learn how to manage sensitive information, such as passwords, using encrypted files in Ansible. You will create a playbook that securely stores user credentials in an encrypted file and uses them to create a user on a managed host. By the end of this quiz, you will have written a playbook that securely creates a user with a defined password.

---

## Table of Contents
1. **Introduction**
2. **Step-by-Step Instructions**
    1. Create Directory and Configuration Files
    2. Create Encrypted Secrets File
    3. Write the Playbook to Use the Secrets
    4. Create a Password File
    5. Run the Playbook
    6. Verify the Results
3. **Conclusion**

---

## Introduction

In this quiz, you will:
1. Create an encrypted file to store sensitive variables, such as usernames and passwords.
2. Use these variables in a playbook to create a user on managed hosts.
3. Create a password file to decrypt the secrets and run the playbook.

You will perform these tasks on the **Ansible controller node**, `pod-[username]-controller`, and manage two hosts: `pod-[username]-managed1` and `pod-[username]-managed2`. Replace `[username]` with your actual username throughout the lab.

---

## Step-by-Step Instructions

### Step 1: Create Directory and Configuration Files

#### 1.1 Create a Working Directory

Start by creating a directory for your project:

```bash
cd ~
mkdir quiz-5
cd quiz-5
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

### Step 2: Create Encrypted Secrets File

You will now create an encrypted file that stores the sensitive variables. We will use **Ansible Vault** to encrypt the file.

#### 2.1 Create the Encrypted File

Create an encrypted file using Ansible Vault:

```bash
ansible-vault create quiz-Secret.yml
```

You will be prompted to enter a password. Use the password `adinusa`.

After entering the password, add the following content to the file:

```yaml
username: [username]
passwd: adinusa2023
```

Replace `[username]` with your actual username.

#### 2.2 Verify the Encrypted File

You can verify that the file is encrypted by using the `cat` command:

```bash
cat quiz-Secret.yml
```

You should see encrypted content, not the actual variables.

---

### Step 3: Write the Playbook to Use the Secrets

Create a playbook that uses the variables from the encrypted file to create a user.

#### 3.1 Create the Playbook

Create the playbook file:

```bash
vim quiz-5_secrets.yml
```

Add the following content:

```yaml
---
- name: Create user from encrypted secrets
  hosts: managed
  become: true
  vars_files:
    - quiz-Secret.yml

  tasks:
    - name: Create a user
      user:
        name: "{{ username }}"
        password: "{{ passwd | password_hash('sha512') }}"
        state: present
```

Explanation:
- **vars_files**: Loads the encrypted `quiz-Secret.yml` file, which contains the variables.
- **user module**: Creates a user with the username stored in the `username` variable and sets the password using the `passwd` variable, hashed using the **SHA512** algorithm.

---

### Step 4: Create a Password File

To automate the decryption of the vault file, we will create a password file.

#### 4.1 Create the Password File

Create a file called `quiz-pass` and store the plain text password for the vault:

```bash
echo "adinusa" > quiz-pass
```

#### 4.2 Change the Permissions of the Password File

To secure the password file, change its permissions so that only the owner can read it:

```bash
chmod 600 quiz-pass
```

---

### Step 5: Run the Playbook

Now, you will run the playbook, using the password file to decrypt the secrets.

#### 5.1 Syntax Check

Before running the playbook, perform a syntax check:

```bash
ansible-playbook quiz-5_secrets.yml --syntax-check
```

#### 5.2 Run the Playbook

Run the playbook using the password file to decrypt the secrets:

```bash
ansible-playbook quiz-5_secrets.yml --vault-password-file quiz-pass
```

---

### Step 6: Verify the Results

#### 6.1 Check the User on Managed Hosts

You can manually verify that the user was created by SSHing into `pod-[username]-managed1` or `pod-[username]-managed2`:

```bash
ssh pod-[username]-managed1
```

Run the following command to check if the user was created:

```bash
id [username]
```

You should see details about the user.

#### 6.2 Test the Password

To verify that the password is correct, try switching to the new user:

```bash
su - [username]
```

When prompted for the password, enter `adinusa2023`. You should be able to log in as the user.

---

## Conclusion

In this quiz, you learned how to:
1. Use **Ansible Vault** to securely store sensitive information, such as usernames and passwords.
2. Create a playbook that uses encrypted variables to create a user on managed hosts.
3. Use a password file to automate the decryption process.
4. Verify that the user was created and that the password works.

By managing secrets securely with Ansible Vault, you can safely store and use sensitive information in your playbooks.

### Example Output of `id [username]`:

```
uid=1001([username]) gid=1001([username]) groups=1001([username])
```

Congratulations on completing **Lab 5.3: Quiz 5 - Managing Secrets in Ansible**! You now have the skills to securely manage sensitive data in your Ansible playbooks.

Happy Automating!
