## Ansible

* **Introduction to Ansible**
    * Ansible is an IT automation tool.
    * It is free and open-source.
    * It is a powerful tool that can be used to automate a wide variety of tasks, including configuration management, application deployment, and infrastructure management.

* **Advantages of Using Ansible**
    * **Simple and easy to use:** Ansible uses a simple and easy-to-learn language (YAML) to define playbooks, which makes it easy for anyone to use, even those wits little or no programming experience.
    * **Agentless architecture:** Ansible does not require any agents to be installed on remote systems, which makes it easy to set up and use.
    * **Configuration management:** Ansible can be used to automate configuration management tasks such as provisioning, application deployment, and infrastructure management.
    * **Scalability:** Ansible can manage a large number of systems simultaneously making it ideal for large-scale deployments.
    * Ansible playbooks can be run multiple times without changing the system state.
    * **Open-source:** Ansible is an open-source tool, which means it is free to use and has a large community of contributors who regularly contribute to its development.
    * **Integration with other tools:** Ansible can be integrated with other tools such as Docker, Kubernetes, and AWS, which makes it versatile and easy to use in a variety of environments.

* **Getting Started with Ansible**
    * Ansible playbooks are YAML files that define the tasks that Ansible will execute.
    * Playbooks can be run on multiple servers at the same time.
    * Ansible is agentless, which means that it does not require any software to be installed on the servers that it manages.
---

To start, you'll need one server where Ansible will be installed and configured. This server will connect to other remote servers for automation tasks.

### Step 1: Install Ansible on the Main Server

