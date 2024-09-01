# Jenkins

- Jenkins is an open-source automation server.
- It automates various parts of software development, including:
  - Building
  - Testing
  - Deployment

**Features of Jenkins:**
- Continuous Integration and Continuous Delivery
- Easy installation
- Easy configuration
- Plugins
- Extensible
- Distributed

### What is CI/CD?

CI/CD is a method of frequently delivering apps to customers by introducing automation into the stages of app development.

```
CONTINUOUS INTEGRATION -->         CONTINUOUS DELIVERY         --> CONTINUOUS DEPLOYMENT
Build -> Test -> Merge --> Automatically release to repository --> Automatically deploy to production
```

### Lab Setup For Jenkins

You can either use VirtualBox or launch an EC2 instance in AWS for Jenkins installation. We are using a CentOS instance for Jenkins installation. By default, Jenkins listens on port 8080. You need to add an inbound rule for port 8080 in the Security Group.

### Jenkins Installation

You can refer to the [Official Documentation](https://www.jenkins.io/doc/book/installing/linux/#red-hat-centos) for more details.

Log in to the CentOS server and run the following commands to install and start Jenkins:

```bash
$ sudo yum upgrade
$ sudo wget -O /etc/yum.repos.d/jenkins.repo \
    https://pkg.jenkins.io/redhat/jenkins.repo
$ sudo rpm --import https://pkg.jenkins.io/redhat/jenkins.io-2023.key
$ sudo yum install fontconfig java-17-openjdk
$ sudo yum install jenkins
$ sudo systemctl enable jenkins
$ sudo systemctl start jenkins
$ sudo systemctl status jenkins
Loaded: loaded (/lib/systemd/system/jenkins.service; enabled; vendor preset: enabled)
Active: active (running) since Tue 2023-06-22 16:19:01 +03; 4min 57s ago
```

- Once Jenkins is installed, get the IP of the CentOS server using the `ifconfig` command.
- Open a browser, type the IP with port 8080, and press Enter. For example: `10.211.55.7:8080`.

<blockquote>
To access it from a browser, you need to enable it on the firewall at the OS level, [only if it is not accessible]:

- `sudo firewall-cmd --permanent --zone=public --add-port=8080/tcp`
- `sudo firewall-cmd --reload`
</blockquote>

- You will see the **Getting Started** page. Here, you will find the path for the Jenkins Admin password on the server. Copy the path, return to the CentOS server, and retrieve the password:

```bash
$ sudo cat /var/lib/jenkins/secrets/initialAdminPassword
16695969633d42f49ace997305a9edb9
```

- Copy the password from the server, paste it into the browser webpage, and click on `Continue`.
- The next page will be **Customize Jenkins**. Click on `Install suggested plugins`. (If the installation encounters any errors, simply restart the CentOS server and try again).
- Once all plugins are installed, you will see the **Create First Admin User** page. Enter the Username, Password, and required details, then click on `Save and Continue`.
- On the **Instance Configuration** page, provide the `Jenkins URL`. We will use the default value "http://10.211.55.7:8080/". Click on `Save and Finish`.
- Finally, click on `Start using Jenkins` to enter the **Jenkins Dashboard**.

### Overview of Jenkins

- Every automation task in Jenkins is called a Job.
- In the dashboard, click on `New Item` to create a job.
- Click on `Manage Jenkins` to access options for configuring `Tools`, `Plugins`, `Users`, `Logs`, and more.

### Creating Your First Jenkins Job

1. Click on **New Item**. Provide an **item name**, choose **Freestyle project**, and click on **OK**.
2. On the next page, you can provide a description (optional). Scroll down to **Build Steps**.
3. Click on the **Add build step** dropdown to see multiple options. Choose **Execute Shell**.
4. Type a basic command to execute on the shell. For example: `echo "HELLO BUDDY!!"`. Click on **Save**.
5. Now, when you click on this Job and open it, you will see the **Build Now** option. Each time you click on **Build Now**, it executes the command and saves it in the **Build History**.
6. Clicking on any entry in **Build History** opens the job details, including Success/Failure status. If you click on **Console Output**, it will show the output when the job was run.

All the jobs we run in Jenkins are performed on the CentOS server itself.

1. Go back to the Job, click on **Configure** (next to **Build Now**).
2. On the next page, scroll down to **Build Steps** again.
3. Edit the execute command option to `echo "Hello" > /tmp/first_job.txt`. Click on **Save**.
4. Click on **Build Now**.
5. Now, on the CentOS server, navigate to the **/tmp/** directory and check for the **first_job.txt** file. The job should create a new file in the provided path on the CentOS server.

### Run Bash Script

Now we will create a basic script on the CentOS server in the `/tmp/` directory and run it using Jenkins.

1. Create and edit the script file:
   ```bash
   vi /tmp/basic.sh
   ```

2. Add the following content to the file:
   ```bash
   #!/bin/bash
   echo "This is from script under /tmp"
   ```

3. Save and exit the editor.

4. In Jenkins, go back to the job configuration, click on **Configure**, and scroll down to **Build Steps**.

5. Edit the execute command option to `bash /tmp/basic.sh`. Click on **Save**.

6. Click on **Build Now**.

7. In **Build History**, you can see the job details. Click on that and check the **Console Output**. You should see the echo statement from the script, which indicates that the script has been executed successfully using Jenkins.

### Bash Script With Parameters

1. To use parameters in the job, go back to the job configuration, click on **Configure**, and in the **General** section, check the **This job is parameterized** option.

2. Once you check this option, you will get an **Add Parameter** dropdown. Choose **String Parameter**.

3. Enter `FNAME` in **Name**, and `Paul` in **Default Value**. You can add another parameter by clicking **Add Parameter** again. Enter `LNAME` as the name and `Philip` as the default value.

4. Scroll down to the **Build Steps** section, and edit the **Execute shell** option to `echo $FNAME $LNAME`. Click on **Save**.

5. Click on **Build with Parameters**. You can either edit the values of `FNAME` and `LNAME` or build with the default values.

6. In **Build History**, you can see the job details. Click on that and check the **Console Output**. You should see the output displaying names from the variables.

Add more parameters:

1. You can also use the same job and add a parameter by choosing **Choice Parameter**.

2. Enter `GENDER` in **Name**, and in **Choices**, provide `Male`, `Female`, and `Others` choices, one per line.

3. In the **Execute shell** option, add `echo $GENDER`. Click on **Save**.

4. When you click on **Build with Parameters**, you will get an option to choose the gender value, and it will be set according to your choice.

For flags, you can use **Boolean Parameters**. Similarly, you can use other parameters based on your use case.

### Scheduling Jenkins Jobs

Instead of clicking on **Build Now** every time you want to run the job, you can schedule the jobs to execute at specific times like cron jobs. Alternatively, you can set it to trigger every time changes are made in Git.

1. In the Jenkins dashboard, click on **New Item**. Provide an **Item name**, choose **Freestyle project**, and click on **OK**.

2. On the next page, you can provide a description (optional). Scroll down to **Build Steps**.

3. Click on the **Add build step** dropdown to see multiple options. Choose **Execute Shell**.

4. Enter a basic command to execute on the shell, such as `echo "HELLO"`. Click on **Save**.

5. Scroll up to **Source Code Management**, and you will find **Build Triggers**. Check **Build Periodically**.

6. It will provide a **Schedule** option. Enter the cron expression when you want to trigger. For this example, use `*/5 * * * *`. Click on **Save**.

Now, there is no need to click on **Build Now**; the job will trigger automatically every 5 minutes. You can use this option to schedule any script, maintenance, restart, etc.

### Working With GitHub

Whenever a developer makes changes and pushes the code to GitHub, you can set up a trigger to pull that code and execute it automatically using Jenkins. Jenkins will pull the code from GitHub and execute it. For this, Jenkins needs the Git plugin installed.

1. In the Jenkins dashboard, click on **Manage Jenkins**, then click on **Plugins**. Check the **Installed Plugins** section for Git-related plugins. If you don't find them, click on **Available Plugins** and search for `GitHub plugin` to install it.

2. Click on **New Item**. Provide an **Item name**, choose **Freestyle project**, and click on **OK**.

3. Scroll down to **Source Code Management**, choose **Git**, and provide the **Repository URL**. (We are using a public GitHub repository in this example. If you use a private GitHub repository, you will need to provide **Credentials** as well.) You can leave **Branches to Build** blank if you only have one branch. Click on **Save**.

4. Click on **Build Now** and check the output in **Build History -> Console Output**.

You will see in the output that Jenkins cloned the GitHub repository into the server and completed the job. To see where exactly it cloned the repository on the server, go to the job and click on **Workspace**. You will see the path where the GitHub repo is cloned. You can also find the path in the **Console Output** of **Build History**.

To check this on the server, log in to the CentOS server and navigate to the path where Jenkins jobs are stored:

```bash
[paul@centos01 ~]$ cd /var/lib/jenkins/
config.xml                                                  nodeMonitors.xml
hudson.model.UpdateCenter.xml                               nodes
hudson.plugins.emailext.ExtendedEmailPublisher.xml          plugins
hudson.plugins.git.GitTool.xml                              secret.key
identity.key.enc                                            secret.key.not-so-secret
jenkins.install.UpgradeWizard.state                         secrets
jenkins.model.JenkinsLocationConfiguration.xml              updates
jenkins.telemetry.Correlator.xml                            userContent
jobs                                                        users
logs                                                        workspace
[paul@centos01 jenkins]$ cd workspace/
[paul@centos01 workspace]$ ls
first-job      cron-job      git-demo
[paul@centos01 workspace]$ pwd
/var/lib/jenkins/workspace
[paul@centos01 workspace]$ cd git-demo
[paul@centos01 git-demo]$ ls -la
drwxr-xr-x. 3 jenkins jenkins 33  Apr 8 09:11 .
drwxr-xr-x. 5 jenkins jenkins 55  Apr 8 09:11 ..
drwxr-xr-x. 8 jenkins jenkins 162 Apr 8 09:11 .git
-rw-r--r--.   1 jenkins jenkins 27  Apr 8 09:11 test.py
```

Now you know where the code is stored on the server. To run the Python file, you need to have Python installed on the server. You can check by typing `python` or `python3`.

```bash
[paul@centos01 git-demo]$ python
Python 3.9.18 (main, Jan 24 2024, 00:00:00)
IGCC 11.4.1 20231218 (Red Hat 11.4.1-3) on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> quit()
[paul@centos01 git-demo]$
```

In the Jenkins dashboard, go to the job, scroll down to **Build Steps**, and edit the **Execute shell** option to `python3 test.py`. Click on **Save** and then click on **Build Now**. This should execute the code we pulled from the repository. Jenkins typically searches for the file inside the job's workspace directory, so there is no need to provide the absolute path of the file.

### GitHub Auto Build

To automate the build process so that Jenkins triggers a build whenever code is pushed to GitHub, use **Build Triggers**.

1. Use the job configured in the previous example. Click on the job, scroll down to **Build Triggers**.

2. In the previous example, we used **Build Periodically** and set cron expressions for the trigger. For GitHub to trigger, select **Poll SCM**.

3. It will provide a **Schedule** option. Enter the cron expression for the polling frequency. For this example, use `* * * * *` to check for changes every minute. Click on **Save**.

4. This setup will check for changes every minute. If it detects a change in GitHub, it will automatically clone the repository and run the job; otherwise, it will ignore it.

To test this, update your code and commit to GitHub, then wait for a minute. With the Jenkins trigger set to check for changes every minute, Jenkins will automatically clone the repository and run the code if it finds a new commit.

### Email Notify On Job Failure

Now that we have set trigger to automatically pull code from GitHub and run the code. We will not be checking the console output everytime for Job success state. If the Job fails, we don't get notified untill we see the Console Output. In such cases, we can set Email notification for the Jobs.

### Email Notification on Job Failure

Now that we have set up a trigger to automatically pull code from GitHub and run it, we will not be checking the console output every time for job success status. If the job fails, we don't get notified until we check the console output. In such cases, we can set up email notifications for the jobs.

For this, first we need to set up email notifications. We are using Gmail SMTP in this example.

Google Account:
- You can create a demo Google account for this example, or you can use your existing Gmail account. The account should have MFA enabled.
- Log in to your account. Go to **Manage your Google Account**.
- Click on **Security**. Verify that **2-step verification** is enabled.
- In the search bar, search for **App Passwords**. Select it. Provide an **App name** and click on **Create**.
- You will get an *App password*. Copy and save it in a notepad.

Jenkins Dashboard:
- Click on **Manage Jenkins**. In **System Configuration**, click on **System**.
- Scroll down to the bottom to the **E-mail Notification** option. Provide your mail server details in **SMTP server**. Since we are using Google SMTP, our mail server name is `smtp.gmail.com`.
- In **Default user e-mail suffix**, you can provide `@jenkinstest.com` (just for demo purposes).
- Click on the **Advanced** dropdown. Enable the **Use SMTP Authentication** option. It will ask for **Username** and **Password**. The Username should be your Gmail ID and the Password will be your *App Password* that you saved in the notepad.
- Check the **Use SSL** option.
- Provide **SMTP Port** as `465` (It should pick the port by default; otherwise, you can provide it manually).
- Check the option **Test configuration by sending test e-mail** for testing purposes. Provide the recipient's email in **Test e-mail recipient**. Click on **Test Configuration**.
- Check your Gmail Inbox for the test email.
- Once you receive confirmation that the email configuration is working, click on **Save**.

Now integrate the email notification with the Jenkins job. We are using the `git-demo` job for email notification setup.
- Click on the job. Click on **Configure**.
- Scroll down to **Post-build Actions**. Click on the **Add post-build actions** dropdown.
- Choose **E-mail notification**. Provide the recipient's email in **Recipients**.
- Check the option **Send e-mail for every unstable build**.
- Click on **Save**.

Now, whenever a developer commits code to GitHub that causes an error, Jenkins will trigger, clone the code, and try to run it. If it encounters an error, it means the job fails. At this point, an email is sent to the designated recipients notifying them that the job has failed. The developer can then fix the issue and commit the code again to GitHub. Jenkins will trigger, clone the code, and run it again. If the job is successful, a success notification will also be sent to the recipients, notifying them that the job was successful.

### Jenkins Job for Remote Server

Using the Jenkins server, we will perform some actions on a remote server. For this, you need two servers: one CentOS server (centos1) where Jenkins is installed, and another CentOS server (centos2) where you want to perform some actions.

1. First, we need to establish access between centos1 and centos2 servers:
- Log in to the centos2 server and get the IP using the `ifconfig` command. (Example: centos2: 10.211.55.11)
- To create a passwordless connection between the two servers, log in to the centos1 server and generate a keypair using `ssh-keygen`.

```bash
[paul@centos01 ~]$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/paul/.ssh/id_rsa) :
Enter passphrase (empty for no passphrase) :
Enter same passphrase again:
Your identification has been saved in /home/paul/.ssh/id_rsa
Your public key has been saved in /home/paul/.ssh/id_rsa.pub
The key fingerprint is:
SHA256: TMdfHw0u4BdQAZApYPofSs105qft3tZcxwz0Z4eTlHk paul@centos01
```

- Then, copy the key to the centos2 server using `ssh-copy-id`. It will prompt for the password of the centos2 server once; enter the password.

```bash
[paul@centos01 ~]$ ssh-copy-id paul@10.211.55.11
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any 
that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now i
t is to install the new keys
paul@10.211.55.11's password:

