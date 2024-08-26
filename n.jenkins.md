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