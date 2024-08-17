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
CONTINUOUS INTEGRATION --> CONTINUOUS DELIVERY --> CONTINUOUS DEPLOYMENT
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

- Click on **New Item**. Provide an **item name**, choose **Freestyle project**, and click on **OK**.
- On the next page, you can provide a description (optional). Scroll down to **Build Steps**.
- Click on the **Add build step** dropdown to see multiple options. Choose **Execute Shell**.
- Type a basic command to execute on the shell. For example: `echo "HELLO BUDDY!!"`. Click on **Save**.
- Now, when you click on this Job and open it, you will see the **Build Now** option. Each time you click on **Build Now**, it executes the command and saves it in the **Build History**.
- Clicking on any entry in **Build History** opens the job details, including Success/Failure status. If you click on **Console Output**, it will show the output when the job was run.

All the jobs we run in Jenkins are performed on the CentOS server itself.

- Go back to the Job, click on **Configure** (next to **Build Now**).
- On the next page, scroll down to **Build Steps** again.
- Edit the execute command option to `echo "Hello" > /tmp/first_job.txt`. Click on **Save**.
- Click on **Build Now**.
- Now, on the CentOS server, navigate to the **/tmp/** directory and check for the **first_job.txt** file. The job should create a new file in the provided path on the CentOS server.