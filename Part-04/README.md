# Apache Installation Playbook for Red Hat

This Ansible playbook automates the process of installing Apache on **Red Hat-based** Linux systems (RHEL, CentOS, etc.) and starting the Apache service.
This Ansible playbook automates the process of installing Apache on **Amazon EC2 instances** running Red Hat-based Linux distributions (RHEL, CentOS, etc.) and starting the Apache service.


## Requirements

- **Ansible**: Installed on the machine that will run the playbook.
- **SSH Access**: You need to have SSH access to the remote machine.
- **Red Hat System**: The target system should be Red Hat-based (RHEL, CentOS, etc.).

## How This Playbook Works

This playbook has the following tasks:
1. Install the Apache web server (httpd) on Red Hat-based systems.
2. Start and enable the Apache service to ensure it runs after a reboot.

## Setup Instructions

### 1. Install Ansible

Make sure Ansible is installed on your local machine. You can install it using the following command:

```bash
sudo yum install ansible   # For Red Hat-based systems
```

**install_apache_redhat.yml**

```yaml
---
- name: Install Apache Web Server on Red Hat-based systems
  hosts: redhat_servers
  become: yes
  tasks:
    - name: Install Apache on Red Hat/CentOS/RHEL
      yum:
        name: httpd
        state: present

    - name: Start Apache service
      service:
        name: httpd
        state: started
        enabled: yes
```

### Explanation of the Playbook:

- **Install Apache:** The playbook uses the yum module to install the httpd package (Apache) on the system.
- **Start Apache Service:** The playbook uses the service module to start the Apache service and ensures that it starts automatically on boot (enabled: yes).

### Run the Playbook

```bash
ansible-playbook -i hosts.ini install_apache_redhat.yml
sudo systemctl status httpd

```

# Apache Installation Playbook for Ubuntu

This Ansible playbook automates the process of installing Apache on **Ubuntu** and starting the Apache service.

## Requirements

- **Ansible**: Installed on the machine that will run the playbook.
- **SSH Access**: You need to have SSH access to the Ubuntu machine.
- **Ubuntu System**: The target system should be running Ubuntu (any version).

## How This Playbook Works

This playbook performs the following tasks:
1. Installs the Apache web server (apache2) on the Ubuntu system.
2. Starts and enables the Apache service to ensure it runs after a reboot.

## Setup Instructions

### 1. Install Ansible

Make sure Ansible is installed on your local machine. You can install it using the following command:

```bash
sudo apt update
sudo apt install ansible   # For Ubuntu
```

**install_apache_ubuntu.yml**

---
```yaml
- name: Install Apache Web Server on Ubuntu
  hosts: ubuntu_servers
  become: yes
  tasks:
    - name: Install Apache (apache2) on Ubuntu
      apt:
        name: apache2
        state: present
        update_cache: yes

    - name: Start Apache service
      service:
        name: apache2
        state: started
        enabled: yes
```

### Explanation of the Playbook:

- **Install Apache:** The playbook uses the apt module to install the apache2 package (Apache web server) on the Ubuntu system.
- **Start Apache Service:** The playbook uses the service module to start the Apache service and ensures it starts automatically on boot (enabled: yes).

### Run the Playbook
```bash
ansible-playbook -i hosts.ini install_apache_ubuntu.yml
sudo systemctl status apache2
```

# How to Install Apache on Ubuntu Using Ansible with Notify and Handlers

This guide explains how to use Ansible to install **Apache** (a web server) on **Ubuntu** and use **Notify** and **Handlers** to restart Apache only when needed.

## What are Notify and Handlers?

### **Notify**:
- **Notify** is a way to tell Ansible to run another task **only if something changes**. 
- **For example**, if Ansible installs Apache, it will notify another task to restart Apache. But this only happens **if** Apache was actually installed or updated.

### **Handlers**:
- **Handlers** are special tasks that only run if they are notified.
- They usually do things like **restarting services** (like Apache) after making a change (like installing or updating software).
  
## Example Playbook

This playbook will:
1. Install Apache on the Ubuntu machine.
2. Start the Apache service.
3. Restart Apache **only if it was installed or changed**.

Here’s the playbook:

