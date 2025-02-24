# Ansible Vault Guide

## What is Ansible Vault?

Ansible Vault is a feature in Ansible that allows you to securely encrypt sensitive information such as passwords, API keys, and private configuration files within your automation scripts. This ensures that your sensitive data is not exposed in plain text, and helps keep it secure.

## Why Use Ansible Vault?

1. **Security**: Encrypt sensitive data to keep it safe from unauthorized access.
2. **Control**: Manage encrypted content directly from your playbooks without exposing sensitive data to version control systems.
3. **Compliance**: Meet security and compliance standards for data encryption.

# Ansible Vault Guide

## What is Ansible Vault?

Ansible Vault is a feature in Ansible that allows you to securely encrypt sensitive information such as passwords, API keys, and private configuration files within your automation scripts. This ensures that your sensitive data is not exposed in plain text, and helps keep it secure.

## Why Use Ansible Vault?

1. **Security**: Encrypt sensitive data to keep it safe from unauthorized access.
2. **Control**: Manage encrypted content directly from your playbooks without exposing sensitive data to version control systems.
3. **Compliance**: Meet security and compliance standards for data encryption.

## How to Use Ansible Vault

### 1. Create Encrypted Files

You can create a new encrypted file to store sensitive data. This file can be an entire playbook, a single variable, or any configuration file.

```bash
ansible-vault create secrets.yml
```

### 2. Edit Encrypted Files

- If you need to modify an already encrypted file, you can use this command:

```bash
ansible-vault edit secrets.yml
```
- It will securely open the file for editing, and once you save it, the file will remain encrypted.

### 3. Encrypt Existing Files

- To encrypt a file that was not previously encrypted:
```bash
ansible-vault encrypt file.yml
```
- This will encrypt the contents of file.yml.

### 4. Decrypt Files

- If you need to decrypt an encrypted file to view or edit it:
```bash
ansible-vault decrypt file.yml
```
- This will decrypt the file so you can view or modify it as needed.

### 5. View Encrypted Files

- To view the contents of an encrypted file without decrypting it permanently, use this command:

```bash
ansible-vault view secrets.yml
```
- This will show the contents of the file but will not decrypt it.

### 6. Running Playbooks with Encrypted Files

- When running a playbook that includes encrypted files, you can pass the --ask-vault-pass option to provide the vault password during runtime:
```bash
ansible-playbook site.yml --ask-vault-pass
```
- This ensures that the encrypted content is decrypted only at runtime.

## Examples of Using Ansible Vault

### 1. Create Encrypted GitHub Credentials

- First, you should create a file named secrets.yml where you will store your GitHub credentials securely.
- To encrypt the GitHub credentials:
```bash
ansible-vault create secrets.yml
```
- This will open the file in a text editor. Inside the file, store your GitHub credentials like this:
```yaml
github_username: "your_github_username"
github_token: "your_github_personal_access_token"
```
- When you're done, save and exit. Now, your credentials are encrypted and safe.

### 2. Ansible Playbook for Using GitHub Credentials

- Now, let's create a playbook called github_clone.yml that will use the credentials stored in secrets.yml to clone a GitHub repository.

```yaml
---
- name: Clone GitHub repository securely
  hosts: all
  become: true
  vars_files:
    - secrets.yml  # Reference to the encrypted GitHub credentials
  tasks:
    - name: Clone GitHub repository
      git:
        repo: "https://github.com/{{ github_username }}/your-repository.git"
        dest: "/path/to/destination/folder"
        clone: yes
        update: yes
        version: master
        force: yes
      environment:
        GIT_ASKPASS: /bin/echo
        GIT_PASSWORD: "{{ github_token }}"  # Use the encrypted GitHub token for authentication
      tags:
        - git
```
#### Explanation of the Playbook:

**hosts:** all: This means the playbook will run on all hosts in your inventory.
**become:** true: This allows the playbook to execute with elevated privileges (sudo).
**vars_files:** This tells Ansible to include the secrets.yml file, which contains your encrypted GitHub credentials.
**tasks:**
- **git:** This task uses the git Ansible module to clone a GitHub repository. You specify the repository URL using github_username (the encrypted username from secrets.yml), and the github_token is used as the password for authentication.
- **environment:** This section sets up the environment variables required for Git to use your credentials (the GitHub token) securely during cloning.

### 3. Running the Playbook

- When you're ready to run the playbook, you can execute it with the following command:

```bash
ansible-playbook github_clone.yml --ask-vault-pass
```
- --ask-vault-pass: This flag will prompt you to enter the vault password in order to decrypt secrets.yml and access the GitHub credentials.

# Ansible Roles

## What are Ansible Roles?

An Ansible role is a way to organize tasks. Each role has a set of instructions (tasks) that do something specific, like setting up a web server or installing a database. You can use roles in multiple playbooks, so you don’t have to write the same tasks again and again.

## What’s in a Role?

Each role is made of different folders and files:

1. **tasks/**: This folder contains the list of actions to be done (like installing packages).
2. **handlers/**: This folder contains tasks that only run when something important changes (like restarting a service).
3. **defaults/**: Here you set default settings for the role.
4. **vars/**: This folder holds variables (values) that the role will use.
5. **files/**: This folder holds files that you want to copy to the target server.
6. **templates/**: This folder holds templates to create files with dynamic values.
7. **meta/**: This file tells information about the role, like other roles it depends on.

## How to Use Roles in a Playbook

### Step 1: Install or Clone the Role

If the role is available online, you can install it using the `ansible-galaxy` command:

```bash
ansible-galaxy install username.role_name
```
