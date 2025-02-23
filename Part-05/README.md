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



# Converting Shell Commands into Ansible Playbooks

## Introduction

This guide explains how to convert typical shell commands into Ansible playbooks. Ansible allows you to automate tasks that you would usually run in a shell, such as installing packages, managing files, and running commands. This approach makes the tasks repeatable, scalable, and more manageable.

---

## 1. Running a Command in the Shell

Imagine you need to run a simple command like `ls` to list files in a directory in the shell:

```bash
ls /home/user
```
```yaml
---
# This is a playbook to set up Tomcat on different systems
- name: Setup Tomcat
  hosts: all  # This means the playbook will run on all the servers listed in your inventory file
  become: true  # This makes sure the tasks are executed with superuser (root) permissions
  tasks:  # This section contains the steps to install and set up Tomcat

  # Step 1: Install Java on RedHat-based systems (like CentOS, RHEL)
  - name: install java
    yum:  # We use yum to install packages on RedHat-based systems
      name: java  # The name of the package to install is "java"
      state: installed  # This ensures that the Java package is installed
    when: ansible_os_family == "RedHat"  # This task will run only on RedHat-based systems

  # Step 2: Install Java on Debian-based systems (like Ubuntu)
  - name: install java on ubuntu
    apt:  # We use apt to install packages on Debian-based systems like Ubuntu
      name: default-jdk  # We install the default Java Development Kit (JDK)
      state: present  # This ensures that Java is installed
    when: ansible_os_family == "Debian"  # This task will run only on Debian-based systems

  # Step 3: Download Tomcat tar file from the internet
  - name: download tomcat packages
    get_url:  # This module downloads a file from the internet
      url: http://mirrors.estointernet.in/apache/tomcat/tomcat-8/v8.5.50/bin/apache-tomcat-8.5.50.tar.gz  # URL where the Tomcat tar file is located
      dest: /opt  # This is the destination directory where Tomcat will be downloaded

  # Step 4: Extract (untar) the downloaded Tomcat tar file
  - name: untar apache packages
    unarchive:  # This module extracts files from a tarball (compressed file)
      src: /opt/apache-tomcat-8.5.50.tar.gz  # The path to the downloaded Tomcat tar file
      dest: /opt  # The directory where the files will be extracted to
      remote_src: yes  # This means the tar file is already on the remote server, so we don't need to upload it

  # Step 5: Give permissions to the startup.sh file (so it can be executed)
  - name: add execution permissions on startup.sh file
    file:  # This module helps manage files and directories
      path: /opt/apache-tomcat-8.5.50/bin/startup.sh  # Path to the startup.sh script that starts Tomcat
      mode: 0777  # This gives full permissions to the file (read, write, execute)

  # Step 6: Start the Tomcat service using the startup.sh script
  - name: start tomcat services
    shell: nohup ./startup.sh  # This command runs the startup.sh script in the background
    args:
      chdir: /opt/apache-tomcat-8.5.50/bin  # This makes sure we're in the correct directory to run the startup.sh script
```

#### Explanation

- **Step 1:** We install Java for RedHat-based systems using yum. It installs the Java package if it's not already installed.
- **Step 2:** We install Java for Debian-based systems (like Ubuntu) using apt. It installs the default JDK package.
- **Step 3:** We download the Tomcat tar file from the internet and save it to /opt.
- **Step 4:** We extract the contents of the Tomcat tar file to the /opt directory.
- **Step 5:** We make the startup.sh script executable, so it can run the Tomcat server.
- **Step 6:** We run the startup.sh script in the background, which starts Tomcat.


# Ansible Playbook - Using Tags

## Introduction

In Ansible, **tags** help you run only specific parts of your playbook. This is useful when you don't want to execute the entire playbook and just need to run a specific task or group of tasks.

You can add tags to tasks, plays, or roles. When you run the playbook, you can choose to execute only the tasks with certain tags. This makes your playbook more efficient and flexible.

## How to Use Tags

### 1. Tagging Tasks
You can add tags to individual tasks in your playbook. Here is an example of how to use tags in tasks:

```yaml
---
- name: Setup Web Server
  hosts: web_servers
  become: true
  tasks:
    - name: Install Apache HTTP server
      yum:
        name: httpd
        state: present
      tags:
        - apache  # Tag added to this task

    - name: Install PHP
      yum:
        name: php
        state: present
      tags:
        - php  # Tag added to this task

    - name: Start Apache service
      service:
        name: httpd
        state: started
      tags:
        - apache  # Tag added to this task

    - name: Install MySQL
      yum:
        name: mysql-server
        state: present
      tags:
        - mysql  # Tag added to this task

    - name: Start MySQL service
      service:
        name: mysqld
        state: started
      tags:
        - mysql  # Tag added to this task
```

- the tasks are tagged with either apache, php, or mysql.

### 2. Running Tasks with Specific Tags

- Once you have added tags to your playbook, you can run specific tasks based on the tags you used. Here’s how to run tasks with a specific tag.

**Example: Run Tasks with the apache Tag**

- To run only the tasks related to Apache (installing Apache and starting the Apache service), you can use the --tags option:

```bash
ansible-playbook setup_web_server.yml --tags apache
```
- This will only run tasks with the apache tag.