```yaml
---
- name: Install Apache Web Server on Ubuntu
  hosts: ubuntu_servers
  become: yes
  tasks:
    - name: Install Apache (apache2) on Ubuntu
      apt:
        name: apache2
        state: present
        update_cache: yes
      notify: Restart Apache  # Notify to restart Apache if the task changes anything

    - name: Start Apache service
      service:
        name: apache2
        state: started
        enabled: yes

  handlers:
    - name: Restart Apache
      service:
        name: apache2
        state: restarted
```

# Gathering Facts in Ansible

In Ansible, **gathering facts** means collecting information about the target machine before running any tasks. This is important because it helps Ansible understand the system’s environment (like its OS version, IP address, hardware details, etc.) and use that information during the playbook execution.

## Key Points About Gathering Facts:

1. **Automatic Data Collection**:
   - Ansible automatically collects facts about the target machine at the start of the playbook.
   - Facts include information like the operating system, available memory, CPU details, IP address, and much more.

2. **Helps Make Decisions**:
   - Gathered facts allow Ansible to make decisions on which tasks to execute based on the system’s environment.
   - For example, Ansible can decide to use the `apt` package manager for Ubuntu and the `yum` package manager for Red Hat based on the gathered facts.

3. **Access Facts Easily**:
   - Facts are stored in a special variable called `ansible_facts` and can be accessed in your tasks.
   - For example, you can access the operating system name using `ansible_facts['distribution']` or its version using `ansible_facts['distribution_version']`.

4. **Use Facts in Tasks**:
   - You can use gathered facts in your tasks to customize the playbook’s behavior.
   - For example, if the operating system is Ubuntu, you might install Apache with `apt`. If it’s Red Hat, you would use `yum` instead.

5. **Improves Efficiency**:
   - Facts allow you to execute tasks that are specific to the system's environment. This ensures that only necessary actions are taken and avoids unnecessary steps.

6. **Disabling Fact Gathering**:
   - If you don’t need system information and want to speed up your playbook, you can disable gathering facts using `gather_facts: no`.
   - This is useful when you know the system’s environment and don’t need Ansible to check it.

7. **Saves Time and Effort**:
   - Gathering facts is quick and ensures Ansible can make informed decisions, reducing the need for manual configuration or redundant tasks.

8. **Example of Accessing Facts**:
   - You can easily print or use facts in your playbook. For example:
     ```yaml
     - name: Print the OS version
       debug:
         msg: "The OS version is {{ ansible_facts['distribution_version'] }}"
     ```

9. **Customized Facts**:
   - Ansible allows you to gather custom facts if needed. This is useful if your playbook needs specific system details that aren't automatically collected.

10. **Consistency Across Systems**:
    - By gathering facts, you ensure your playbook will behave consistently across different systems since Ansible will know the exact environment it is working with.

## How Does Gathering Facts Work?

By default, Ansible will gather facts automatically at the beginning of every playbook run. However, you can choose to skip this step if it's not necessary for your playbook.

### Example Playbook:

```yaml
---
- name: Gather Facts and Install Apache on Ubuntu
  hosts: ubuntu_servers
  become: yes
  tasks:
    - name: Install Apache (apache2) on Ubuntu
      apt:
        name: apache2
        state: present
        update_cache: yes
```

### In the example above:

- Ansible gathers facts automatically before running any tasks, such as installing Apache.
- You don’t have to explicitly ask Ansible to gather facts in the playbook; it happens automatically.

### How to Disable Fact Gathering:

```yaml
---
- name: Skip gathering facts and install Apache
  hosts: ubuntu_servers
  gather_facts: no
  tasks:
    - name: Install Apache (apache2) on Ubuntu
      apt:
        name: apache2
        state: present
        update_cache: yes
```
- Fact gathering is skipped with gather_facts: no, which can be helpful when you know the system environment beforehand.


# Ansible Playbook - Install and Start Apache Web Server

## Overview

This Ansible playbook installs and starts the **Apache Web Server** on different Linux distributions. It checks the operating system family (RedHat or Debian) and installs the correct Apache package (`httpd` for RedHat-based systems like CentOS or Fedora, and `apache2` for Debian-based systems like Ubuntu). After installation, it starts the Apache service and ensures it is enabled to start on boot.

---

## How It Works

This playbook uses conditional statements with the **`when`** clause to determine which tasks should run based on the operating system family (RedHat or Debian). It leverages **Ansible facts**, which are gathered automatically, to detect the operating system.

