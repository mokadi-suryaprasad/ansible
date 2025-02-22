# What are Ansible Playbooks?
## Ansible Playbooks

Ansible Playbooks are used to define and automate tasks in a structured way using YAML. These tasks can be anything from managing packages, starting services, configuring files, and more. Playbooks are essential for automating infrastructure management and configuration.

### Key Concepts

1. **Plays**: A play is a set of tasks to execute on specific hosts.
2. **Tasks**: A task is an individual action, like installing a package.
3. **Modules**: Modules are the tools that Ansible uses to perform tasks, such as `apt` or `service`.
4. **Variables**: Variables allow for customization of playbooks and make them reusable.
5. **Hosts**: Hosts are the machines that the playbook will run on.
6. **Handlers**: Handlers are special tasks that run only when notified.

### Example Playbook

```yaml
---
- name: Install and Start Nginx
  hosts: all
  become: yes

  tasks:
    - name: Install Nginx
      apt:
        name: nginx
        state: present

    - name: Start Nginx service
      service:
        name: nginx
        state: started
```
### Playbook Execution
- To run a playbook, use the following command:
```bash
ansible-playbook my_playbook.yml
```
### Best Practices

1. **Keep playbooks simple:** Avoid complex, long playbooks. Split them into smaller, manageable pieces.
2. **Use variables:** Variables help you customize tasks and improve playbook reusability.
3. **Use roles:** Roles organize tasks into reusable components.
4. **Template configuration files:** Use Jinja2 templates for configuration management.
5. **Document your playbooks:** Add descriptions and comments to your playbooks for clarity.

### MySQL Installation Playbook Example:

```yaml
---
# This is an Ansible Playbook that installs MySQL, starts the service,
# and sets up a simple database and user.

- name: Install and configure MySQL server
  hosts: mysql_servers  # This play will run on all hosts in the "mysql_servers" group.
  become: yes  # Run tasks as superuser (root) on remote machines.

  vars:
    mysql_package: mysql-server  # The MySQL package to install (change as per your OS, e.g., mysql-server or mariadb-server).
    mysql_service: mysql  # The MySQL service name to manage.
    mysql_root_password: "root_password"  # Root password for MySQL (use a strong password in production).
    mysql_database: example_db  # The database to be created.
    mysql_user: example_user  # The user to be created.
    mysql_user_password: "user_password"  # The password for the new user.

  tasks:
    - name: Install MySQL package  # Install MySQL server package.
      apt:
        name: "{{ mysql_package }}"
        state: present  # Ensures MySQL is installed.
        update_cache: yes  # Updates the apt cache before installing the package.

    - name: Start MySQL service  # Start the MySQL service.
      service:
        name: "{{ mysql_service }}"
        state: started  # Ensures MySQL service is started.
        enabled: yes  # Ensures the service starts automatically on boot.

    - name: Set MySQL root password  # Set the root password for MySQL.
      mysql_user:
        name: root
        password: "{{ mysql_root_password }}"  # Use the password defined in variables.
        host: localhost  # Localhost access for the root user.
        state: present  # Ensures the root password is set.

    - name: Create a MySQL database  # Create the database.
      mysql_db:
        name: "{{ mysql_database }}"
        state: present  # Ensures the database is created.

    - name: Create a MySQL user  # Create the user.
      mysql_user:
        name: "{{ mysql_user }}"
        password: "{{ mysql_user_password }}"  # Use the password defined in variables.
        host: "%"  # Allow the user to connect from any host.
        state: present  # Ensures the user is created.

    - name: Grant privileges to MySQL user  # Grant all privileges to the user on the database.
      mysql_user:
        name: "{{ mysql_user }}"
        password: "{{ mysql_user_password }}"
        host: "%"
        priv: "{{ mysql_database }}.*:ALL"  # Grant all privileges on the specific database.
        state: present  # Ensures privileges are set.

  # This playbook installs MySQL, creates a database and user, and configures the necessary privileges.
```

# Ansible Yum Module

The **Ansible `yum` module** is used to manage packages on Red Hat-based systems (like CentOS, Fedora, RHEL). It interacts with the system's package manager (`yum`) to perform actions such as installing, upgrading, or removing software packages.

### Why Use the `yum` Module?

Ansible is a powerful automation tool, and the `yum` module simplifies software management on Red Hat-based Linux systems. You can easily ensure that the correct software packages are installed, upgraded, or removed, without needing to manually run `yum` commands on each server.

### Key Features of the `yum` Module

- **Install packages**: You can use it to install new packages.
- **Upgrade packages**: Automatically upgrade packages to the latest available version.
- **Remove packages**: Uninstall software packages from the system.
- **Ensure specific versions**: You can specify exactly which version of a package you need.
- **Manage repositories**: It can be used to install packages from specific repositories.
- **State management**: You can define the state of a package, like ensuring it's installed or absent.

### Basic Syntax

Here’s a simple breakdown of how you use the `yum` module in an Ansible playbook:

```yaml
- name: Install a package
  ansible.builtin.yum:
    name: <package_name>   # Name of the package
    state: present         # Ensure the package is installed (default)

- name: Remove a package
  ansible.builtin.yum:
    name: <package_name>   # Name of the package
    state: absent          # Ensure the package is removed

- name: Ensure a specific version of a package is installed
  ansible.builtin.yum:
    name: <package_name>-<version>  # Name with version (e.g., httpd-2.4.6)
    state: present                 # Ensure the specific version is installed
```

### Parameters

**name:** The name of the package you want to install or remove. It can also include a version or repository.
**state:** This defines the desired state of the package. Common values are:
- **present:** Ensures the package is installed.
- **absent:** Ensures the package is removed.
- **latest:** Installs the latest version available.
**update_cache:** If set to yes, it will refresh the package cache before attempting to install or update a package.
**enablerepo:** Enables a specific repository to install packages from. Use this if the package isn’t in the default repository.

