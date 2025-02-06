# Amazon Web Services (AWS)
AWS tips

# Create AWS Free Account
1. Go to https://signin.aws.amazon.com/signup?request_type=register
2. Enter your email address and username.
3. Then verify email address, enter OTP then continue.
4. Select for personal use
5. Enter full name, mobile number, country, address, city, state
6. Then check AWS customer agreement and click Agree and continue.
7. Enter Billing information, card details then agree and continue.
8.  confirm your identity, enter mobile number, send AWS SMS code, agree and continue
9.  Select support plan, choose basic support -free then complete signup
10.  In AWS console, Choose Singapore arew= region.

# Create Key Pair
1. Using the AWS Management conosle, Go to the EC2 Dashboard in the AWS Management Console.
2. In the left navigation pane, under "Network & Security", click on "Key Pairs".
3. Click the "Create key pair" button at the top of the page.
4. Enter mykeypair then click Create Key Pair.

# How to Get AMI ID?
1. Go to the EC2 Dashboard in the AWS Management Console.
2. Click on "Launch Instance".
3. In the "Choose an Amazon Machine Image (AMI)" page, look for "Amazon Linux 2 AMI (HVM) - Kernel 5.10, SSD Volume Type" under the "Quick Start" section.
4. You'll see the AMI ID next to the AMI name. It will be in the format "ami-xxxxxxxxxxxxxxxxx".
   
# How to get key-pair name?
1. Go to the EC2 Dashboard in the AWS Management Console.
2. In the left navigation pane, under "Network & Security", click on "Key Pairs".
3. You'll see a list of your existing key pairs. The names are listed in the "Key pair name" column.
4. If you don't have a key pair, you can create one by clicking the "Create key pair" button at the top of the page.

# How to get Security Group ID?
1. Go to the EC2 Dashboard in the AWS Management Console.
2. In the left navigation pane, under "Network & Security", click on "Security Groups".
3. You'll see a list of your Security Groups. The "Security group ID" column shows the IDs in the format "sg-xxxxxxxxxxxxxxxxx".

# How to get the subnet ID?
1. Go to the VPC Dashboard in the AWS Management Console.
2. In the left navigation pane, click on "Subnets".
3. You'll see a list of your Subnets. The "Subnet ID" column shows the IDs in the format "subnet-xxxxxxxxxxxxxxxxx".

# How to create AMI from existing EC2
Creating an Amazon Machine Image (AMI) from an existing Amazon EC2 instance is a common task for backups, cloning instances, or disaster recovery. An AMI is an archival copy of an instance that includes the configuration, operating system, and data of the instance. Here are the steps to create an AMI from an EC2 instance using different methods.

1. Log in to AWS Management Console: Navigate to the EC2 Dashboard.
2. Select the Instance: Choose the EC2 instance you want to create an AMI from.
3. Create Image: Click on Actions, navigate to Image and templates, and select Create image.
4. Provide Details: Enter the necessary details such as the name and description for your AMI.
5. Create Image: Click Create Image to initiate the process

For automation, you can use a Bash script with AWS CLI to create an AMI. Here is a sample script:
```
#!/bin/bash
# Prompt for the EC2 Instance Name and AMI Name
read -p "Enter the EC2 Instance Name: " instance_name
read -p "Enter the AMI Name: " ami_name

# Find the instance ID based on the given instance name
instance_id=$(aws ec2 describe-instances --filters "Name=tag:Name,Values=$instance_name" --query 'Reservations[*].Instances[*].InstanceId' --output text)

# Check if the instance ID was found
if [ -z "$instance_id" ]; then
echo "No instance found with name: $instance_name"
exit 1
fi

echo "Creating AMI from instance: $instance_id"

# Create an image (AMI) from the instance and capture the AMI ID
ami_id=$(aws ec2 create-image --instance-id $instance_id --name "$ami_name" --query 'ImageId' --output text)
echo "AMI creation initiated for instance $instance_id with AMI ID $ami_id"

# Wait for the AMI to be available
echo "Waiting for AMI to become available..."
aws ec2 wait image-available --image-ids $ami_id

# Additional delay to ensure snapshots are registered
echo "Waiting for snapshots to be registered..."
sleep 60

# Retrieve the snapshot IDs from the block device mappings of the AMI
snapshot_ids=$(aws ec2 describe-images --image-ids $ami_id --query 'Images[*].BlockDeviceMappings[*].Ebs.SnapshotId' --output text)

# Check if there are snapshots to rename
if [ -z "$snapshot_ids" ]; then
echo "No snapshots found for AMI: $ami_id"
exit 1
fi

# Rename each snapshot to use the AMI name
for snapshot_id in $snapshot_ids; do
echo "Renaming snapshot: $snapshot_id to $ami_name"
aws ec2 create-tags --resources $snapshot_id --tags Key=Name,Value="$ami_name"
done

echo "Snapshots associated with AMI $ami_id have been renamed to $ami_name"

```

# How to get AWS Access Key/Secret
1. Sign in to the AWS Management Console.
2. Click on your account name in the top right corner, then click "Security credentials".
3. If prompted, click "Continue to Security credentials".
4. Scroll down to the "Access keys" section.
5. Click "Create New Access Key".
6. You'll see a message saying "Your security credentials have been created successfully". Click "Show Access Key" to reveal the access key ID and secret access key.
7. Download the key file and store it securely. This is the only time you'll be able to view or download the secret access key.

For an IAM user:

1. Sign in to the AWS Management Console.
2. Navigate to the IAM dashboard.
3. Click on "Users" in the left navigation pane.
4. Select the IAM user for which you want to create an access key.
5. Go to the "Security credentials" tab.
6. Under "Access keys", click "Create access key".
7. Download the key file or copy the access key ID and secret access key.
