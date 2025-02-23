# Understanding Ansible Variables

Ansible variables enable you to manage differences between systems, environments, and configurations, making your automation tasks more streamlined and adaptable. In this guide, we'll dive into how Ansible variables can be best utilized through step-by-step guides and practical examples.

## What are Ansible Variables?

Ansible variables are dynamic components that allow for reusability in playbooks and roles, enhancing the efficiency of configurations.

By using variables, you can make your Ansible projects adaptable to different environments and scenarios, allowing for more robust automation and efficient configurations.

## Why Use Variables?

- **Dynamic configurations**: Variables allow you to define values that can change based on the environment or context, such as different server IPs, usernames, or package versions.
- **Simplified management**: By using variables, you can manage complex configurations more easily, as changes need to be made in only one place.
- **Enhanced readability**: Meaningful variable names make playbooks more understandable and maintainable.

## Variable Naming Rules

Ansible enforces specific rules for variable names to ensure consistency and prevent conflicts:

- **Start with a letter or underscore**: Variable names must begin with a letter (a-z, A-Z) or an underscore (_).
- **Allowed characters**: Subsequent characters can include letters, numbers (0–9), and underscores.
- **Avoid reserved words**: Do not use reserved words from Python or Ansible’s playbook keywords as variable names.

### Examples of valid variable names:
- `server_port`
- `_user_name`
- `backup_interval_7`

### Examples of invalid variable names:
- `1st_user` (Starts with a number)
- `user-name` (Contains a hyphen)
- `backup@time` (Contains an invalid character `@`)

Adhering to these naming conventions is a good start, but it's also important to give your variables **meaningful names** so anyone reading your Ansible playbooks will understand what they represent.

## Ansible User Creation Playbooks

- This project demonstrates how to create users on remote machines using Ansible. The playbooks are structured in a way that allows you to pass values dynamically when running the playbook, making it flexible and reusable.

### Directory Structure
- Here is the directory structure of the project:

``` python

ansible_project/
│
├── playbooks/
│   ├── main_playbook.yml
│   ├── create_user.yml
│   └── vars/
│       └── user_vars.yml
└── inventory.ini
```
---

#### 1. Inventory File (inventory.ini)

- This file defines the remote hosts (machines) where you want to create users. For example, let's define two hosts under the group webservers.

```ini
[webservers]
web1 ansible_host=192.168.1.10
web2 ansible_host=192.168.1.11
```
- web1 and web2 are two hosts where we want to create users.
- ansible_host is the IP address of each host.

#### 2. Main Playbook (main_playbook.yml)

- This playbook is the starting point. It calls another playbook `(create_user.yml)` to create users and passes dynamic values to it.

```yaml
---
- name: Main Playbook
  hosts: webservers
  gather_facts: no
  tasks:
    - name: Create a user by passing values dynamically
      include_playbook: create_user.yml
      vars:
        username: "{{ username }}"
        password: "{{ password }}"
        uid: "{{ uid }}"
        group: "{{ group }}"
```

**Explanation:** 

- This playbook will run on the hosts defined in the webservers group.
- It uses include_playbook to call the create_user.yml playbook.
- The variables username, password, uid, and group will be passed to the create_user.yml playbook.

#### 3. Create User Playbook (create_user.yml)

- This playbook is responsible for creating users on the remote hosts. It uses the values passed from the main playbook.

```yaml
---
- name: Create a user on the target system
  hosts: all
  gather_facts: no
  tasks:
    - name: Create a user
      ansible.builtin.user:
        name: "{{ username }}"
        password: "{{ password | password_hash('sha512') }}"
        uid: "{{ uid }}"
        group: "{{ group }}"
        state: present
```

**Explanation:**
- The ansible.builtin.user module is used to create the user.
- The password is hashed using password_hash('sha512') for security.
- The variables username, password, uid, and group are taken from the main_playbook.yml.

#### 4. Variable File (vars/user_vars.yml)

- This is an optional file to store default variables. You can use this file to keep default values for the users you want to create.

``` yaml

username: "newuser"
password: "password123"
uid: "1001"
group: "users"

```

- This file holds the default values for user creation (like username, password, etc.).
- This file is not strictly necessary unless you want to store default values in one place.

#### 5. How to Run the Playbook

- The key to passing values dynamically is to use the -e flag when running the playbook. Here’s how you can run the playbook and pass values for the user:

```bash
ansible-playbook main_playbook.yml -e "username=testuser password=securepassword uid=1002 group=developers"
```
**Explanation:**
- -e allows you to pass variables directly in the command line.
- username=testuser defines the username to be created.
- password=securepassword sets the password.
- uid=1002 sets the user ID.
- group=developers defines the group the user should belong to.


#### 6. How It Works

**Main Playbook (main_playbook.yml):**
- The main playbook runs on the hosts defined in inventory.ini.
- It calls create_user.yml and passes variables like username, password, uid, and group.
**Create User Playbook (create_user.yml):**
- This playbook receives the variables from the main playbook and creates the user with the given values.
**Running the Playbook:**
- When you run the playbook using the ansible-playbook command, you can pass different values for username, password, uid, and group each time without needing to modify the playbook.

#### 7. Example of Running the Playbook

- You can create users by running the following command with dynamic variables:

```bash
ansible-playbook main_playbook.yml -e "username=devuser password=mysecurepassword uid=2001 group=devgroup"
```
**In this case:**

- A new user devuser will be created with the password mysecurepassword.
- The user will have a UID of 2001 and will belong to the devgroup.

#### 8. Advantages of This Approach

**Modular Playbooks:** The logic for user creation is separated into a separate playbook (create_user.yml), making your playbooks easier to maintain.
**Dynamic Variables:** You can pass different values each time you run the playbook, so you don’t need to modify the playbook for each user.
**Easily Scalable:** You can scale this process to more users and systems by just passing new sets of variables without changing the playbook.
**Simplified Maintenance:** Keeping variables and user creation logic separate makes the playbooks cleaner and easier to manage.

