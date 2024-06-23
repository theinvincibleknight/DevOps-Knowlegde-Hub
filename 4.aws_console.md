## Introduction to AWS

**What is AWS Cloud?** <br>
AWS (Amazon Web Services) Cloud refers to the comprehensive and constantly expanding suite of cloud computing services provided by Amazon. These services include computing power, storage options, databases, networking, machine learning, and more. AWS enables businesses and individuals to access computing resources on-demand over the internet with a pay-as-you-go pricing model.

**Advantages of AWS Cloud:**
1. **Scalability:** Easily scale resources up or down based on demand.
2. **Flexibility:** Choose from a wide range of services to meet specific needs.
3. **Cost-effective:** Pay only for what you use, with no upfront costs.
4. **Security:** Built-in security measures to protect data and applications.
5. **Global Reach:** AWS operates in multiple geographic regions, allowing for low-latency access worldwide.
6. **Reliability:** High availability and redundancy ensure minimal downtime.

### Creating an IAM User and Access Keys

IAM (Identity and Access Management) is used to manage access to AWS services and resources securely.

**Steps to Create an IAM User and Access Keys:**
1. **Sign in to the AWS Management Console:**
   - Go to [AWS Management Console](https://aws.amazon.com/console/) and sign in.

2. **Navigate to IAM:**
   - In the AWS Management Console, search for and select "IAM" under "Services".

3. **Create a New User:**
   - In the IAM dashboard, click on "Users" in the left-hand menu.
   - Click on "Add user" to start creating a new IAM user.

4. **Set User Details:**
   - Enter a username for the new user.
   - Choose access type:
     - **Programmatic access:** Allows the user to interact with AWS using APIs, SDKs, or CLI.
     - **AWS Management Console access:** Allows the user to sign in to the AWS Management Console with a password.

5. **Set Permissions:**
   - Attach policies to the user to grant permissions (e.g., AmazonEC2FullAccess for EC2 management).

6. **Review and Create:**
   - Review the user details and permissions.
   - Click "Create user".

7. **Access Keys:**
   - After creating the user, you will see a screen with the user's details.
   - Under "Security credentials", click on "Create access key".
   - Note down the Access Key ID and Secret Access Key (or download the .csv file containing these keys).

### Launching an Ubuntu EC2 Instance, Logging In, and Configuring AWS CLI

**Steps to Launch an Ubuntu EC2 Instance:**

1. **Sign in to AWS Management Console:**
   - Go to [AWS Management Console](https://aws.amazon.com/console/) and sign in.

2. **Navigate to EC2:**
   - In the AWS Management Console, search for and select "EC2" under "Services".

3. **Launch Instance:**
   - Click on "Instances" in the left-hand menu.
   - Click on "Launch Instance" to start the instance creation wizard.

4. **Choose AMI (Ubuntu):**
   - Select an Ubuntu AMI (Amazon Machine Image). For example, search for "Ubuntu" and choose an appropriate version.

5. **Choose Instance Type:**
   - Select an instance type based on your requirements (e.g., t2.micro for a free-tier eligible instance).

6. **Configure Instance Details:**
   - Configure instance details such as network settings, subnet, IAM role (if required), etc. Leave defaults if unsure.

7. **Add Storage:**
   - Specify the storage size and type (defaults are usually fine for testing).

8. **Add Tags (Optional):**
   - Add any tags if needed to identify your instance.

9. **Configure Security Group:**
   - Create a new security group or select an existing one. Ensure SSH (port 22) is open to your IP address for remote access.

10. **Review and Launch:**
    - Review your instance configuration.
    - Click "Launch" and select an existing key pair or create a new key pair to securely connect to your instance.

11. **Accessing and Configuring AWS CLI on Ubuntu EC2 Instance:**

    **a. Logging into your Instance:**
    - Once the instance is launched, note its Public IP or Public DNS.
    - Use SSH to connect to your instance from a terminal:
      ```bash
      ssh -i /path/to/your/keypair.pem ubuntu@public_dns_or_ip
      ```
      Replace `/path/to/your/keypair.pem` with the path to your private key file.

    **b. Installing AWS CLI on Ubuntu:**
    - Update package lists and install AWS CLI:
      ```bash
      sudo apt-get update
      sudo apt-get install awscli
      ```

    **c. Configuring AWS CLI:**
    - Configure AWS CLI with the IAM user credentials you created earlier:
      ```bash
      aws configure
      ```
      Enter the Access Key ID, Secret Access Key, default region (e.g., `us-east-1`), and output format (e.g., `json`).

    **d. Testing AWS CLI:**
    - Test your AWS CLI installation by running a simple command, for example:
      ```bash
      aws ec2 describe-instances
      ```
      This should list the EC2 instances in your account.