Number of keys added: 1

Now try logging into the machine with: "ssh 'paul@10.211.55.11'"
and check to make sure that only the key(s) you wanted were added.
```

- Once this is done, you can directly SSH into the centos2 server without a password.

```bash
[paul@centos01 ~]$ ssh paul@10.211.55.11
Activate the web console with: systemctl enable --now cockpit.socket

Last login: Mon Apr 8 07:14:04 2024 from 10.211.55.7
[paul@centos02 ~]$
```

2. Next step, in Jenkins Dashboard:
- Click on **Manage Jenkins**. Click on **Plugins**. Click on **Available Plugins**.
- Search and install the **Publish over SSH** plugin. (You can use it right away or after a restart.)
- Once installed, click on **Manage Jenkins** again. Click on **System**. Scroll down to **Publish over SSH** option.
- You need to provide the **Path to key** or **Key** (Private Key) itself of the Jenkins server to establish a connection to the centos2 server.

Log in to the centos1 server, where Jenkins is installed, and get the private key you generated.
```bash
[paul@centos01 ~]$ pwd
/home/paul
[paul@centos01 ~]$ cd .ssh/
[paul@centos01 .ssh]$ ls
id_rsa id_rsa.pub known_hosts known_hosts.old
[paul@centos01 .ssh]$ cat id_rsa
```

- Copy the key from the beginning `---BEGIN OPENSSH PRIVATE KEY---` to the last line `---END OPENSSH PRIVATE KEY---`. Paste it in the Jenkins **Key** option in **Publish over SSH**.
- Next, in **SSH Servers**, click on the **Add** button. (Add the remote server details you want to connect to.)
- Provide a **Name** for the server for your reference (e.g., `centos2`). **Hostname** can be the server IP `10.211.55.11`. Provide the **Username** of the remote server (in this example, it's `paul`). [Optional] **Remote Directory** is the default directory where you want to perform actions.
- Click on **Save**.

3. Now, create a new job to perform actions on the remote server.
- In Jenkins, click on **New Item**. Enter an item name: `remote-job`. Choose **Freestyle project**. Click on **OK**.
- Scroll down to **Build Steps**. Click on **Add build step** dropdown. Choose **Send files or execute commands over SSH**.
- It will provide the **SSH Publishers** details. You can choose the `centos2` server details you provided in the previous steps.
- In the same option, scroll down to **Transfers**. Provide the **Source Path** of the file you want to transfer. By default, it checks the file in `/var/lib/jenkins/workspace/remote-job/` of the centos1 server.
- You can create a directory named `files` in this path and keep your files there. In this example, we are keeping `basic.sh` in this folder.
- So in **Transfers**, in **Source Path**, you can provide `files/basic.sh`. In **Exec Command**, you can give `bash /home/paul/files/basic.sh`.
- Click on **Save**.

This should copy the `file/basic.sh` from centos1 to the centos2 server. Since we have not provided a **Remote Directory** path in **Transfers**, it will copy the `files` folder and its files to the user home directory of centos2. Then it executes the script with the commands provided in the **Exec Command** option.

#### Transfer Multiple Files and Exclude Files

- Use the same steps; just in **Transfers**, in **Source Path**, you can provide `files/*`. Now all files inside the `files` directory will be transferred to the remote server.
- In the same option, if you scroll down, you will find the **Advanced** option. You can choose **Exclude files** and enter the files you want to exclude during the transfer. In this example, you can provide `files/file1`.
- Now all files in the `files` directory will be copied to the remote server, excluding `file1`.

> Note: You can use coma separared values to add multiple files.

#### Quick Overview of Ansible Setup

In the `centos1` server, both Jenkins and Ansible are installed. We have some simple Ansible playbooks ready for demo purposes. To run a playbook, we use `ansible-playbook first_pb.yml`. This playbook contains commands to ping the localhost. All the playbooks are kept in the `/etc/ansible/playbooks` directory.

### Jenkins Job with Ansible

To configure Ansible with Jenkins, we need to install plugins in Jenkins.

- Log in to the Jenkins Dashboard. Click on **Manage Jenkins**. Click on **Plugins**. Click on **Available Plugins**.
- Search and install the **Ansible** plugin. (You can use it right away or after a restart.)
- Click on **New Item**. Enter an item name: `ansible-job`. Choose **Freestyle project**. Click on **OK**.
- Scroll down to **Build Steps**. Click on the **Add build step** dropdown. Choose **Invoke Ansible Playbook**.
- Provide the **Playbook path**: `/etc/ansible/playbooks/first_pb.yml`. Click on **Save**.
- Click on **Build Now**.

Now try to include the **remote host IP** in the playbook and invoke it using Jenkins. You may encounter an error because Jenkins invokes the playbook using the `jenkins` user. Since Jenkins is installed on the `centos1` server, it will already have a **jenkins** user. However, this user does not exist on the `centos2` server (the remote server) where we are trying to run the playbook. Thus, you need to create the `jenkins` user on the `centos2` server and provide root access. (In RedHat/CentOS, you can add the user to the `wheel` group for root access.)

```bash
[paul@centos02 ~]$ sudo useradd jenkins
[paul@centos02 ~]$ sudo passwd jenkins
Changing password for user jenkins.
New password:
BAD PASSWORD: The password is shorter than 8 characters
Retype new password:
passwd: all authentication tokens updated successfully.
[paul@centos02 ~]$ id jenkins
uid=1001(jenkins) gid=1001(jenkins) groups=1001(jenkins)
[paul@centos02 ~]$ sudo usermod -aG wheel jenkins
[paul@centos02 ~]$ id jenkins
uid=1001(jenkins) gid=1001(jenkins) groups=1001(jenkins),10(wheel)
```

On the `centos1` server, we already have the `jenkins` user, but by default, it does not have a shell. So you need to use `sudo su -s /bin/bash jenkins` to start a shell for the **jenkins** user. Then establish a passwordless connection between `centos1` and `centos2` using the **jenkins** user.

```bash
[paul@centos01 playbooks]$ sudo su -s /bin/bash jenkins
bash-5.1$ whoami
jenkins
bash-5.1$ ssh-keygen
bash-5.1$ ssh-copy-id jenkins@10.211.55.11
bash-5.1$ ssh jenkins@10.211.55.11
[jenkins@centos02 ~]$
```

Now when you run the same job again, it should be successful. The `jenkins` user has permission to run the playbook on the `centos2` (remote server) from the `centos1` server.

### Project: Remote Webserver Changes

In this example, the scenario is that on the `centos2` server, we have an Apache webserver running, and the website is live. We are going to make changes to this using Jenkins. Jenkins will pull code from GitHub, copy it to the `centos2` server, and restart the service.

First, set up an Apache server on `centos2`.

```bash
[paul@centos02 ~]$ sudo yum install httpd -y
[paul@centos02 ~]$ sudo systemctl start httpd.service
[paul@centos02 ~]$ sudo systemctl status httpd.service
```

Now in your browser, try to access the IP of the `centos2` server; you should be able to see the default Apache page. The content of this default page will be stored in `/var/www/html/` on the server. We need to replace the default webpage with our new webpage stored in a GitHub repository.

In Jenkins Dashboard:
1. Click on **New Item**. Enter an item name: `webserver-update`. Choose **Freestyle project**. Click on **OK**.
2. Scroll down to **Source Code Management**. Choose **Git**.
3. Provide the **Repository URL** of the GitHub repo.

At this point, Jenkins should be able to pull the repo from GitHub and store it in the `centos1` **workspace** of this Jenkins Job at `/var/lib/jenkins/workspace/webserver-update/index.html`. The next step is to copy this `index.html` file to the `centos2` server in the `/var/www/html/` path and restart the Apache service. For this, we will use an Ansible playbook. Create a simple playbook on the `centos1` server to perform this task.

```sh
[paul@centos01 playbooks]$ vi webserver_update.yml
```

```yml
---
- name: Upload new code and Restart the service 
  hosts: 10.211.55.11
  vars:
    - app: httpd

  tasks:
  - name: Place custom HTML File
    copy:
      src: /var/lib/jenkins/workspace/webserver-update/index.html
      dest: /var/www/html/index.html
    become: true
  
  - name: Start the service 
    service:
      name: httpd
      state: restarted 
      enabled: yes 
    become: true
```

4. Scroll down to **Build Steps**. Click on the **Add build step** dropdown. Choose **Invoke Ansible Playbook**.
5. Provide the **Playbook path**: `/etc/ansible/playbooks/webserver_update.yml`.
6. In the same option, scroll down to the **Advanced** dropdown. Scroll down to **Extra Variables**. Click on **Add Extra Variable**.
7. In the **Key** field, provide `ansible_become_pass`. And in the **Value** field, provide `jenkins` (the sudo password of the jenkins user on `centos2`, to run the Ansible playbook with sudo access).
8. Click on **Save**. Then click on **Build Now**.

Now this job should be able to pull the code from GitHub, copy it to the remote server, and restart the Apache service using the Ansible playbook.

You can also configure SCM polling to automatically pull the code from GitHub when changes are detected. Additionally, set up email notifications for job failure.

In the Jenkins dashboard:
- Click on the **webserver-update** job. Click on **Configure**.
- Scroll down to **Build Triggers**. Check **Poll SCM**. In **Schedule**, provide the cron expression `* * * * *`.
- Scroll down to **Post-build Actions**. Click on **Add Post-build Actions** dropdown. Choose **E-mail Notification**. Provide the recipient email ID.
- Click on **Save**.

### User Management in Jenkins

User management in Jenkins is crucial when working in a team, where different users require various levels of access and permissions.

- In the Jenkins Dashboard, click on **Manage Jenkins**. Scroll down to **Security** and click on **Users**.
- Click on **Create User**. Provide **Username** (e.g., `nick`), **Password**, **Full Name**, and **Email address**. Click on **Create User**.
- Now, in **Manage Jenkins**, click on **Plugins**. Click on **Available Plugins**. Search for and install the **Role-based Authorization Strategy** plugin.
- Check the box **Restart Jenkins when installation is complete and no jobs are running** after installation.
- Wait for Jenkins to restart. Once restarted, you will be signed out. Log in to Jenkins using the new user `nick` you just created.

By default, this new user will have all permissions. You need to restrict the permissions of this user.
- Click on **Manage Jenkins**. Scroll down to **Security** and click on **Configure Global Security**.
- Scroll down to **Authorization**. Click on the dropdown and choose **Role-based Strategy**. Click on **Save**.
- Now, in **Manage Jenkins**, scroll down to **Security** and click on **Manage and Assign Roles**. Click on **Manage Roles**.
- Scroll down to **Role to add**. Enter a role name (e.g., `read-only`) and click on **Add**.
- Your new role will appear under **Global roles**. Check the boxes for the permissions you want to assign to this role (e.g., `read`). Click on **Save**.

To assign the `read-only` role to the `nick` user:
- In the same **Manage and Assign Roles** section, click on **Assign Roles**.
- Under **Global roles**, click on **Add User** and type the **User ID** (e.g., `nick`). Click on **OK**.
- Check the box for `read-only` against the user `nick`. Click on **Save**.

Now, you can log in to Jenkins with the `nick` user, who will have only read-only permissions.

### Environment Variables in Jenkins

When a Jenkins job executes, it sets some pre-defined environment variables that you can use in your shell script, batch command, etc.

You can use pre-defined variables in Jenkins, such as `BUILD_NUMBER`, `WORKSPACE`, etc.

For more information on Jenkins environment variables, refer to the [Jenkins Environment Variables Documentation](https://wiki.jenkins.io/display/JENKINS/Building+a+software+project#Buildingasoftwareproject-belowJenkinsSetEnvironmentVariables).

- **Example Usage:** In any job, under **Build Steps**, if you use **Execute Shell**, you can pass environment variables with the `$` sign, just like in Linux, and it will get the pre-defined values for you.

    ```sh
    echo $WORKSPACE
    echo $BUILD_NUMBER
    ```

- **Creating Custom Environment Variables:**
  1. In Jenkins, click on **Manage Jenkins**. Click on **System**.
  2. Scroll down to **Global Properties**. Check **Environment Variables**. In **List of Variables**, click on the **Add** button.
  3. Provide a **Name** and **Value** for your custom environment variable. Click on **Save**.

Now you can use this custom environment variable anywhere in Jenkins, just like the pre-defined variables.

### Maven with Jenkins

Maven is a build automation tool used primarily for Java projects.

We are using the following Git Repository for this example:  
https://github.com/jenkins-docs/simple-java-maven-app.git

In Jenkins Dashboard:
1. Click on **New Item**. Enter an item name: `maven-demo`. Choose **Freestyle project**. Click on **OK**.
2. Scroll down to **Source Code Management**. Choose **Git**.
3. Provide the **Repository URL** of the GitHub repo where your Maven project is saved.
4. Scroll down to **Branches to build**. In **Branch Specifier**, check the branch. It should be the same branch as your project in GitHub.
5. Click on **Save**. And click on **Build Now**.

Now, the code should be cloned into the Jenkins Workspace. To install Maven, we need plugins.

**Maven Installation:**
1. Click on **Manage Jenkins**. Click on **Plugins**. Click on **Available Plugins**.
2. Search for and install the **Maven Integration** plugin. (You can use it right away or after a restart.)
3. Once this is installed, click on **Manage Jenkins**. Click on **Tools**.
4. Scroll down to **Maven Installation**. Click on **Maven**.
5. Provide a **Name**, check the box **Install automatically**, and select the Maven version in **Install from Apache**. Click on **Save**.
6. Select the `maven-demo` job. Click on **Configure**. Scroll down to **Build Steps**.
7. Click on **Add build step** and select **Invoke top-level Maven targets**.
   - In the **Maven Version** field, select the desired Maven version to use for this build.
   - In the **Goals** field, provide the Maven goals that you want to execute.

     For building a JAR file using Maven, you would typically use the command `mvn -B -DskipTests clean package` in a command-line environment. However, in Jenkins, you don’t need to include `mvn` in the Goals field. You only need to specify the Maven goals directly. So, you would enter `-B -DskipTests clean package` in the Goals field.
8. Click on **Save**. And click on **Build Now**.

> If you get a BUILD FAILURE error while compiling the file with **javac**, check in the Jenkins server if the **javac** command is present. If not present already, you need to install `java-17-openjdk-devel.aarch64`. You can use the command `sudo yum install java-17-openjdk-devel.aarch64` for the installation. Once installed, try to build again in Jenkins.

Once the build is successful, you should be able to see the `my-app-1.0-SNAPSHOT.jar` file in the `/var/lib/jenkins/workspace/maven-demo/target/my-app-1.0-SNAPSHOT.jar` directory. A new folder named **target** will be created in the workspace, and the `.jar` file will be inside that folder.

### TESTING Of Application

So, the code we have in GitHub, if you go inside the **src** folder, you will see `main/java/com/mycompany/app` and `test/java/com/mycompany/app`. The main code will be present in the main folder and test cases will be present in the test folder.

Now using Jenkins, in the first step, we cloned the code; in the next step, we built the JAR file of the code using Maven. In this step, we need to test the application. For this, we use `mvn test` in the command line. In Jenkins, we need to pass this value in the **Goals** field.

- Select the `maven-demo` job. Click on **Configure**. Scroll down to **Build Steps**. Add another build step.
- Click on **Add build step** and select **Invoke top-level Maven targets**.
  - In the **Maven Version** field, select the desired Maven version to use for this build.
  - In the **Goals** field, provide the Maven goals that you want to execute. In our case, you just provide `test`. And click on **Save** and click on **Build Now**.

Now the job will build the JAR file as well as test the code too. Your console output will be something like:

```
......
.
.
.
Downloaded from central: https://repo.maven.apache.org/maven2/org/junit/platform/junit-platform-
launcher/1.10.2/junit-platform-launcher-1.10.2.jar (184 kB at 3.6 MB/s)
[INFO]
[INFO] --------------------------------------------------------
[INFO]    T E S T S
[INFO] --------------------------------------------------------
[INFO] Running com.mycompany.app.AppTest
[INFO] Tests run: 2, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.037 - in com.mycompany-app.AppTest
[INFO]
[INFO] Results:
[INFO]
[INFO] Tests run: 2, Failures: 0, Errors: 0, Skipped: 0
[INFO] 
[INFO] -------------------------------------------------------------------------
[INFO] BUILD SUCCESS [INFO]
[INFO] -------------------------------------------------------------------------
Total time: 2.771 s
[INFO] Finished at: 2024-04-12T11:35:44+05:30
[INFO] -------------------------------------------------------------------------
Finished: SUCCESS
```

### Local Deployment Of App

Now, since we have built the application and tested it, we need to deploy the application. As we are doing a local deployment, we already have a JAR file and just need to run it.

- Select the `maven-demo` job. Click on **Configure**. Scroll down to **Build Steps**. Add another build step.
- Click on **Add build step** and select **Execute Shell**.
- In **Command**, provide `java -jar <location_of_the_jar>`.
```
echo "DEPLOYMENT STEP"
java -jar /var/lib/jenkins/workspace/maven-demo/target/my-app-1.0-SNAPSHOT.jar
```
- Click on **Save** and click on **Build Now**.

Your console output should be something like:
```
...
.
.
.
[INFO] -------------------------------------------------------------------------
[INFO] BUILD SUCCESS 
[INFO] -------------------------------------------------------------------------
[INFO] Total time: 1.573 s
[INFO] Finished at: 2024-04-12T12:12:38+05:30
[INFO] -------------------------------------------------------------------------
[maven-demo] $ /bin/sh -xe /tmp/jenkins12320327623376406642.sh 
+ echo 'DEPLOYMENT STEP'
DEPLOYMENT STEP
+ java -jar /var/lib/jenkins/workspace/maven-demo/target/my-app-1.0-SNAPSHOT.jar
Hello World!
Finished: SUCCESS
```

### Test Results Graph

Whenever we click on **Build Now**, we can see the results in **Build History**. But there is also another way of graphical representation of results.

- In Jenkins, select the `maven-demo` job, click on **Workspace**, and you will get **Workspace of maven-demo on Built-in Node**. This is basically your directory structure of the `maven-demo` job workspace from the server.
- Under **maven-demo/**, you will see multiple folders. Select **target/**. Select **surefire-reports/**. Inside this folder, you will see an **.xml** file.
  If you open it, it will generate a report in XML format. Just copy the path to this report.
- Click on **Configure**. Scroll down to **Post-build Actions**. Click on **Add post-build actions** dropdown. Select **Publish JUnit test result report**.
- In **Test report XMLs**, provide the path of the XML file `target/surefire-reports/*.xml`.
- Click on **Save** and click on **Build Now**.

Now once it builds, you will get results in Build History as usual. Also, in job **Status**, you will get a **Test Result Trend** option, which graphically represents the results.

### Archive Artifact

- Click on **Configure**. Scroll down to **Post-build Actions**. Click on **Add post-build actions** dropdown. Select **Archive the artifacts**.
- In **Files to archive**, provide the file path you want to archive: `target/*.jar`.
- Click on **Advanced**, check the box **Archive artifacts only if the build is successful**.
- Click on **Save** and click on **Build Now**.

Now in the status, you will see `my-app-1.0-SNAPSHOT.jar` in **Last Successful Artifacts**. This will save artifacts of every build. You can download them by clicking on it.

### Jenkins Pipeline

The flowchart below is an example of one CD scenario easily modeled in Jenkins Pipeline:

![CD Pipeline](https://www.jenkins.io/doc/book/resources/pipeline/realworld-pipeline-flow.png)

In Jenkins Dashboard:
- Click on **New Item**. Provide an **Item name**, in this example `pipeline-demo`, choose **Pipeline**, and click on **OK**.
- In **Configure**, scroll down to **Pipeline**. In **Definition**, choose **Pipeline Script**.
- In **Script**, you will get a dropdown to choose **try sample pipeline**. Choose **Hello World**.

```groovy
pipeline {
    agent any

    stages {
        stage('Hello') {
            steps {
                echo 'Hello World'
            }
        }
    }
}
```
- Check the box **Use Groovy Sandbox**.
- Click on **Save** and click on **Build Now**.
- In the **Status** option, you will see **Stage View**. When you hover over the result of the stage view, you will get a **Logs** button. You will get logs of every stage.
- Click on the **Logs** button to view the logs.

> When you have multiple steps, and if any step fails in between, it will not proceed to the next step.

#### Multi-Steps Pipeline Step

If you want to execute any shell command, you will use `sh` to pass the commands.

- Single Line Commands
```groovy
pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                sh 'echo Hello'
            }
        }
    }
}
```
- Multi Line Commands
```groovy
pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                sh '''
                    echo Hello
                    echo World
                '''
            }
        }
    }
}
```
#### Retry Steps in Jenkins Pipeline

If you want any step to retry multiple times, you can use `retry(number_of_times)`.

```groovy
pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                sh '''
                    echo Hello
                    echo World
                '''
                retry(3){
                    sh 'ech trying...'
                }
            }
        }
    }
}
```
In the **retry** section, as `ech` is not a valid command, it tries to execute it 3 times then fails.

#### Timeout Function in Jenkins

If you have some task that is taking too much time to execute, you can add a **timeout function** to control this.

```groovy
pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                sh '''
                    echo Hello
                    echo World
                '''
                retry(3){
                    sh 'echo trying...'
                }

                timeout(time:5, unit:'SECONDS'){
                    sh 'sleep 30'
                }
            }
        }
    }
}
```

In the above example, in the timeout section, we have a command to go to sleep mode for 30 seconds before completing the job using `sh 'sleep 30'`. But the timeout function has set a time limit of 5 seconds using `timeout(time:5, unit:'SECONDS')`. Hence, it should get timed out after 5 seconds and shouldn't wait for 30 seconds.

#### Environment Variables in Jenkins

To use environment variables in the script, you need to provide an environment block in the script.

```groovy
pipeline {
    agent any

    environment {
        NAME="Paul"
        AGE=20
    }

    stages {
        stage('Build') {
            steps {
                sh '''
                    echo $NAME
                    echo $AGE
                '''
                //retry(3){
                //    sh 'echo trying...'
                //}
            }
        }
    }
}
```

Now when you **Save** and click on **Build Now**, environment variables should be able to fetch the values from the environment block.

> Using `//` you can comment out the commands.

If you want to provide any secret values and you don't want to enter the values openly in the script, you can use Jenkins to store the credentials and use environment variables to fetch the value.

- Click on **Manage Jenkins**. Click on **Credentials**.
- In **Stores scoped to Jenkins**, click on **System**.
- In **System**, you will see **Global credentials (unrestricted)** dropdown. Click on the dropdown and click on **Add credentials**.
- In the **New Credentials** page, in **Kind**, choose **Secret text**. Under this, **Scope** should be **Global (Jenkins, nodes, items, all child items, etc.)**. Provide your `Password` in the **Secret** option. And in **ID**, you can use any name; in this example, we use `PASS`.
- Click on **Create**.

Now in the script, you can use `credentials('ID_name')` to fetch the secret.

```groovy
pipeline {
    agent any

    environment {
        NAME="Paul"
        AGE=20
        PASSWORD=credentials('PASS')
    }

    stages {
        stage('Build') {
            steps {
                sh '''
                    echo $NAME
                    echo $PASSWORD
                    echo $AGE
                '''
            }
        }
    }
}
```
Now the variable `$PASSWORD` has the value `credentials('PASS')`, which fetches the password from Jenkins when the build runs. But no one will be able to see the password even after the build is successful in **Console Output** or **Logs**. It will display as `*******` to maintain secrecy.

**Console Output:**
```
...
.
.
[Pipeline] { (Build)
[Pipeline] sh
+ echo Paul
Paul
+ echo ****
****
+ echo 20
20
[Pipeline] }
.
.
...
```

### Maven Project With Jenkins Pipeline

We are using the following Git Repository for this example:  
[https://github.com/jenkins-docs/simple-java-maven-app.git](https://github.com/jenkins-docs/simple-java-maven-app.git)

In Jenkins Dashboard:
- Click on **New Item**. Provide an **Item name**; in this example, use `maven-pipeline`. Choose **Pipeline** and click **OK**.
- In **Configure**, scroll down to **Pipeline**. In **Definition**, choose **Pipeline Script**.
- In **Script**, you will get a dropdown to choose **Try sample pipeline**. Choose **GitHub+Maven**.
  You will get a basic sample script. You can either edit the script directly there or copy it to VS Code and edit it in VS Code.

```groovy
pipeline {
    agent any

    tools {
        // Install the Maven version configured and add it to the path.
        maven "jenkins-maven"
    }

    stages {
        stage ('Checkout') {
            steps {
                // Get some code from a GitHub repository
                git 'https://github.com/jenkins-docs/simple-java-maven-app.git'
            }
        }
        stage ('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
        stage ('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit '**/target/surefire-report/TEST-*.xml'
                }
            }
        }
        stage ('Deploy') {
            steps {
                // Transfer the Jar to remote server
                sh 'echo Execute the Jar file'
            }
        }
    }
}
```

---

### Explanation

#### Pipeline Declaration
```groovy
pipeline {
    agent any
```
- **agent any**: This specifies that the pipeline can run on any available agent (Jenkins node). It means Jenkins will use any available machine to execute the pipeline stages.

#### Tools Section
```groovy
    tools {
        maven "jenkins-maven"
    }
```
- **tools**: This section defines tools required for the pipeline. Here, it specifies that Maven should be installed and configured. `"jenkins-maven"` is the name of the Maven installation configured in Jenkins (this should match a tool name configured in Jenkins' global tool configuration).

#### Stages
The pipeline consists of several stages, each performing a different task in the CI/CD process.

##### 1. Checkout
```groovy
    stages {
        stage ('Checkout') {
            steps {
                git 'https://github.com/jenkins-docs/simple-java-maven-app.git'
            }
        }
```
- **stage ('Checkout')**: This stage is responsible for retrieving the source code from a Git repository.
- **git 'https://github.com/jenkins-docs/simple-java-maven-app.git'**: This step clones the specified GitHub repository to the Jenkins workspace.

##### 2. Build
```groovy
        stage ('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
```
- **stage ('Build')**: This stage compiles and packages the code.
- **sh 'mvn clean package'**: Executes the Maven command to clean any previous build artifacts and then package the application into a JAR file.

##### 3. Test
```groovy
        stage ('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit '**/target/surefire-report/TEST-*.xml'
                }
            }
        }
```
- **stage ('Test')**: This stage runs the unit tests for the application.
- **sh 'mvn test'**: Runs the Maven test phase to execute the tests.
- **post**: After the test step has completed, this block defines actions to be performed regardless of the test result.
  - **always**: Ensures that the following steps run even if the tests fail.
  - **junit '**/target/surefire-report/TEST-*.xml'**: Publishes the test results to Jenkins using the JUnit plugin. This allows Jenkins to display test results in the build report.

##### 4. Deploy
```groovy
        stage ('Deploy') {
            steps {
                sh 'echo Execute the Jar file'
            }
        }
    }
}
```
- **stage ('Deploy')**: This stage is intended for deployment tasks.
- **sh 'echo Execute the Jar file'**: This is a placeholder command that simply prints a message. In a real deployment, this would likely be replaced with commands to transfer the JAR file to a remote server or execute the JAR file on a deployment server.

---

- Once the script is written in VS Code, copy it into the Jenkins job's **Pipeline Script** section. Click **Save** and then **Build Now**.
- You should be able to see the status of each stage in the **Stage View** board.

    Declarative: Tool Install > Checkout > Build > Test > Deploy

- If any stage fails, for example, if the **Test** stage fails, the subsequent **Deploy** stage will not be executed.

#### Adding Archiving Artifacts and Email Notification

You need to add a **post** block after the completion of the **stages** block. Provide the commands to send an email notification in case of build failure and to archive the artifacts if the build is successful.

```groovy
pipeline {
    agent any

    tools {
        // Install the Maven version configured and add it to the path.
        maven "jenkins-maven"
    }

    stages {
        stage ('Checkout') {
            steps {
                // Get some code from a GitHub repository
                git 'https://github.com/jenkins-docs/simple-java-maven-app.git'
            }
        }
        stage ('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
        stage ('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit '**/target/surefire-report/TEST-*.xml'
                }
            }
        }
        stage ('Deploy') {
            steps {
                // Transfer the Jar to a remote server
                sh 'echo Execute the Jar file'
            }
        }
    }
    post {
        failure {
            echo 'failure!'
            mail to: 'phillippaul019@gmail.com',
                subject: "FAILED: ${env.JOB_NAME} - Build #${env.BUILD_NUMBER}",
                body: "Job '${env.JOB_NAME}' (${env.BUILD_URL}) failed."
        }
        success {
            echo 'Build and Test Stages Successful!'
            archiveArtifacts artifacts: '**/target/*.jar', onlyIfSuccessful: true
        }
    }
}
```
Now, if the build is successful, you should see the artifact file in the **Stage View**, which you can download. If the build fails, you will receive a **Mail Notification** mentioning the build failure, with a **Build URL** in the email. You can click on it to see the **Console Output** and get the logs for failure.

#### Approval Before Deployment

In some cases, you may need manual approval before proceeding with deployment instead of automatic deployment. For this, you need to add another stage in the pipeline script for `Approval`.

In this example, we are adding an `Approval` stage after the `Test` stage.

```groovy
pipeline {
    agent any

    tools {
        // Install the Maven version configured and add it to the path.
        maven "jenkins-maven"
    }

    stages {
        stage ('Checkout') {
            steps {
                // Get some code from a GitHub repository
                git 'https://github.com/jenkins-docs/simple-java-maven-app.git'
            }
        }
        stage ('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
        stage ('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit '**/target/surefire-report/TEST-*.xml'
                }
            }
        }
        stage ('Approval') {
            steps {
                input 'Approve Deployment to Production?'
            }
        }
        stage ('Deploy') {
            steps {
                // Transfer the Jar to a remote server
                sh 'echo Execute the Jar file'
            }
        }
    }
    post {
        failure {
            echo 'failure!'
            mail to: 'phillippaul019@gmail.com',
                subject: "FAILED: ${env.JOB_NAME} - Build #${env.BUILD_NUMBER}",
                body: "Job '${env.JOB_NAME}' (${env.BUILD_URL}) failed."
        }
        success {
            echo 'Build and Test Stages Successful!'
            archiveArtifacts artifacts: '**/target/*.jar', onlyIfSuccessful: true
        }
    }
}
```

Now, when you build this job, after the **Test** stage is completed, it will wait for you to manually approve proceeding with the next stage, **Deploy**. In **Stage View**, when you hover over the **Approval** stage, you will get options to click on **Proceed** or **Cancel**. You need to choose whether to proceed with deployment based on your requirements.

> You can use Jenkins [Pipeline Syntax](https://www.jenkins.io/doc/book/pipeline/syntax/#steps) official documentation for reference.

### Snippet Generator Jenkinsfile

Jenkins will help you generate pipeline script syntax that you can use in your scripts. You just need to add `pipeline-syntax` after the Jenkins URL. In our case, our Jenkins URL is "http://10.211.55.7:8080/". For the syntax generator, you can use "http://10.211.55.7:8080/pipeline-syntax".

You will get multiple options like Snippet Generator, Declarative Directive Generator, etc.

- For example, in **Snippet Generator**, in **Steps**, select **archiveArtifacts: Archive the artifacts** from the dropdown.
- In **Files to archive**, provide `/target/*.jar`.
- Click on the **Advanced** dropdown and check the box **Archive the artifact only if build is successful**.
- Click **Generate Pipeline Script**.

This will generate a syntax that you can use in your pipeline script:
```groovy
archiveArtifacts artifacts: '/target/*.jar', followSymlinks: false, onlyIfSuccessful: true
```