### 3. Skipping Tasks with Specific Tags

- If you want to run all tasks except the ones with a specific tag, you can use the --skip-tags option.

**Example: Skip Tasks with the mysql Tag**

- To skip the tasks related to MySQL, run the playbook like this:
```bash
ansible-playbook setup_web_server.yml --skip-tags mysql
```
- This will run all tasks except the ones related to MySQL.

### 4. Tagging Entire Plays

- You can also tag entire plays (not just individual tasks). This can be helpful when you want to run or skip entire plays in the playbook.

```bash
---
- name: Setup Web Server
  hosts: web_servers
  become: true
  tags:
    - webserver_setup  # Tag assigned to the entire play
  tasks:
    - name: Install Apache HTTP server
      yum:
        name: httpd
        state: present

    - name: Install PHP
      yum:
        name: php
        state: present
```
- Now, when you run the playbook with the --tags webserver_setup option, the entire play will run, including all tasks under it:

```bash
ansible-playbook setup_web_server.yml --tags webserver_setup
```
#### Benefits of Using Tags:

**Selective Execution:** You can run only the specific parts of the playbook that you need.
**Efficiency:** You don’t have to run the entire playbook, which saves time, especially for large playbooks.
**Organization:** Tags help you organize your playbook into different sections, like installing Apache, PHP, or MySQL.

#### Example Use Case for Tags

- Imagine you have a playbook that sets up a complete LAMP stack (Linux, Apache, MySQL, PHP). You only want to run the Apache setup one day and the MySQL setup another day. By tagging the tasks and running them individually, you can save time and focus on what’s needed.


# HTTP Server Installation Playbook with Error Handling

This Ansible playbook installs and configures Apache HTTP Server (`httpd` for RedHat-based systems and `apache2` for Debian-based systems). It also copies an `index.html` file to the web server and ensures that Apache is running. The playbook includes error handling to make sure that even if some tasks fail, the playbook can continue running or retry specific tasks.

## Playbook Tasks

1. **Install Apache HTTP Server (RedHat-based systems)**: 
   - Installs `httpd` using the `yum` package manager for RedHat-based systems.

2. **Start Apache service (RedHat-based systems)**: 
   - Starts the Apache HTTP server service (`httpd`) if it is installed.

3. **Install Apache HTTP Server (Debian-based systems)**: 
   - Installs `apache2` using the `apt` package manager for Debian-based systems.

4. **Start Apache service (Debian-based systems)**: 
   - Starts the Apache HTTP server service (`apache2`) if it is installed.

5. **Copy `index.html` to the Web Server**: 
   - Copies the `index.html` file from `/opt/ansible/index.html` to the web server directory `/var/www/html`.

## Error Handling

### 1. **Ignoring Errors for Specific Tasks (`ignore_errors`)**

Some tasks in this playbook might not be critical, and we don't want them to stop the entire playbook if they fail. For instance, when installing Apache (`httpd` or `apache2`), if the package installation fails, we will ignore the error and continue with the next tasks.

Example:

```yaml
- name: Install package (RedHat-based systems)
  yum:
    name: httpd
    state: installed
  when: ansible_os_family == "RedHat"
  ignore_errors: yes  # Ignore errors if yum installation fails
```

```yaml
---
- name: This playbook installs and configures httpd and apache2
  hosts: all
  become: true
  tasks:
    - name: Install Apache HTTPD (RedHat-based systems)
      yum:
        name: httpd
        state: installed
      when: ansible_os_family == "RedHat"
      ignore_errors: yes  # Continue playbook even if yum installation fails
      tags:
        - apache

    - name: Start Apache HTTPD service (RedHat-based systems)
      service:
        name: httpd
        state: started
      when: ansible_os_family == "RedHat"
      failed_when: result.rc != 0  # Fail task if service fails to start
      retries: 3
      delay: 5
      until: result.state == "started"  # Retry 3 times with a 5-second delay
      tags:
        - apache

    - name: Install Apache2 (Debian-based systems)
      apt:
        name: apache2
        state: present
      when: ansible_os_family == "Debian"
      ignore_errors: yes  # Continue playbook even if apt installation fails
      tags:
        - apache

    - name: Start Apache2 service (Debian-based systems)
      service:
        name: apache2
        state: started
      when: ansible_os_family == "Debian"
      failed_when: result.rc != 0  # Fail task if service fails to start
      retries: 3
      delay: 5
      until: result.state == "started"  # Retry 3 times with a 5-second delay
      tags:
        - apache

    - name: Copy index.html to the web server
      copy:
        src: /opt/ansible/index.html
        dest: /var/www/html/index.html
        mode: '0666'
      notify:
        - Restart Apache service
      failed_when: result.rc != 0  # Fail task if file copy fails
      tags:
        - apache

  handlers:
    - name: Restart Apache HTTPD service (RedHat-based systems)
      service:
        name: httpd
        state: restarted
      when: ansible_os_family == "RedHat"
      failed_when: result.rc != 0  # Fail task if service fails to restart
      tags:
        - apache

    - name: Restart Apache2 service (Debian-based systems)
      service:
        name: apache2
        state: restarted
      when: ansible_os_family == "Debian"
      failed_when: result.rc != 0  # Fail task if service fails to restart
      tags:
        - apache
```

