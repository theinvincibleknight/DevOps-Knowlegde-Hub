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















