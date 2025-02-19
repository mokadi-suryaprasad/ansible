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