1. **Install Ansible:**
   - Follow the steps outlined in the official documentation [here](https://docs.ansible.com/ansible/latest/installation_guide/index.html) to install Ansible on your main server.

2. **Verify Ansible Installation:**
   - After installation, check the Ansible version:
     ```bash
     ansible --version
     ```

3. **Test Ansible Connectivity:**
   - Ensure Ansible is functioning correctly:
     ```bash
     ansible localhost -m ping
     ```

### Step 2: Set Up Connectivity with Remote Servers

1. **Check SSH Connectivity:**
   - Verify connectivity between Ansible and remote servers:
     ```bash
     ssh root@remote_server_ip
     ```
   - Enter the password when prompted. Ensure port 22 is open on the remote server.

2. **Configure Passwordless SSH Authentication:**
   - Generate an SSH key on the Ansible server:
     ```bash
     ssh-keygen
     ```
   - Copy the SSH key to the remote server:
     ```bash
     ssh-copy-id <remote_server_ip>
     ```
   - Enter the password when prompted. You should now be able to SSH into the remote server without entering a password. Check using `ssh root@remote_server_ip`.

### Step 3: Configure Ansible

1. **Navigate to Ansible Configuration Files:**
   - Ansible configuration files are located at:
     ```bash
     /etc/ansible/ansible.cfg
     /etc/ansible/hosts
     ```
   - Navigate to the `/etc/ansible` directory and inspect the configuration files.

2. **Generate Example Configuration File:**
   - Initialize a new `ansible.cfg` file with default settings commented out:
     ```bash
     ansible-config init --disabled -t all > ansible.cfg
     ```
   - This will provide multiple options with default examples for reference.

### Step 4: Create and Run Your First Playbook

1. **Create Playbook Directory:**
   - Create a directory to store your playbooks:
     ```bash
     mkdir playbooks
     cd playbooks/
     ```

2. **Create Your First Playbook:**
   - Playbooks are written in YAML format. YAML file starts with `---`. Formatting/indentation matters in YAML files.
You can provide the description using `- name`. 
Begin with a basic playbook:
     ```bash
     vi first_pb.yml
     ```
     ```yaml
     ---
     - name: First Basic PB
       hosts: localhost
       
       tasks:
       - name: Test Connectivity
         ping:
     ```

3. **Run Your Playbook:**
   - Execute the playbook using `ansible-playbook`:
     ```bash
     ansible-playbook first_pb.yml
     ```

4. **Check Playbook Syntax:**
   - Ensure your YAML syntax is correct:
     ```bash
     ansible-playbook --syntax-check first_pb.yml
     ```

### Step 5: Create a Playbook for Installing and Starting a Package

1. **Example Playbook for CentOS (using `yum` module):**
   - Create a playbook (`app_install.yml`) to install and start a package (e.g., nginx):
     ```bash
     vi app_install.yml
     ```
     ```yaml
     ---
     - name: Install and Start the service
       hosts: localhost
     
       tasks:
       - name: Installing nginx 
         yum:
           name: nginx 
           state: present
       - name: Starting the nginx service 
         service:
           name: nginx 
           state: started 
           enabled: true
     ```

### Overview of Ansible Playbook:

```yaml
---
- name: Install and start Nginx
  hosts: webservers  # Assuming 'webservers' is defined in your Ansible inventory 
  become: true  # Tasks should be run with sudo privileges

  tasks:
  - name: Install Nginx 
    yum:  # Using the yum module for package management
      name: nginx  # Name of the package 
      state: present  # Ensure the package is installed

  - name: Start Nginx 
    service:  # Using the service module to manage the service
      name: nginx 
      state: started
      enabled: true  # Ensure Nginx is enabled to start on boot
```

**Index of all Ansible Modules**: [Official Documentation](https://docs.ansible.com/ansible/latest/collections/index_module.html)

### Playbook for Remote Server

To provide inventory of remote servers, navigate to the configuration directory of Ansible.

```bash
cd /etc/ansible
```

When listing files, you will find the `hosts` file in this directory. You can directly add IPs of remote servers under the `Ungrouped hosts` list in this file. Alternatively, create a `Grouped hosts` section inside this `hosts` file to group a set of IPs.

**Adding a remote IP in the Ungrouped hosts list:**

```bash
[root@centos1 ansible]# vi hosts
...
...
# Example of Ungrouped hosts, specify before any group headers:
10.211.55.5
## green.example.com
## blue.example.com
## 192.168.100.1
## 192.168.100.10
...
...
```

After saving the `hosts` file, verify if it's properly inserted using `ansible-inventory --list`.

```bash
[root@centos01 ansible]# ansible-inventory --list
{
    "_meta": {
        "hostvars": {}
    },
    "all": {
        "children": [
            "ungrouped"
        ]
    },
    "ungrouped": {
        "hosts": [
            "10.211.55.5"
        ]
    }
}
[root@centos01 ansible]#
```

Verify if the Ansible server can connect to the remote server using `ansible all -m ping`.

```bash
[root@centos01 ansible]# ansible all -m ping
10.211.55.5 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
[root@centos01 ansible]#
```

To specify remote IPs in a playbook, use `hosts: all` to include all servers listed in the host file. If using grouped hosts, specify the group name like `hosts: webservers`.

**Example:**

```bash
vi first_pb.yml
```
```yaml
---
- name: First Basic PB
  hosts: all
  
  tasks:
  - name: Test Connectivity
    ping:
```

Run the playbook:

```bash
ansible-playbook first_pb.yml
```

### Copying files to Remote Server

```bash
vi 02_copy_files.yml
```
```yaml
---
- name: Copying files to remote
  hosts: all

  tasks:
  - name: Copy files
    copy:
      src: /root/myfile.txt
      dest: /tmp/
```

When you run this playbook, it copies the file to the remote server with the same permissions and file ownership. You can modify these settings by adding `owner`, `group`, and `mode`.

```yaml
---
- name: Copying files to remote
  hosts: all

  tasks:
  - name: Copy files
    copy:
      src: /root/myfile.txt
      dest: /tmp/
      owner: paul
      group: paul
      mode: "0777"
```

### Backup of Files on Remote Server

If you make changes to the file `myfile.txt` on the Ansible server and run the playbook again, it will overwrite the existing file on the remote server. To keep a backup of the old file when copying the updated file to the remote server, use the `backup` option.

```yaml
---
- name: Copying files to remote
  hosts: all

  tasks:
  - name: Copy files
    copy:
      src: /root/myfile.txt
      dest: /tmp/
      owner: paul
      group: paul
      mode: ugo=rw
      backup: true
```

When you run the Ansible playbook, it copies the file to the remote server while creating a backup of the existing file with an updated filename.

Example output on the remote server:
```
- rw-rw-rw-. 1 paul paul 12 Feb 8 17:29 myfile.txt.47862.2024-02-080
-rw-rw-rw-. 1 paul paul 19 Feb 8 17:31 myfile.txt
```

### Ansible File Module: Create, Delete, and Set Permissions on Remote Server

**To Create a File:**

```yaml
---
- name: File Module
  hosts: all

  tasks:
  - name: Creating a file 
    file:
      path: /tmp/newfile.txt
      state: touch 
      owner: paul 
      group: paul
      mode: u=rwx,g=rw,o=r
```

**To Create a Directory:**

```yaml
---
- name: File Module
  hosts: all

  tasks:
  - name: Creating a directory 
    file:
      path: /tmp/myfolder
      state: directory
```

**To Delete a File/Directory:** Use `state: absent` for both file and directory.

```yaml
---
- name: File Module
  hosts: all

  tasks:
  - name: Deleting a file 
    file:
      path: /tmp/newfile.txt
      state: absent

  - name: Deleting a directory 
    file:
      path: /tmp/myfolder
      state: absent
```

### Run Script Remotely

Assume there is a script `test.sh` on the remote server with executable permissions.

```bash
vi test.sh
```

```bash
#!/bin/bash

echo "Hey buddy"
touch /tmp/script/testscriptfile
```

To run this script remotely using Ansible, write a playbook.

```yaml
---
- name: Run a script 
  hosts: all
  
  tasks:
  - name: Run script
    shell: /tmp/script/test.sh
```

When this playbook runs, it executes `test.sh` on the remote server. However, if you want to capture the script's output, such as the `echo` statement, you can redirect the output to a file on the remote server either by modifying `test.sh` or by adjusting the playbook.

Modify the playbook to redirect the output to `test.log`.

```yaml
---
- name: Run a script 
  hosts: all

  tasks:
  - name: Run script 
    shell: /tmp/script/test.sh >> test.log
    args:
      chdir: /tmp/script/
      creates: test.log
```

In this playbook:
- `args` are provided to change the directory (`chdir`) to `/tmp/script/`.
- It creates the `test.log` file if it doesn't exist (`creates`).
- It runs the shell script `/tmp/script/test.sh` and appends (`>>`) the output to `test.log`.

### Adding Cronjob Remotely

Create a playbook to add a cron job:

```yaml
---
- name: Cron Setup
  hosts: all

  tasks:
  - name: Add Cron Job 
    cron:
      name: Run Test Script 
      minute: 30
      hour: 18
      day: 15
      month: "*"
      weekday: "*" 
      user: paul 
      job: /tmp/script/test.sh
```

When executed, this playbook adds a cron job entry on the remote server.

**Disabling the Cronjob**

To disable a cronjob, include `disabled: yes` in the playbook. This comments out the cron job entry on the remote server.

```yaml
---
- name: Cron Setup
  hosts: all

  tasks:
  - name: Add Cron Job 
    cron:
      name: Run Test Script 
      minute: 30
      hour: 18
      day: 15
      month: "*"
      weekday: "*" 
      user: paul 
      job: /tmp/script/test.sh
      disabled: yes
```

**Using Environmental Variables in Cronjob**

To set an environmental variable (`PATH="/tmp/script/"`) in a cronjob, use the following playbook:

```yaml
---
- name: Modify Cron
  hosts: all

  tasks:
  - name: Modify Cron Job
    cron:
      name: PATH
      env: yes 
      user: paul 
      job: /tmp/script/
```

To insert a variable (`VAR`) before or after another variable (`PATH`), adjust `insertbefore` or `insertafter` accordingly.

```yaml
---
- name: Modify Cron
  hosts: all

  tasks:
  - name: Modify Cron Job
    cron:
      name: VAR
      env: yes 
      user: paul 
      job: /tmp/script/
      insertbefore: PATH
```

To remove a variable from the cronjob, use `state: absent`.

```yaml
---
- name: Cron Setup
  hosts: all

  tasks:
  - name: Remove Cron Job
    cron:
      name: Run Test Script
      user: paul
      state: absent
```

### Create User on Remote Server

Create a playbook to manage user creation:

```yaml
---
- name: User Management
  hosts: all

  tasks:
  - name: User Creation
    user:
      name: nick
      comment: New user for QA Team
      home: /home/nick # Optional, by default it creates /home/nick directory, if you want custom home directory, use this.
      shell: /bin/bash # Assign default shell
```

To add the user in one group, you can use `group: QA`. To add the user in multiple groups, you can use `groups: nick,QA,automation`.

```yaml
---
- name: User Management
  hosts: all

  tasks:
  - name: User Creation
    user:
      name: nick
      comment: New user for QA Team
      shell: /bin/bash
      groups: nick,QA,automation
```

To delete a user and optionally remove associated data/home directory, use `state: absent` and `remove: yes`.

```yaml
---
- name: User Management
  hosts: all

  tasks:
  - name: User Deletion
    user:
      name: nick
      comment: New user for QA Team
      shell: /bin/bash
      groups: nick,QA,automation
      state: absent
      remove: yes
```

### Add or Update Password

To set a password for a user, use the `user` module in Ansible.

```yaml
---
- name: Set Password
  hosts: all

  tasks:
  - name: Set password 
    user:
      name: nick
      update_password: always
      password: "{{ 'abcd123' | password_hash('sha512') }}"
```

Use `update_password: oncreate` to update the password only during user creation. For existing users, use `update_password: always`.

You can't just provide the password in simple text format. Passwords must be encrypted using a hash function like `password_hash('sha512')` for security reasons.

### Find and Stop Process

Check if your remote server has the nginx server running:
```
systemctl status nginx
```
If you want to kill the process, you can obtain the PID using `pgrep nginx`. To kill the process, use:
```
pgrep nginx | xargs kill
```
This is a manual process; you can integrate this command into an Ansible playbook to manage the service.

Create a playbook on the Ansible server to terminate a process using the `shell` module and another task to start the service using the `service` module.

```yaml
---
- name: Kill a process 
  hosts: all

  tasks:
  - name: Find a process and kill it 
    ignore_errors: yes
    shell: "pgrep nginx | xargs kill"

  - name: Start the service 
    service:
      name: nginx 
      state: started
```
The `ignore_errors: yes` parameter allows the playbook to continue even if the process is not found.

### Download Files from the Internet on Remote Server

```yaml
---
- name: Download files
  hosts: all

  tasks:
  - name: Download file 
    get_url:
      url: https://www.python.org/ftp/python/3.12.2/Python-3.12.2.tar.xz
      dest: /tmp/script/ 
      owner: paul 
      group: paul 
      mode: '0777'
```
You can specify `owner`, `group`, and `mode` to ensure the downloaded file has appropriate permissions.

### Enable Firewall Service or Port

```yaml
---
- name: Firewall changes
  hosts: all

  tasks:
  - name: Enable a service in firewalld
    firewalld:
      port: 80/tcp  # Specify protocol (tcp/udp) after port number
      permanent: true 
      state: enabled

  - name: Reload the firewalld
    service:
      name: firewalld 
      state: reloaded
```
Use the actual service name instead of `port` if it's a known service. For example: `service: nginx`. To list all existing services you can use `firewall-cmd --get-services`.

`permanent: true` ensures changes persist across reboots.

Also need to restart the serice once you make any changes in firewalld, you can use `service` module for the same.

### Run Playbook Tasks as a SUDO User

On the Ansible server, all playbooks have been created and executed using the **root** user. When a non-root user like **paul** attempts to run the playbook, authentication fails because passwordless authentication was set up only for the **root** user.

To enable **paul** to run playbooks, follow the same steps used for the **root** user. Log into the Ansible server as **paul**, generate keys with `ssh-keygen`, and copy the key to the remote server using `ssh-copy-id paul@<remote_server_ip>`. Enter the password when prompted. This setup allows **paul** to execute playbooks on the remote server.

However, executing playbooks that require sudo privileges (e.g., tasks to enable/disable firewalld services) fails for **paul**. In such cases, add `become: true` at the task or playbook level to escalate privileges, essentially **becoming root** while running the playbook.

```yaml
---
- name: Firewall changes
  hosts: all
  become: true

  tasks:
  - name: Enable a service in firewalld
    firewalld:
      port: 80/tcp  # Specify protocol (tcp/udp) after port number
      permanent: true 
      state: enabled

  - name: Reload the firewalld
    service:
      name: firewalld 
      state: reloaded
```
To run a playbook that requires **becoming root**, use `--ask-become-pass` to provide the sudo password. Otherwise, you'll encounter an error: `FAILED! => {"msg": "Missing sudo password"}`.

```
[paul@centos01 playbooks]$ ansible-playbook --ask-become-pass 12_firewalld.yml
BECOME password:
```

### Ad hoc tasks with Ansible

There is no need to write playbooks for simple tasks; you can directly pass the Ansible modules using the command line.

Syntax:
```ssh
ansible <host-pattern> -m <module> -a "<module arguments>" -u <username> -b
```

```ssh
ansible myserver -m command -a "df -h"
```
- myserver: Provide your host details here, `localhost`, `IP address`, or `hosts`.
- `-m`: Module
- `-a`: Arguments


Example: Ping the host
```
[paul@centos01 ~]$ ansible localhost -m ping
localhost | SUCCESS => {
"changed": false,
"ping": "pong"
[paul@centos01 ~1$ ansible all -m ping
10.211.55.9 SUCCESS => {
"ansible_facts": {
"discovered_interpreter_python": "/usr/bin/python3"
},
"changed": false,
"ping": "pong"
}
[paul@centos01 ~1$ ansible 10.211.55.9 -m ping
10.211.55.9 | SUCCESS => {
"ansible facts": {
"discovered_interpreter_python": "/usr/bin/python3"
},
"changed": false,
"ping": "pong"
}
[paul@centos01 ~1$
```
Example: Copy files from Ansible server to remote server
```
[paul@centos01 ~1$ ansible 10.211.55.9 -m copy -a "src=/home/paul/paulfile dest=/tmp/script/"
```
You are running this as a user **paul**. What if the remote server directory has permissions to write only for the root user? You can use `-b` to **become root** and `--ask-become-pass` to enter the root password.
```
[paul@centos01 ~1$ ansible 10.211.55.9 -m copy -a "src=/home/paul/paulfile dest=/tmp/script/" -b --ask-become-pass
BECOME password:
```
To change permissions, you can use `mode=0777`.
```
[paul@centos01 ~]$ ansible 10.211.55.9 -m copy -a "src=/home/paul/paulfile dest=/tmp/script/ mode=0777" -b --ask-become-pass
```
Example: Restart service. To restart the service, you need to be the root user.
```
[root@centos01 ~]# ansible all -m service -a "name=nginx state=reloaded"
```
Example: To run a script
```
[root@centos01 ~]# ansible all -m shell -a "/tmp/script/test.sh"
10.211.55.9 CHANGED rc=0 >>
Hey buddy
[root@centos01 ~]#
```
Example: To execute commands
```
[root@centos01 ~]# ansible all -m command -a "free -h"
[root@centos01 ~]# ansible all -m command -a "df -h"
```
Example: To install any service
```
[root@centos01 ~]# ansible all -m yum -a "name=vim state=present"
```

### Tags in Ansible

In a single playbook, you can have multiple tasks, and when you run it, it will execute all the tasks. If you want to execute only some specific tasks from the playbook, you can assign tags to every task and run that specific task using tags. Tags should align at the same level as the task name `- name`.

```yaml
---
- name: Install and Start the service 
  hosts: all

  tasks:
  - name: Installing nginx 
    yum:
      name: nginx 
      state: present 
    tags: i-nginx

  - name: Starting the nginx service 
    service:
      name: nginx 
      state: started 
      enabled: true 
    tags: ss-nginx
```
To list all tags in a playbook, use `--list-tags`.

```
[root@centos01 playbooks]# ansible-playbook 01_app_install.yml --list-tags

playbook: 01_app_install.yml

    play #1 (all): Install and Start the service TAGS: []
        TASK TAGS: [i-nginx, ss-nginx]
[root@centos01 playbooks]#
```
To pass specific tags, use `-t` followed by the tag name.

```
[root@centos01 playbooks]# ansible-playbook 01_app_install.yml -t i-nginx
```
You can also use `--skip-tags` to skip tasks associated with that tag.

```
[root@centos01 playbooks]# ansible-playbook 01_app_install.yml --skip-tags ss-nginx
```
### Variables in Ansible

You need to provide a variable name using `vars` and **variable name and value** at the start of the playbook. And referenced using `"{{ variable_name }}"`.

```yaml
---
- name: Install and Start the service 
  hosts: all
  vars: 
    - app: nginx

  tasks:
  - name: Installing nginx 
    yum:
      name: "{{ app }}" 
      state: present 
    tags: i-nginx

  - name: Starting the nginx service 
    service:
      name: "{{ app }}"  
      state: started 
      enabled: true 
    tags: ss-nginx
```

Using variables allows you to create a single playbook to perform tasks for multiple applications or purposes just by changing variables. You can modify the `- app` value to any application you want without needing to modify the entire playbook.

You can also add variables in the Ansible Inventory for server IPs. Edit the Ansible Inventory/Host file. (`/etc/ansible/hosts`).

```bash
[root@centos1 ansible]# vi hosts
...
...
# Example of Ungrouped hosts, specify before any group headers:
serverA ansible_host=10.211.55.9
## green.example.com
## blue.example.com
## 192.168.100.1
## 192.168.100.10
...
...
```
Now IP **10.211.55.9** can be recognized as **serverA**. Try to ping serverA.

```
[root@centos01 ansible]# ansible serverA -m ping
serverA | SUCCESS => {
      "ansible facts": {
          "discovered_interpreter_python": "/usr/bin/python3"
      },
      "changed": false,
      "ping": "pong"
}
[root@centos01 ansible]#
```
### Handlers in Ansible

When you run a playbook to open port 80 and reload the firewall service, it just performs the tasks one after another without checking if the first task has completed, so that it can execute the second task. You can use handlers to manage these types of scenarios.

You can notify the second task, which is defined under a handler, by using its name in the `notify` directive of the first task. This notification mechanism ensures that the handler task only runs when changes are made by the first task. Handlers execute after all tasks in a play have been processed.

```yaml
---
- name: Firewall changes
  hosts: all

  tasks:
  - name: Enable a service in firewalld
    firewalld:
      port: 80/tcp  # Specify protocol (tcp/udp) after port number
      permanent: true 
      state: enabled
    notify:
      - Reload the firewalld  # Use handler name

  handlers:
  - name: Reload the firewalld
    service:
      name: firewalld 
      state: reloaded
```

### Conditions in Ansible

You can incorporate conditions within a single playbook to execute different tasks based on specific conditions.

For example, in the following playbook snippet, you can choose different modules (`yum` or `apt`) based on the operating system being **RedHat** or **Ubuntu** respectively.

```yaml
---
- name: Installing Httpd
  hosts: all

  tasks:
  - name: Install httpd on Redhat 
    yum:
      name: httpd
      state: present 
    when: ansible_os_family == "RedHat"

  - name: Install apache2 on Ubuntu 
    apt:
      name: apache2
      state: present 
    when: ansible_os_family == "Ubuntu"
```

Here, built-in Ansible variables like `ansible_os_family` are used along with specific values to define the conditions. To gather information about these built-in variables, you can use the `ansible <localhost> -m setup` command.

If you need to obtain details about built-in variables on a remote server, you can utilize the `setup` module. This command will provide you with the names and values of built-in variables.

```
[root@centos01 playbooks]# ansible 10.211.55.9 -m setup
```

### Loops in Ansible

When you need to repeat the same task multiple times, instead of duplicating tasks, you can utilize loops.

For instance, if you want to create multiple users on a remote server using Ansible, you can employ the `loop` feature. Provide a list of user names, and within the `name:` field, use `"{{ item }}"`. This setup iterates through each item in the loop and performs the specified action.

```yaml
---
- name: User Management
  hosts: all

  tasks:
  - name: User Creation 
    user:
      name: "{{ item }}"
      comment: new user adding for QA Team
      shell: /bin/bash
    loop:
      - Raju
      - Sham
      - Baburao
```

Another example involves performing operations on multiple files across remote servers. You can define multiple values within `vars` using square brackets `[ ]`, and specify `"{{ item }}"` in place of the variable name. Use `with_items:` to iterate over the list of items.

```yaml
---
- name: Install and Start the service 
  hosts: all 
  vars:
    - apps: [yum, httpd, vim, telnet] 

  tasks:
  - name: Installing apps 
    yum:
      name: "{{ item }}" 
      state: present 
    tags: i-nginx
    with_items: "{{ apps }}"
``` 

### Ansible Roles

An Ansible role is a structured way of grouping together various functionalities, making it easier to reuse and share common setup tasks.

You can use different roles for different categories of tasks and call a particular role when you need to execute tasks specific to that category.

**Example Use Case:**
- Install Apache HTTPD webserver on a remote server
- Place a custom HTML file to use
- Start the service
- Enable the service in the firewall
- Reload the firewall

First, on the Ansible server, navigate to the `/etc/ansible/roles` directory and check for available roles.

```bash
[root@centos01 ansible]# pwd
/etc/ansible
[root@centos01 ansible]# ls 
ansible.cfg hosts playbooks roles
[root@centos01 ansible]# cd roles
[root@centos01 roles]# ls
[root@centos01 roles]#
```

By default, the roles directory will be empty. When you create a new role, a new directory is created under roles.

To create a custom role in Ansible, use the following syntax:

```bash
ansible-galaxy init role_name
```

```bash
[root@centos01 roles]# ansible-galaxy init httpd_setup
- Role httpd_setup was created successfully
[root@centos01 roles]#
```

Check the files and directories created in the new role.

```bash
[root@centos01 roles]# ls
httpd_setup
[root@centos01 roles]# cd httpd_setup/
[root@centos01 httpd_setup]# ls -ltr
drwxr-xr-x. 2 root root    6 Feb 11 15:48 templates
drwxr-xr-x. 2 root root   22 Feb 11 15:48 tasks
-rw-r--r--. 1 root root 1328 Feb 11 15:48 README.md
drwxr-xr-x. 2 root root   22 Feb 11 15:48 meta
drwxr-xr-x. 2 root root   22 Feb 11 15:48 handlers
drwxr-xr-x. 2 root root    6 Feb 11 15:48 files
drwxr-xr-x. 2 root root   22 Feb 11 15:48 defaults
drwxr-xr-x. 2 root root   22 Feb 11 15:48 vars
drwxr-xr-x. 2 root root   39 Feb 11 15:48 tests
[root@centos01 httpd_setup]# cd tasks/
[root@centos01 tasks]# ls
main.yml
[root@centos01 tasks]#
```

All the components used in a playbook, such as handlers, tasks, and vars, are pre-created as directories with a `main.yml` file inside. You can edit this YAML file to define tasks.

According to the example use case, the first task is to **install Apache HTTPD webserver on a remote server**. The second task is to **place a custom HTML file on the remote server**. The third task is to **start the service**.

Keep your custom HTML file `index.html` in the **files** directory.

```bash
[root@centos01 httpd setup]# cd files/
[root@centos01 files]# cp /tmp/index.html .
[root@centos01 files]# ls
index.html
[root@centos01 files]# pwd
/etc/ansible/roles/httpd_setup/files
[root@centos01 files]#
```

You can place all required files in the **files** directory inside the **httpd_setup/** role. By doing this, you can directly refer to file names in `main.yml` without providing absolute paths.

Navigate to the **tasks** directory inside **httpd_setup/** role and edit the `main.yml` file. There's no need to explicitly mention tasks in the YAML file, as you're editing the YAML file within the **tasks** directory.

```yaml
---
# tasks file for httpd_setup
- name: Install Httpd
  yum:
    name: "{{ httpd_package_name }}"
    state: present
  become: true

- name: Place custom HTML File
  copy:
    src: index.html
    dest: /var/www/html/index.html
  become: true

- name: Start the service 
  service:
    name: "{{ httpd_package_name }}"
    state: started 
    enabled: yes 
  become: true
```

You're directly specifying `src` as `index.html` since it's already in the **files** directory. Also, `"{{ httpd_package_name }}"` is used as a variable; you can set its value in the `main.yml` file located in the **vars** directory.

```bash
[root@centos01 httpd_setup]# ls
defaults files handlers meta README.md tasks templates tests vars
[root@centos01 httpd_setup]# cd vars/
[root@centos01 vars]# ls
main.yml
[root@centos01 vars]# vi main.yml
```

```yaml
---
# vars file for httpd_setup

httpd_package_name: httpd
dest_html_file_path: /var/www/html/index.html
```

Now, as per the example use case, two tasks are pending: **Enable service in firewall** and **Reload the firewall**. Instead of adding those tasks in the same role, you can create a new role since these are different categories of tasks.

Create a new role, navigate to the **tasks** directory of the new role, and edit `main.yml`.

```bash
[root@centos01 roles]# ansible-galaxy init fwd_service
- Role fwd_service was created successfully
[root@centos01 roles]# ls
fwd_service httpd_setup
[root@centos01 roles]# cd fwd_service/
[root@centos01 fwd_service]# ls
defaults files handlers meta README.md tasks templates tests vars
[root@centos01 fwd_service]# cd tasks/
[root@centos01 tasks]# ls
main.yml
[root@centos01 tasks]# vi main.yml
```

```yaml
---
# tasks file for fwd service
- name: Enable a service in firewalld
  firewalld:
    service: "{{ service_name }}"
    state: enabled 
    permanent: true
  become: true
  when: ansible_os_family == "RedHat"
```

You're using `"{{ service_name }}"` as a variable; you can set its value in the `main.yml` file located in the **vars** directory.

```bash
[root@centos01 tasks]# cd ../vars/
[root@centos01 vars]# vi main.yml
```

```yaml
---
# vars file for fwd_service
service_name: http
```

Handlers execute last, and you need to specify the exact handler name in tasks to execute them.

Go back to `main.yml` in the **tasks** directory and add `notify` with the handler name to execute the handler.

```bash
[root@centos01 handlers]# cd ../tasks/
[root@centos01 tasks]# vi main.yml
```

```yaml
---
# tasks file for fwd service
- name: Enable a service in firewalld
  firewalld:
    service: "{{ service_name }}"
    state: enabled 
    permanent: true
  become: true
  when: ansible_os_family == "RedHat"
  notify: Reload Firewall
```

Now everything is organized properly. You can create a simple playbook and use these roles to perform the example use case tasks.

```bash
[root@centos01 ~]# cd /etc/ansible/
[root@centos01 ansible]# ls
ansible.cfg hosts playbooks roles
[root@centos01 ansible]# cd playbooks/
[root@centos01 playbooks]# vi roles_demo.yml
```

Specify the role names in the playbook; Ansible fetches and executes them.

```yaml
---
- name: Install httpd and Firewall changes
  hosts: all
  roles:
    - httpd_setup
    - fwd_service
```

Run the playbook:

```bash
[root@centos01 playbooks]# ansible-playbook roles_demo.yml

PLAY [Install httpd and Firewall changes] *********************************

TASK [Gathering Facts] ****************************************************
ok: [serverA]

TASK [httpd_setup : Install Httpd] ****************************************
changed: [serverA]

TASK [httpd_setup : Place custom HTML File] ********************************
changed: [serverA]

TASK [httpd_setup : Start the service] *************************************
changed: [serverA]

TASK [fwd_service : Enable a service in firewalld] *************************
changed: [serverA]

RUNNING HANDLER [fwd_service : Reload Firewall] ***************************
changed: [serverA]

PLAY RECAP ****************************************************************
serverA               : ok=6    changed=5   unreachable=0   failed=0   skipped=0   rescued=0   ignored=0
[root@centos01 playbooks]#
```

### Ansible Galaxy

Ansible Galaxy refers to the [Galaxy website](https://galaxy.ansible.com/ui/), a free site for finding, downloading, and sharing community-developed roles.

You can install and use roles created by other contributors using:

```bash
ansible-galaxy role install <role-name>
```

You can find all available roles on the [Official Ansible Galaxy website](https://galaxy.ansible.com/ui/standalone/roles/).

### Red Hat Ansible Tower

Red Hat Ansible Tower provides:

- A web-based UI
- Centralized management
- Job scheduling
- Reporting and analytics
- Integration with ticketing systems