### Key Parts of the Playbook:

1. **RedHat-based Systems (RHEL, CentOS, Fedora)**
    - Install Apache with the `yum` package manager (`httpd`).
    - Start Apache using the `systemd` service manager, and enable it to start on boot.

2. **Debian-based Systems (Ubuntu, Debian)**
    - Install Apache with the `apt` package manager (`apache2`).
    - Start Apache using the `systemd` service manager, and enable it to start on boot.

---

## Playbook Explanation

```yaml
---
- name: Install Apache Web Server
  hosts: all
  become: yes
  tasks:
    # Install Apache on Red Hat-based systems (RHEL, CentOS, Fedora)
    - name: Install Apache on Red Hat
      yum:
        name: httpd
        state: present
      when: ansible_facts['os_family'] == "RedHat"

    # Install Apache on Ubuntu-based systems (Debian, Ubuntu)
    - name: Install Apache on Ubuntu
      apt:
        name: apache2
        state: present
      when: ansible_facts['os_family'] == "Debian"

    # Start Apache on Red Hat-based systems
    - name: Start Apache on Red Hat
      systemd:
        name: httpd
        state: started
        enabled: yes
      when: ansible_facts['os_family'] == "RedHat"

    # Start Apache on Ubuntu-based systems
    - name: Start Apache on Ubuntu
      systemd:
        name: apache2
        state: started
        enabled: yes
      when: ansible_facts['os_family'] == "Debian"
```

**Breakdown:**

1. **Hosts:**

- The hosts: all line means that this playbook will run on all servers in your Ansible inventory.

2. **Become:**

- The become: yes statement ensures that tasks are executed with elevated (root) privileges because installing and starting services requires administrative access.

3. **Tasks:**

Each task is conditional using the **when** statement:

- The first two tasks install Apache depending on the os_family (either "RedHat" or "Debian").
- The last two tasks start the Apache service and enable it to start on boot, based on the system type.

### How to Use the Playbook

1. **Set up your inventory file** to specify the target hosts where you want to install Apache.

- inventory_file
```ini
[web_servers]
server1
server2
```
2. **Run the playbook** with the following command:

```bash
ansible-playbook -i inventory_file install_apache.yml
```
---

# Ansible Playbook - Uninstall Apache Web Server

## Overview

This Ansible playbook helps you **uninstall Apache Web Server** from your servers. It works on two types of systems:
- **RedHat-based systems** (such as CentOS, RHEL, and Fedora)
- **Debian-based systems** (such as Ubuntu and Debian)

The playbook uses **conditional statements** to check the system type and remove the correct Apache package (either `httpd` for RedHat-based or `apache2` for Debian-based). It also stops the Apache service and ensures it does not start again on boot.

---

## How It Works

1. **For RedHat-based systems**:
    - The playbook removes the `httpd` package using the `yum` package manager.
    - It also stops the Apache service and prevents it from starting on boot using `systemd`.

2. **For Debian-based systems**:
    - The playbook removes the `apache2` package using the `apt` package manager.
    - It also stops the Apache service and prevents it from starting on boot using `systemd`.

---

## Playbook Explanation

```yaml
---
- name: Uninstall Apache Web Server
  hosts: all
  become: yes
  tasks:
    # Uninstall Apache on Red Hat-based systems (RHEL, CentOS, Fedora)
    - name: Uninstall Apache on Red Hat
      yum:
        name: httpd
        state: absent
      when: ansible_facts['os_family'] == "RedHat"

    # Uninstall Apache on Ubuntu-based systems (Debian, Ubuntu)
    - name: Uninstall Apache on Ubuntu
      apt:
        name: apache2
        state: absent
      when: ansible_facts['os_family'] == "Debian"

    # Stop Apache on Red Hat-based systems
    - name: Stop Apache on Red Hat
      systemd:
        name: httpd
        state: stopped
        enabled: no
      when: ansible_facts['os_family'] == "RedHat"

    # Stop Apache on Ubuntu-based systems
    - name: Stop Apache on Ubuntu
      systemd:
        name: apache2
        state: stopped
        enabled: no
      when: ansible_facts['os_family'] == "Debian"
```
---

# Ansible Playbook - Install Apache Web Server and Copy index.html

## Overview

