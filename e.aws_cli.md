## Introduction to AWS CLI

AWS CLI (Command Line Interface) is a unified tool that allows you to manage your AWS services and resources directly from the command line. It provides a convenient way to automate and script interactions with AWS services, making it a powerful tool for administrators, developers, and anyone working with AWS.

### Most Commonly Used AWS CLI Commands

Here are some of the most commonly used AWS CLI commands along with simple explanations:

1. **aws configure**
   - Command: `aws configure`
   - Purpose: Configures your AWS CLI with your AWS access key, secret key, default region, and output format.
   - Example:
     ```bash
     aws configure
     ```

2. **aws ec2 describe-instances**
   - Command: `aws ec2 describe-instances`
   - Purpose: Lists information about your Amazon EC2 instances.
   - Example:
     ```bash
     aws ec2 describe-instances
     ```

3. **aws s3 ls**
   - Command: `aws s3 ls [s3://bucketname]`
   - Purpose: Lists all buckets or objects within a specified S3 bucket.
   - Example:
     ```bash
     aws s3 ls s3://my-bucket
     ```

4. **aws s3 cp**
   - Command: `aws s3 cp [file] [s3://bucketname/path]`
   - Purpose: Copies a file or directory to/from Amazon S3.
   - Example (uploading a file to S3):
     ```bash
     aws s3 cp localfile.txt s3://my-bucket/path/to/file.txt
     ```

5. **aws ec2 create-instance**
   - Command: `aws ec2 run-instances --image-id [AMI ID] --instance-type [Instance Type] --key-name [Key Pair Name]`
   - Purpose: Launches a new EC2 instance.
   - Example:
     ```bash
     aws ec2 run-instances --image-id ami-12345678 --instance-type t2.micro --key-name my-keypair
     ```

6. **aws ec2 terminate-instance**
   - Command: `aws ec2 terminate-instances --instance-ids [Instance ID]`
   - Purpose: Terminates (shuts down) one or more EC2 instances.
   - Example:
     ```bash
     aws ec2 terminate-instances --instance-ids i-1234567890abcdef0
     ```

7. **aws iam list-users**
   - Command: `aws iam list-users`
   - Purpose: Lists IAM users in your AWS account.
   - Example:
     ```bash
     aws iam list-users
     ```

8. **aws rds describe-db-instances**
   - Command: `aws rds describe-db-instances`
   - Purpose: Lists information about your Amazon RDS DB instances.
   - Example:
     ```bash
     aws rds describe-db-instances
     ```

### Using Query with AWS CLI to Get Desired Outputs

AWS CLI allows you to use the `--query` parameter to filter and format the output of commands. This is particularly useful when you want to extract specific information from the JSON output returned by AWS CLI commands.

**Syntax for using `--query`:**

```bash
aws <service> <operation> --query <query-expression>
```

Here are a few examples of using `--query` with AWS CLI commands:

1. **Filtering EC2 Instance Details**

   Retrieve instance IDs and their associated public DNS names:

   ```bash
   aws ec2 describe-instances --query "Reservations[].Instances[].[InstanceId, PublicDnsName]"
   ```

2. **Listing S3 Bucket Names**

   List all S3 bucket names only:

   ```bash
   aws s3api list-buckets --query "Buckets[].Name"
   ```

3. **Describing IAM Users**

   List IAM user names and their creation dates:

   ```bash
   aws iam list-users --query "Users[].{UserName:UserName, CreationDate:CreateDate}"
   ```

4. **Filtering RDS Instance Details**

   Retrieve RDS DB instance IDs, engine types, and statuses:

   ```bash
   aws rds describe-db-instances --query "DBInstances[].{DBInstanceIdentifier:DBInstanceIdentifier, Engine:Engine, DBInstanceStatus:DBInstanceStatus}"
   ```

5. **Getting EC2 Instance IDs from Specific Tag**

   Retrieve EC2 instance IDs that have a specific tag key-value pair:

   ```bash
   aws ec2 describe-instances --filters "Name=tag-key,Values=Environment" --query "Reservations[].Instances[].InstanceId"
   ```

6. **Listing EC2 Instance IDs and Private IP Addresses**

   Retrieve EC2 instance IDs and their corresponding private IP addresses:

   ```bash
   aws ec2 describe-instances --query "Reservations[].Instances[].[InstanceId, PrivateIpAddress]"
   ```

   This command will list all EC2 instance IDs along with their private IP addresses.

7. **Filtering and Sorting EC2 Instances by Launch Time**

   List EC2 instance IDs and their launch times, sorted by launch time in descending order:

   ```bash
   aws ec2 describe-instances --query "Reservations[].Instances[].[InstanceId, LaunchTime]" --output json | jq -r 'sort_by(.LaunchTime) | reverse[] | [.InstanceId, .LaunchTime]'
   ```

   This command uses `jq` (a command-line JSON processor) to sort the instances by launch time and display the results in descending order.

8. **Counting the Number of IAM Users**

   Get the total count of IAM users in your AWS account:

   ```bash
   aws iam list-users --query "length(Users)"
   ```

   This command uses the `length()` function in the query expression to count the number of IAM users returned.

9. **Filtering RDS Instances by Instance Class**

   List RDS DB instances and their instance classes (DB instance types) for instances where the instance class starts with "db.t":

   ```bash
   aws rds describe-db-instances --query "DBInstances[?starts_with(DBInstanceClass, 'db.t')].[DBInstanceIdentifier, DBInstanceClass]"
   ```

   This command filters RDS instances based on the instance class starting with 'db.t' and lists their identifiers and classes.

10. **Finding S3 Buckets with Specific Tag**

   List S3 bucket names that have a specific tag key-value pair (e.g., "Environment:Production"):

   ```bash
   aws s3api list-buckets --query "Buckets[?Tags[?Key=='Environment' && Value=='Production']].Name"
   ```

   This command filters S3 buckets based on a specific tag key-value pair and lists their names.

11. **Describing Auto Scaling Groups with Instances Count**

   List Auto Scaling groups along with the number of instances in each group:

   ```bash
   aws autoscaling describe-auto-scaling-groups --query "AutoScalingGroups[].{AutoScalingGroupName: AutoScalingGroupName, InstancesCount: length(Instances)}"
   ```

   This command uses the `length()` function to count the number of instances in each Auto Scaling group.

> Reference: [AWS CLI Command Reference](https://docs.aws.amazon.com/cli/latest/)