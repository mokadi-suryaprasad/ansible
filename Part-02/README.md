# Ansible Ad-Hoc Commands

Ansible **ad-hoc commands** are simple one-time commands you run to quickly do tasks on multiple servers. You don’t need to write a full playbook for these tasks. These are great for checking things, installing software, or running commands on your servers without much setup.

## Ansible Ping Command

### What is the Ping Command?

The **Ping command** in Ansible is used to check if Ansible can talk to your servers. It sends a small message to each server and waits for a reply. If a server answers, that means it is online and Ansible can connect to it.

### How to Use the Ping Command

#### Command:
```bash
ansible all -m ping
```


## Ansible Command

### What is an Ansible Command?

An **Ansible command** is a way to run tasks on one or more remote servers without needing a full playbook. You can quickly check the system, install software, or manage services with these simple commands. They are fast and easy for everyday tasks.

### Basic Format of an Ansible Command

The general structure of an Ansible command is:

```bash
ansible [target] -m [module] -a "[arguments]"
```
#### 1.Ping All Servers

This command checks if Ansible can reach all the servers in your list.

```bash
ansible all -m ping
```

#### 2.Check the Uptime of All Servers

This command runs the uptime command on all your servers to see how long they've been running.
```bash
ansible all -m command -a "uptime"
```

#### 3.Install a Package on All Servers

This command installs the htop package (a system monitoring tool) on all your servers.
```bash
ansible all -m yum -a "name=htop state=present"
```
#### 4.Copy a File to All Servers

This command copies a file from your local machine to all your remote servers.
```bash
ansible all -m copy -a "src=/path/to/local/file dest=/path/to/remote/file"
```
#### 5.Start a Service on All Servers

This command starts the `httpd` service (Apache) on all your servers.
```bash
ansible all -m service -a "name=httpd state=started"
```

## Ansible `stat` Module

### What is the `stat` Module?

The **`stat`** module in Ansible is used to gather information about files or directories on remote servers. It helps you check things like:
- Whether a file or directory exists.
- The size of a file.
- File permissions (who can read or write to it).
- The last time a file was accessed or modified.

This is useful if you want to know more about a file before doing other tasks like copying, moving, or modifying it.

---

### How to Use the `stat` Module

The basic command for using the **`stat`** module is:

```bash
ansible [target] -m stat -a "path=[file_path]"
```

```bash
ansible all -m stat -a "path=/path/to/your/file"
```

## Ansible `yum` Module

### What is the `yum` Module?

The **`yum`** module in Ansible is used to manage software (packages) on Linux servers that use **Red Hat**, **CentOS**, or **Fedora**. You can use it to:
- Install new software.
- Remove software.
- Update software to the latest version.

`yum` is a tool used on these systems to manage software, and the Ansible `yum` module makes it easy to control that process across many servers.

---

### How to Use the `yum` Module

The basic command to use the **`yum`** module is:

```bash
ansible [target] -m yum -a "name=[package_name] state=[present|absent|latest]"
```

### Examples:

#### 1.Install a Package

What it does: Installs the `nginx` web server if it’s not installed.

```bash
ansible all -m yum -a "name=nginx state=present"
```


#### 2.Remove a Package
What it does: Removes the `nginx` web server from the servers.

```bash
ansible all -m yum -a "name=nginx state=absent"
```

#### 3.Update a Package to the Latest Version

```bash
ansible all -m yum -a "name=nginx state=latest"
```
What it does: Updates the `nginx` package to the latest version available.

## Ansible `user` Module

### What is the `user` Module?

The **`user`** module in Ansible is used to manage user accounts on remote Linux or Unix systems. You can use it to:
- Create new users.
- Modify existing users.
- Delete users.
- Manage user properties like passwords, groups, and home directories.

---

### How to Use the `user` Module

The basic command to use the **`user`** module is:

```bash
ansible [target] -m user -a "name=[username] [options]"
```

### Examples:

#### 1.Create a User
What it does: Creates a new user named alice on all servers.

```bash
ansible all -m user -a "name=alice state=present"
```
#### 2. Remove a User
What it does: Removes the user `alice` from all servers.

```bash
ansible all -m user -a "name=alice state=absent"
```
#### 3. Create a User with a Password
What it does: Creates a user `alice` with the specified encrypted password.

```bash
ansible all -m user -a "name=alice password=$6$random_salt$encrypted_password state=present"
```
#### 4. Create a User with Groups

What it does: Creates a user alice and adds her to the admin and sudo groups.
```bash
ansible all -m user -a "name=alice groups=admin,sudo state=present"
```

## Ansible Setup Module Guide

This guide explains how to use the **`setup`** module in Ansible to gather information about remote systems.

### What is the `setup` Module?

The **`setup`** module is a built-in Ansible module used to gather system facts about remote systems. These facts include detailed information such as:

- Operating system details (e.g., Ubuntu, CentOS)
- CPU architecture
- Memory usage
- Network interfaces and IP addresses
- Filesystems and disk usage
- Hostname and more...

### How to Use the `setup` Module

You can use the `setup` module with the **`ansible`** command to collect system facts from remote machines.

#### Basic Command:

```bash
ansible all -m setup -i inventory.ini
```