This Ansible playbook helps you **install Apache Web Server** and **copy an `index.html` file** to the remote web server. It works on two types of systems:
- **RedHat-based systems** (such as CentOS, RHEL, and Fedora)
- **Debian-based systems** (such as Ubuntu and Debian)

The playbook performs the following tasks:
1. Installs the Apache Web Server (`httpd` or `apache2`) based on the OS type.
2. Starts the Apache service and ensures it starts on boot.
3. Copies the `index.html` file from your local machine to the web server's document root.

---

## How It Works

1. **For RedHat-based systems**:
    - The playbook installs the `httpd` package using the `yum` package manager.
    - It starts the Apache service and enables it to start on boot using `systemd`.

2. **For Debian-based systems**:
    - The playbook installs the `apache2` package using the `apt` package manager.
    - It starts the Apache service and enables it to start on boot using `systemd`.

3. **Copy Task**:
    - The playbook copies an `index.html` file from your local machine to the remote server. This is typically placed in the `/var/www/html` directory, which is the default document root for Apache.

---

## Playbook Explanation

```yaml
---
- name: Install Apache Web Server and Copy index.html
  hosts: all
  become: yes
  tasks:
    # Install Apache on Red Hat-based systems (RHEL, CentOS, Fedora)
    - name: Install Apache on Red Hat
      yum:
        name: httpd
        state: present
      when: ansible_facts['os_family'] == "RedHat"

    # Install Apache on Ubuntu-based systems (Debian, Ubuntu)
    - name: Install Apache on Ubuntu
      apt:
        name: apache2
        state: present
      when: ansible_facts['os_family'] == "Debian"

    # Start Apache on Red Hat-based systems
    - name: Start Apache on Red Hat
      systemd:
        name: httpd
        state: started
        enabled: yes
      when: ansible_facts['os_family'] == "RedHat"

    # Start Apache on Ubuntu-based systems
    - name: Start Apache on Ubuntu
      systemd:
        name: apache2
        state: started
        enabled: yes
      when: ansible_facts['os_family'] == "Debian"

    # Copy the index.html file to the remote server
    - name: Copy index.html to the web server
      copy:
        src: /path/to/local/index.html  # Local path to your index.html file
        dest: /var/www/html/index.html  # Destination path on the remote server
        owner: root                    # Optional: Set the file owner
        group: root                    # Optional: Set the file group
        mode: '0644'                   # Optional: Set file permissions
```
---

# Ansible Playbook - Install Multiple Software Packages

## Overview

This Ansible playbook is designed to **install multiple software packages** on remote servers. It works on two types of systems:
- **RedHat-based systems** (e.g., CentOS, RHEL, Fedora)
- **Debian-based systems** (e.g., Ubuntu, Debian)

The playbook does the following:
1. Installs a list of software packages on **RedHat-based** systems using the `yum` package manager.
2. Installs a list of software packages on **Debian-based** systems using the `apt` package manager.

### Software Packages Installed

The playbook installs the following software on both system types:
- **Nginx**: A web server.
- **Git**: A version control system.
- **Curl**: A tool to transfer data from or to a server.

---

## How It Works

### For RedHat-based Systems (e.g., CentOS, RHEL, Fedora):
1. The playbook uses the `yum` package manager to install the following packages:
   - Nginx
   - Git
   - Curl

### For Debian-based Systems (e.g., Ubuntu, Debian):
1. The playbook uses the `apt` package manager to install the following packages:
   - Nginx
   - Git
   - Curl

### Looping Through Packages
- The playbook uses the **loop** feature to install each package from the list one by one.

---

## Playbook Structure

```yaml
---
- name: Install Multiple Software Packages
  hosts: all
  become: yes
  tasks:
    # Install software on RedHat-based systems
    - name: Install software on RedHat-based systems
      yum:
        name: "{{ item }}"
        state: present
      loop: "{{ redhat_packages }}"
      when: ansible_facts['os_family'] == "RedHat"

    # Install software on Debian-based systems
    - name: Install software on Debian-based systems
      apt:
        name: "{{ item }}"
        state: present
      loop: "{{ debian_packages }}"
      when: ansible_facts['os_family'] == "Debian"

# Variables for software packages
vars:
  # List of packages for RedHat-based systems
  redhat_packages:
    - nginx
    - git
    - curl

  # List of packages for Debian-based systems
  debian_packages:
    - nginx
    - git
    - curl
```