### Example Playbook

- Here’s an example of an Ansible playbook that installs the httpd package, ensures it’s the latest version, and removes nginx if it’s present:

```yaml
---
- name: Manage Packages with Yum
  hosts: webservers
  become: yes

  tasks:
    - name: Install the latest version of httpd
      ansible.builtin.yum:
        name: httpd
        state: latest

    - name: Remove nginx if installed
      ansible.builtin.yum:
        name: nginx
        state: absent
```
---
```yaml
    - name: Install multiple packages
      ansible.builtin.yum:
        name:
          - httpd
          - git
          - curl
        state: present
```
---

# Ansible File Module

The **Ansible `file` module** is used to manage file properties on remote systems. It allows you to set permissions, ownership, and other attributes for files and directories, making it an essential tool for configuration management.

### Why Use the `file` Module?

The `file` module helps you ensure the correct file and directory settings are applied automatically across multiple systems. This is particularly useful for managing permissions, creating files or directories, and modifying their ownership or modes without manually logging into each system.

### Key Features of the `file` Module

- **Set file permissions**: Modify file or directory permissions.
- **Change ownership**: Assign specific users and groups as the owners of files and directories.
- **Create files and directories**: Automatically create files or directories if they don’t exist.
- **Ensure file state**: Make sure a file exists or is absent, and manage symbolic links.

### Basic Syntax

Here’s a simple breakdown of how you can use the `file` module in an Ansible playbook:

```yaml
- name: Set file permissions
  ansible.builtin.file:
    path: /path/to/file    # The path to the file or directory
    mode: '0755'           # File permissions in octal format

- name: Change file ownership
  ansible.builtin.file:
    path: /path/to/file
    owner: username        # The user who should own the file
    group: groupname       # The group who should own the file

- name: Create a directory
  ansible.builtin.file:
    path: /path/to/directory
    state: directory       # Ensures the directory is present

- name: Ensure a file is absent
  ansible.builtin.file:
    path: /path/to/file
    state: absent          # Ensures the file is removed
```

### Parameters

**path:** The path to the file or directory that you want to manage. This is required.
**state:** Defines the desired state of the file or directory. Common values are:
- **file:** Ensures the path is a regular file.
- **directory:** Ensures the path is a directory.
- **absent:** Ensures the file or directory is removed.
- **link:** Ensures the path is a symbolic link.
**owner:** The user who should own the file or directory. This is optional.
**group:** The group who should own the file or directory. This is optional.
**mode:** The file permissions in octal format (e.g., 0644 for readable by owner and group, and readable by others). This is optional.
**recurse:** If set to yes, it will apply the permissions and ownership recursively to all files and directories within the specified directory.

### Example Playbook
- Here’s an example of an Ansible playbook that manages file permissions and ownership:
---
```yaml
- name: Manage Files and Directories
  hosts: webservers
  become: yes

  tasks:
    - name: Create a directory with proper permissions
      ansible.builtin.file:
        path: /var/www/html
        state: directory
        owner: apache
        group: apache
        mode: '0755'

    - name: Ensure a configuration file exists with specific permissions
      ansible.builtin.file:
        path: /etc/httpd.conf
        state: file
        owner: root
        group: root
        mode: '0644'

    - name: Remove a log file
      ansible.builtin.file:
        path: /var/log/application.log
        state: absent
```

# Ansible Copy Module

The **Ansible `copy` module** is used to copy files from your local machine to remote systems. It’s an easy way to transfer files to your servers during automation.

### Why Use the `copy` Module?

The `copy` module helps automate file distribution. Whether you’re copying configuration files, scripts, or other important files to multiple systems, this module does the job without needing to manually upload them.

### Basic Examples

1. **Copy a file from local to remote:**

```yaml
- name: Copy a file to a remote system
  ansible.builtin.copy:
    src: /path/to/local/file    # Local path to the file
    dest: /path/to/remote/file  # Remote path where file should go
```
2. **Copy a file and set permissions:**

```yaml
- name: Copy a file with specific permissions
  ansible.builtin.copy:
    src: /path/to/local/file
    dest: /path/to/remote/file
    mode: '0644'                # Set file permissions
```
3. **Copy a file and change ownership:**
```yaml
- name: Copy a file and set ownership
  ansible.builtin.copy:
    src: /path/to/local/file
    dest: /path/to/remote/file
    owner: username             # Set file owner
    group: groupname            # Set file group
```
4. **Copy a directory from local to remote (with recursion):**

```yaml
- name: Copy a directory to a remote system
  ansible.builtin.copy:
    src: /path/to/local/directory  # Local directory path
    dest: /path/to/remote/directory  # Remote directory path
    recurse: yes                   # Copy files in subdirectories
```

### Important Parameters
**src:** The source file or directory on your local machine.
**dest:** The destination file or directory on the remote system.
**mode:** File permissions in octal format (e.g., 0644).
**owner and group:** Set the user and group ownership for the file.
**recurse:** If set to yes, it copies all files inside a directory (used for directories).

### Example Playbook

```yaml
---
- name: Copy files to remote servers
  hosts: webservers
  become: yes

  tasks:
    - name: Copy the Apache configuration file
      ansible.builtin.copy:
        src: /files/httpd.conf  # Local path
        dest: /etc/httpd/httpd.conf  # Remote path
        owner: root
        group: root
        mode: '0644'

    - name: Copy the website content directory
      ansible.builtin.copy:
        src: /files/website/  # Local directory
        dest: /var/www/html/   # Remote directory
        recurse: yes           # Copy all files inside
```
