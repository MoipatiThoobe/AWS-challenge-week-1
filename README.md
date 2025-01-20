# AWS-challenge-week-1
Week 1 of the 12 weeks of AWS challenge

The first week of this challenge focused on the AWS compute services. Compute services allow you to develop, deploy, run and scale their applications and worksloads in the cloud. AWS compute services have the following characteristics: 
* Right compute for your workloads
* Accelerate from idea to market
* Offer built in security
* Flexibility to optimize costs
* Provide compute resource where you need it

## EC2 Windows Hands on Lab

Amazon EC2 provides scalable computing capacity in the AWS cloud. Using EC2 eliminates the need to invest in hardware upfront, so that you can develop and deploy applications faster. EC2 can be used to launch as many servers as you need, configure security and networking and manage storage. EC2 enables you to scale up or down to handle changes in requirements or spikes in popularity. 

Step 1: Create a new key pair

1.1 Sign into the AWS management console and open the EC2 console

1.2 Click on **Key pairs** in the **Network & Security** section and then click the **Create key pair** button

1.3 Enter the name for the key pair. Keep the default **Key pair type** RSA and **Private key** file format .pem and click the **Create key pair** button. 

1.4 The page will download the key pair file to the local drive. Follow the instructions to save the file to the default download location. 

<img width="950" alt="1" src="https://github.com/user-attachments/assets/14bbd5d1-b839-418d-9057-a9fff5f17223" />

Step 2: Launch a web server instance

2.1 Open the EC2 console

2.2 Click on the **Launch Instance** button

2.3 Create a name for the instance. In the **Amazon machine Image** section, choose **Windows** and then choose Microsoft Windows Server 2022 Base

2.4 Select **t3.medium** in Instance type section

2.5 Select the key pair that you created previously 

2.6 Click the **Edit** button in **Network settings** section. Check the default VPC and subnet, set **Auto-assign public IP** to Enable. Select **Create security group**, create a security group name and description. For security group rule 1, set type to rdp and Source type to My IP. Select **Add security group rule**. For **Security group rule 2** set Type to HTTP and Source type to My IP. 

2.7 Expand the **Advanced Details** tab at the bottom of the screen and configure the **Metadata version** to V1 and V2 (token optional)

2.8 Enter the following code in the **User Data** field

```
<powershell>
Install-WindowsFeature -name Web-Server -IncludeManagementTools

$instanceId   = (Invoke-WebRequest -Uri  http://169.254.169.254/latest/meta-data/instance-id -UseBasicParsing).content
$instanceAZ   = (Invoke-WebRequest -Uri  http://169.254.169.254/latest/meta-data/placement/availability-zone -UseBasicParsing).content

New-Item -Path C:\inetpub\wwwroot\index.html -ItemType File -Force
Add-Content -Path C:\inetpub\wwwroot\index.html "<title>Windows EC2 Lab</title>"
Add-Content -Path C:\inetpub\wwwroot\index.html "<font face = `"Verdana`" size = `"5`">"
Add-Content -Path C:\inetpub\wwwroot\index.html "<center><img src=`"https://d0.awsstatic.com/logos/powered-by-aws.png`" alt=`"Powered by AWS Cloud Computing`"/><center>"
Add-Content -Path C:\inetpub\wwwroot\index.html "<center><h1>Windows EC2 Lab</h1></center>"
Add-Content -Path C:\inetpub\wwwroot\index.html "<hr>"
Add-Content -Path C:\inetpub\wwwroot\index.html "<center> <b>EC2 Instance Metadata</b> </center>"
Add-Content -Path C:\inetpub\wwwroot\index.html "<center> <b>Instance ID:</b> $instanceId </center>"
Add-Content -Path C:\inetpub\wwwroot\index.html "<center> <b>AWS Availablity Zone:</b> $instanceAZ </center>"
Add-Content -Path C:\inetpub\wwwroot\index.html "</font>"
</powershell>

```
2.9 Verify your that your selections are correct and then choose **Launch instance**

2.10 Click **View Instances** button. Once the instance is launched, click the checkbox next to the web server to view details about the EC2 instance

<img width="955" alt="2" src="https://github.com/user-attachments/assets/d634e80d-f897-41bd-b3e1-46d067161756" />

2.11 Open a new browser tab and browse the web server by entering the EC2 instance's Public IPv4 DNS name into the browser

<img width="956" alt="2 1" src="https://github.com/user-attachments/assets/c953f7dd-6206-4822-b984-1aef38babac4" />

Step 3: Connecting to your Windows Instance

3.1 On the Instances page, right click the instance and select **Connect**

3.2 Click the **RDP client** tab and then click **Get password**

3.3 On the next screen, click **Upload private key file** and select the private key that you downloaded previously and click **Decrypt password**

3.4 Copy the Adminstrator password and start the RDP application and connect to the hostname of the instance

Here is a screenshot of the RDP to show that the session has been established and the instance is successfully connected

<img width="953" alt="3" src="https://github.com/user-attachments/assets/342ad791-eed7-47c9-b2c5-84003fb12af8" />

Step 4: Clean up the resources

4.1 Open the EC2 instance state menu and select **Terminate instance**

4.2 Confirm that you want to terminate the instance by clicking the **Terminate** button

In this Lab I created a new key pair, launched a web server instance and connected to the instance using Remote Desktop. 


## EC2 Auto Scaling Hands On Lab

AWS Auto Scaling monitors your applications and automatically adjusts capacity to maintain steady, predictable performance at the lowest possible cost. Using Auto Scaling makes it easy to setup application scaling for multiple resources across multiple services in minutes. Auto scaling makes scaling simple with recommendations that allow you to optimize performance, cost or balance between them. With AWS Auto scaling, your applications will always have the right resources at the right time. 

Lab Prerequisites

We will need to create an AMI (Amazon Machine Image) for our Auto Scaling group but first we need to setup a web host. The AMI will be generated from the instance and then auto-scale the instance behind a load balancer. 

Step 1: Download and Launch the CloudFormation template

1.1 Download the CloudFormation template (you can find the template in the repo) and save it to your local hard drive

1.2 Open the CloudFormation serice on AWS

1.3 Select the **Create stack** button and then select **With new resources (standard)**

1.4 Under the "Specify template" section, select **Upload a template file** and then select the **Choose file** button. Select the template that you downloaded previously and then click on the **Next** button

1.5 On the Specify stack details page
* Create a stack name
* Leave "AmiID" as the default value
* Under "InstanceType" select the **t2.micro**
* Under "MyIP" input the IP address of your local machine followed by a /32
* Under "MyVPC" select the VPC that you want to use or you can use the default VPC
* Under "PublicSubnet" select a subnet with your VPC that has internet access

1.6 Once you have entered all of those details, click on **Next**. On this page, you can leave everything as default and click on **Next** again

1.7 On the "Review and create" page, review your settings and click **Submit** start building the web server

<img width="959" alt="1" src="https://github.com/user-attachments/assets/35e25ec4-9da1-4741-bc86-b88cb297059c" />

Step 2: Confirm the successful setup of the instance

2.1 Navigate to the Ec2 service console and select **Instances** from the left hand menu

<img width="946" alt="2" src="https://github.com/user-attachments/assets/f7646e3c-913c-43c5-9bde-3a3d5f945c63" />

2.2 Select the instance and copy the "Public IPv4 DNS" address and paste the address into a new tab in the browser

<img width="952" alt="3" src="https://github.com/user-attachments/assets/7fde7083-7b29-47f5-8225-22150c35fe3b" />

Step 3: Generate a custom AMI of the web server 

3.1 Open the EC2 console and **Right click** the web server instance. Choose **Create image** from the context menu

3.2 On the "Create Image" page, put in an image name and a description. Leave the Instance volumes as default and then click **Create Image**

3.3 After a few minutes the AMI will be created

<img width="956" alt="4" src="https://github.com/user-attachments/assets/32bc829c-5adc-4639-bf6b-6bc76cec4483" />

Step 4: Create a new security group for the Auto Scaling group

4.1 Open the EC2 console, on the left hand menu under "Network & Security" select **Security Groups**

4.2 Click on the **Create security group** button

4.3 Name the security group, you can also use the same name for the description. Select the same VPC that was used when creating the EC2 web server using CloudFormation.

4.4 Leave the inbound rules and outbound rules as is and click on **Create security group**

<img width="958" alt="5" src="https://github.com/user-attachments/assets/bd46a63c-a508-4c2f-9025-7a2b5e916f39" />

The prerequisite steps are now completed

Step 1: Create a Launch template

1.1 Navigate to the EC2 Service Console and select **Launch Templates**

1.2 Click on the **Create launch template** button

1.3 On the "Create launch template" page do the following:

* Create a launch template name
* Template version description: this is optional
* Auto scaling guidance: **Check the box** to provide guidance
* "Launch Template Contents" defines the parameters for the instances in the ASG (Auto Scaling group)
  * AMI: Select "My AMIs" and "Owned by me". Search for the AMI that was created earlier and select it
  * Instance type: **t2.micro**
  * Key pair: Select the key pair that was created in the first lab
  * Networking settings:
    * Subnet: don't include in launch template
    * Firewall (security groups): Select "Select existing security group" and then select the security group that was created during the prerequisite
  * Configure storage: leave as default
  * Resource tags: None
  * Advanced details: select the arrow to expand "Advanced details" and under "Detailed CloudWatch monitoring" select **Enable**. Leave everything else as default.

1.4 Check that the configurations are correct and click **Create launch template**, then click **View launch template**

<img width="958" alt="6" src="https://github.com/user-attachments/assets/cfc952b6-0736-411c-ab32-d9245a8b9895" />

Step 2: Create a target group

2.1 Open the EC2 service console and in the left hand menu select **Target groups** under "Load balancing"

2.2 Select the **Create target group** button

2.3 Under the "Choose a target type" section, make sure that **Instances** is selected

2.4 Under the "Target group name" enter a name for the target group

2.5 Make sure that IPv4 and the VPC that was used in the CloudFormation deployment is selected

2.6 Under "Health checks" make that **HTTP** is selected and for "Health check path" enter /health

2.7 Expand the **Advanced health check settings** and update the thresholds with the following:

* Healthy threshold: 2
* Unhealthy threshold: 5
* Timeout: 20

2.8 Leave the rests as defaults and select the **Next** button

2.9 Leave the "Register targets" page as is and click the **Create target group** button

Step 3: Create an Auto Scaling Group

3.1 Open the EC2 Service console and select **Auto scaling groups** in the left navigation pane

3.2 Select the **Create an Auto Scaling group** button

3.3 Give the Auto scaling group a name and on the "Launch template" section, select the launch template that was previously created and click **Next**

3.4 Configure the following settings and then click "Next"

Network
* VPC: Select the VPC that you selected earlier for the Launch template
* Availability zones and subnets: select the subnets where you would like the ASG to use to create hosts
* Availability zone distribution: Balanced only

3.5 Specify load balancing and health checks:
* Load balancing: Attach to a new load balancer
* Load balancer type: Application load balancer
* Load balancer name: create a new for the load balancer
* Load balancer scheme: Internet facing
* Networking mapping: The AZs and subnets that you selected in the previous step
* Listeners and routing: Keep the Port as **80** and the target group was previously created from the "Default routing (forwarded to)"
* Health checks and additional settings: leave as default and select **Next**


3.6 Configure the group size and scaling policies with the following and then select **Next**:
* Group size:
  * Desired capacity: 1
* Scaling:
  * Min desired capacity: 1
  * Max desired capacity: 5
  * Select **Target tracking scaling policy**
  * Metric type: **Average CPU utilization**
  * Target value: 25

3.7 Select on the Add notifications page

3.8 Add a tag with the following and then select **Next**
* Key: Name
* Value: name of the auto scaling group

3.9 Review the settings and then select **Create Auto Scaling group**

<img width="955" alt="7" src="https://github.com/user-attachments/assets/feb0fca4-e8c6-4260-839b-accbd1c3682b" />

Step 4: Creating a Load Balancer Security group

4.1 Navigate to the EC2 service console and on the left hand menu select **Security Groups**

4.2 Enter the following details:
* Security group name
* Description
* VPC: select your VPC

4.3 Inbound rules:
* Click on the **Add rule** button
* Type: HTTP
* Source: Custom : Input your public IP address followed by /32

4.4 Outbound rules:
* Change the rule with the "Type" of **All traffic** to **HTTP**
* Under "Destination" select **Custom** and in the field select the security group that you created for the auto scaling group

4.5 Select **Create security group** button when finished

<img width="951" alt="8" src="https://github.com/user-attachments/assets/5069c1d4-89ee-4a98-bb83-b0b28934de76" />

4.6 On the EC2 service page left side menu find "Load balancing" and select **Load Balancers**. Select the load balancer you created and make sure that the state is "Active"

4.7 On the load balancer "Details" page, select **Security** tab and then select the **Edit** button

4.8 Remove the auto scaling group security group and select the load balancer security that we just created in the "Security groups" dropdown

4.9 Select the **Save changes** button when finished

Step 5: Add Inbound Rule to the Auto Scaling Security Group

5.1 On the EC2 service and select **Security Groups**

5.2 Select the auto scaling security group

5.3 Select the **Inbound rules** tab and click **Edit inbound rules** button and then the **Add rule** button

5.4 From the "Type" drop down select HTTP. Under "Source" select custom and in the field specify the Load balancer security group as the "Source"

<img width="950" alt="9" src="https://github.com/user-attachments/assets/2dde698f-8b16-41ae-9846-14b5ee443cd3" />

Step 6: Test the Auto Scaling group

6.1 Open the Load Balancers page and under the "Description" tab, copy the DNS name and paste it into a web browser

<img width="955" alt="10" src="https://github.com/user-attachments/assets/f2bf96db-710b-437d-ba98-0f8d686107de" />

6.2 At the botton of the web page, click the **Start CPU Load Generation** link. When the CPU load reaches 25% for a sustained period, the Auto scaling policy will start creating new instances to meet demand. 

6.3 In the "Instances" sections we will see new instances being created by the Auto Scaling Group

<img width="948" alt="11 3" src="https://github.com/user-attachments/assets/ee2bdda5-68d0-4c68-a1e7-d5085dbf12e7" />

6.4 Once a number of new instances have successfully started, refresh the web broswer of the web host. The Instance ID, Availability zone and Private IP will change as the load balancer distributes the requests across the auto scaling group.

<img width="939" alt="12 1" src="https://github.com/user-attachments/assets/a727aca5-a381-464b-b1a2-c40a371e0e85" />

<img width="946" alt="12 2" src="https://github.com/user-attachments/assets/b77f58f0-7bb5-4309-bcc4-eccd652b4234" />

<img width="950" alt="12" src="https://github.com/user-attachments/assets/fcdcfc0c-31ff-444b-a8d2-c3201ea1d80f" />

Step 7: Clean up resources

7.1 Delete the Load balancer

7.2 Delete the Target Group

7.3 Delete the Auto Scaling Group

7.4 Delete the Launch Template

7.5 Delete the Security Group

7.6 Delete the CloudFormation Stack

In this lab, I learned how to create an AMI, included it in the setup of the Launch Template, created an EC2 Auto scaling group behind an Application Load Balancer.

## VPC Hands on Lab

Amazon Virtual Private Cloud (VPC) enables you to launch AWS resources into a virtual network that you have defined. You have complete control over your virtual networking environment, including selection of your own IP address range, creation of subnets and configurations of route tables and network gateway. 

Step 1: Create a VPC

1.1 Open the VPC console and click **Create VPC**

1.2 Under VPC settings, select **VPC and more**. Under Name tag auto generation, type a name for the VPC and set the CIDR block to the default value of 10.0.0.0/16

1.3 Under Number of Availability zones choose 1 AZ amd expand **Customize AZs**. Select First Availability Zone as an A zone. 

1.4 Select the number of public subnets as 1. Select 0 under Number of Private subnets and expand Customize subnets CIDR blocks, set the CIDR block to 10.0.10.0/24!

1.5 Choose **None** for Nat gateways and VPC endpoints and then click the **Create VPC** button at the button

<img width="951" alt="1" src="https://github.com/user-attachments/assets/88b4ae90-b77f-4685-8cc8-ac9d2ff60184" />

1.6 In the left side bar, select **Subnets** and click the refresh button if the newly created subnets is not listed

Step 2: Creating additional subnets

2.1 Click the **Create Subnet** button

2.2 For VPC id, select the VPC that we created previously

2.3 In **Subnet settings** section, enter a subnet name and choose a C availability zone. Click the **Create subnet** button when finished. 

<img width="952" alt="2" src="https://github.com/user-attachments/assets/d128b7c7-5d70-4018-961f-42f298f5963f" />

Step 3: Edit the routing table

3.1 Select **public subnet c** and click **Actions** button in the **Subnet** menu and choose **Edit route table association**

3.2 Select the route table that is associated with the VPC and verify that there is a route via the Internet Gateway and click **Save**

<img width="955" alt="3" src="https://github.com/user-attachments/assets/b6589f3d-26fd-4179-a410-087fcc3d391b" />

Step 4: Create a security group 

4.1 Verify the region code that correlates to the region that the console is in

4.2 In a seperate browser, navigate to the following link (https://ip-ranges.amazonaws.com/ip-ranges.json) and search the JSON for the EC2_INSTANCE_CONNECT and locate the region code that correlates to the region your console is in. Record the ip_prefix value, it will be used for the SSH security group rule source

4.3 Click **Security groups** and then click the **Create security group** button

4.4 Enter a name and description for the security group, select the VPC that we created previously

4.5 Add the following 2 Inbound rules and leave the Outbound as the default and click **Create security group** button when finished:
* Type: All ICMP - IPv4 Source: Anywhere
* Type: SSH Source: The ip_prefix value that we recorded earlier

<img width="956" alt="4" src="https://github.com/user-attachments/assets/4d8b0248-db00-4a59-959a-a06c2d17d5d9" />

Step 5: Test EC2 instances network communication

5.1 Open the **EC2 Dashboard** and click **Launch instance**

5.2 Create a name for the EC2 instance and leave the default settings for Amazon Machine Image

5.3 Select **t2.micro** as the instance type and select proceed without a key pair

5.4 Click the **Edit** button in Network settings:
* Select the VPC that was created previously
* Select the subnet that was created with the VPC in zone A
* Enable the Auto-assign Public IP

5.5 In the Security Group section, choose **Select existing security group** and select the security group that was previously created

5.6 Leave all other values as default and click **Launch Instance**

<img width="956" alt="5 1" src="https://github.com/user-attachments/assets/2d68c00b-2dd9-41f4-afd0-c0f5bdb537f0" />

Step 6: Create a new EC2 instance in Public Subnet C

6.1 Repeat the previous steps to launch another EC2 instance
* Use a different name of the instance
* Select the subnet that was created in zone C

<img width="953" alt="5 2" src="https://github.com/user-attachments/assets/2a5091d8-3fde-4de9-84ca-fcc51a6c86e9" />

Step 7: Validate connectivity between the EC2 Instances

7.1 In the EC2 console, select the 2nd Instance and note down the Public IPv4 address

7.2 Select the 1st instance and click the **connect** button

7.3 Select the **EC2 Instance Connect** tab, leave the default values and click **Connect**

7.4 In the Linux terminal, type ping <Public IPv4 address of the 2nd instance>. A successful ping response confirms cross-Availability Zone Ec2 communication over ICMP

<img width="941" alt="6" src="https://github.com/user-attachments/assets/95e0a1cb-fee7-4392-8e5d-1c597db1a6c8" />

7.5 Verify internet connectivity by entering the command curl example.com into the terminal

<img width="946" alt="7" src="https://github.com/user-attachments/assets/65ab1c82-2b62-481c-b4d8-3f5edac5ae2e" />

Step 8: Reachability analyzer for troubleshooting

8.1 Open VPC Reachability analyzer 

8.2 Click **Create and analyze path**

8.3 Provide the Name of the Path configuration, Path Source, Path Destination and Protocol and click the **Create and analyze path** button

8.4 Analysis will show **Pending** for a few minutes, click the refresh button

<img width="957" alt="8" src="https://github.com/user-attachments/assets/22976141-9012-4fd3-9827-cb92d58de5d0" />

8.5 Scroll down to view the result. The report will display the components used in the network path for the EC2 instance to reach the Internet Gateway

<img width="959" alt="9" src="https://github.com/user-attachments/assets/05aec89f-f17f-4380-a9aa-b9051ce26115" />

Step 9: Clean up resources

9.1 Terminate the EC2 Instance

9.2 Delete the VPC

## IAM Hands On Lab

AWS Identity and Access Management (IAM) is a web service that helps you securely control access to AWS resources. You use IAM to constrol who is **authenticated** (signed in) and **authorized** (has permissions) to use resources.

Step 1: Launch EC2 instances with Tags

1.1 Open the EC2 service console, click on **EC2 Dashboard** and click on **Launch instances**

1.2 In the Namw field, enter the value **prod-instance** and click on **Add additional tags**

1.3 Click **Add tag** then in **Key** enter **Env** and **Value** enter **prod**

1.4 Select the default AMI and then select **t2.micro** in the Instance Type

1.5 In **Key pair (login)** select proceed without a key pair and then click **Launch instances**

1.6 Repeat the above steps to create another EC2 instance for the development environment, use different Name and Env tags

<img width="957" alt="1" src="https://github.com/user-attachments/assets/4c5c6de2-e131-4db3-908b-f8d20ee711d0" />

Here is a screenshot of the tags for the prod instance

<img width="835" alt="1 1" src="https://github.com/user-attachments/assets/b85bb166-330f-4e5a-93f3-3eeaa33558c0" />

Here is a screenshot of the tags for the dev instance

<img width="838" alt="1 2" src="https://github.com/user-attachments/assets/6dacb6fd-4c6a-4fd0-bcd4-26cc6b4ec58d" />

Step 2: Create AWS IAM identities

2.1 Open the IAM console, to generate a console login Sign-in URL, click the customize button

2.2 Input an account alias and click the **create alias** button

2.3 Click **Policies** and then click **Create Policy** button 

2.4 Click the JSON tab and paste the following policy, click the **Next: Tags** when finished

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "ec2:*",
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "ec2:ResourceTag/Env": "dev"
                }
            }
        },
        {
            "Effect": "Allow",
            "Action": "ec2:Describe*",
            "Resource": "*"
        },
        {
            "Effect": "Deny",
            "Action": [
                "ec2:DeleteTags",
                "ec2:CreateTags"
            ],
            "Resource": "*"
        }
    ]
}
```

2.5 Keep the default settings on the next step, click **Next:Review** button. Write a policy name and a description for the policy. Next click the Create policy button. 

2.6 Click **User groups** and then click the **Create group** button

2.7 Type in a **User group name** and select the policy that was just created in **Attach permissions policies - Optional** section

2.8 Click **Users** and then click the **Add users** button. Type in a **User name** and allow two access types, both Programmatic access and AWS Management console access. And then, select **Custom password** and type in the password you want, then click Next

2.9 Select the user group that was just created and click Next, skip the Add user page and move to the next step

2.10 Click **Create user** button. Download the csv file to get the Access key ID and Secret access key. 

2.11 Click Sign in and log in as the new user that was just created. 

Step 3: Test the access for resources

3.1 Open the EC2 console and click **Instances** menu. Select the 'prod-instance' and click **Instance state** button and **Stop instance** button and click **Stop**

<img width="959" alt="2" src="https://github.com/user-attachments/assets/066a60a4-a2cb-4fdf-9fb4-a047a2988e0d" />

A warning sign is appearing because the user does not have permissions to perform the stop EC2 instance operation. 

3.2 Select the instance named 'dev-instance' and click **Instance state** button and click the **Stop instance** button

<img width="956" alt="2 1" src="https://github.com/user-attachments/assets/b1fcf3a9-fc73-44bc-a433-202c44f66ccd" />

The instance has been successfully stopped because the user has permission to perform the stop EC2 instance operation

Step 4: Assign IAM Role for EC2 Instance and Test the access

4.1 Re-login into AWS using the AWS account with administrator role 

4.2 Open the S3 console and click **Create bucket** button

4.3 Create a unique bucket name, leave the remaining settings intact and click create bucket button

4.4 Upload any file in the S3 bucket

4.5 Create one more bucket and upload any file into the S3 bucket

<img width="959" alt="3" src="https://github.com/user-attachments/assets/ef865431-ba46-4e6a-a64f-09d1bf157a74" />

4.6 Open the IAM console, go to policies and click **Create policy**

4.7 Click JSON next to the policy editor and paste the following code

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
        "Action": ["s3:ListAllMyBuckets", "s3:GetBucketLocation"],
        "Effect": "Allow",
        "Resource": ["arn:aws:s3:::*"]
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:Get*",
                "s3:List*"
            ],
            "Resource": [
                "arn:aws:s3:::iam-test-user_name/*",
                "arn:aws:s3:::iam-test-user_name"
            ]
        }
    ]
}
```

4.8 Click **Next** and create a name for the policy. Click **Create policy** to attach the policy to the IAM role

<img width="959" alt="4" src="https://github.com/user-attachments/assets/3fb9c3eb-289f-4b8c-8a04-f53f02ab8ab7" />

4.9 Go to the **Roles** and click **Create role** button, select AWS service as the Trusted entity type and choose **EC2** for the service / use case

4.10 Search for the policy that was just created and click the check box to add a permission

4.11 Click **Next** and create a name for the role, click **Create role**

<img width="956" alt="5" src="https://github.com/user-attachments/assets/abac8712-2031-44dd-9714-3065489ba171" />

4.12 Open the EC2 console, select the 'prod-instance' and then click the **Connect** button

4.13 Connect to the instance by using EC2 instance connect and click connect. In the terminal, type the following command: aws s3 ls

<img width="950" alt="6" src="https://github.com/user-attachments/assets/823c5a23-56eb-430c-955d-a002279cdb7a" />

This command is producing an error because the EC2 instance does not have the permissions to access the S3 buckets

4.14 Open the Instances page, select the 'prod-instance'. Click the **Actions** button and select **Security** and click **Modify IAM role**

4.15 Select the IAM role that was previously created and click **Save** button to attach the IAM role to the EC2 Instance

<img width="830" alt="7" src="https://github.com/user-attachments/assets/387a4025-e757-4fc2-a44e-182d7cb83a25" />

4.16 Connect to the 'prod-instance' again and type the aws s3 ls command into the terminal

<img width="953" alt="8" src="https://github.com/user-attachments/assets/76efa0e5-f328-4992-9915-243bddc5f52c" />

We can see the S3 buckets because the EC2 instance has the correct permissions through the IAM role that is attached to the instance

Step 5: Clean up resources

5.1 Delete both of the EC2 instances

5.2 Delete all of the User group, User, Role and policies

## Amazon CloudWatch Hands on Lab

Amazon CloudWatch is a monitoring and observability service which provides you with data and actionable insights to monitor your applications, respond to system-wide performance changes, optimize resource utilisation and get a unified view of operation health. CloudWatch collects monitoring and operational data in the form of logs, metrics and events, providing you with a unified view of your resources. 

Step 1: Create a simple notification service (SNS) topic

1.1 Open the Amazon SNS

1.2 Select **Topics** and click **Create topic**

1.3 For type, select **Standard**, type in a name for the topic. Scroll to the bottom of the screen and click **Create topic**

<img width="958" alt="1" src="https://github.com/user-attachments/assets/34e276a9-b236-4ad5-9029-c1e535f07329" />

1.4 Click **Create subscription**. In the Protocol, drop down select **Email** and enter a working email address. Click **Create subscription**

<img width="955" alt="2" src="https://github.com/user-attachments/assets/6175a584-baf1-4ca5-b4a0-7f9d38118772" />

1.5 A verification email will be sent to the address that you entered with the subject "AWS Notification - Subscription Confirmation". Open the email and click the Confirm subscription link.

<img width="695" alt="3" src="https://github.com/user-attachments/assets/60e155c0-c3d9-4c58-913a-ac51a84b07e1" />

<img width="441" alt="4" src="https://github.com/user-attachments/assets/670f0cf7-7e75-4172-a1a9-40a10551b6fe" />

Step 2: Launch an EC2 Instance

2.1 Open the EC2 service console and click **Launch instance**

2.2 Create a name for the instance and select the **Amazon Linux** for Amazon Machine Image (AMI) 

2.3 Select the **t2.micro** instance type

2.4 In the Network settings section, select **Create security group** option and check out all check boxes allowing traffic

2.5 Expand the **Advanced Details** section and paste the following code into the **User Data** field, click the **Launch instance** button when finished. 

```
#!/bin/sh 
yum -y update
amazon-linux-extras install epel -y
yum install stress -y
stress -c 1 --backoff 300000000 -t 30m
```

<img width="953" alt="5" src="https://github.com/user-attachments/assets/873161f4-5bf8-45ce-9e32-a8e3cabb32df" />

Step 3: Configure a CloudWatch Alarm

3.1 In the EC2 console, click the checkbox next to your server. Click Actions > Monitor and troubleshoot > Manage detailed monitoring

3.2 Check the **Enable** button under Detailed monitoring to provide monitoring data at a 1 minute interval and click Save

3.3 Click the details tab and copy the **Instance ID**

3.4 Click on Actions > Monitoring and troubleshoot > Manage CloudWatch alarms

3.5 Select **Create an alarm**, under Alarm notification, select the SNS topic that was previously created

3.6 In the **Alarm thresholds** section, set the values to trigger an alarm when CPU utilization is greater than 60% and click **Create**

<img width="957" alt="5 1" src="https://github.com/user-attachments/assets/46cf0320-5a3f-4592-a2c1-c71bca680601" />

3.7 Open CloudWatch and select **All alarms** and check the state the of alarm using the **instance-id** to filter the alarms

<img width="952" alt="6" src="https://github.com/user-attachments/assets/2751b509-02be-47ac-8318-40a559471587" />

3.8 In the CloudWatch console, select **Metrics**. Select the **All Metrics** tab and paste the **Instance ID** into the filter

3.9 Click on per-instance Metrics and add an additional filter, select **CPUUtilization** metric

<img width="951" alt="7" src="https://github.com/user-attachments/assets/27858b00-ff3b-4222-a6b7-9ca6e063fd46" />

3.10 Click on **Graphed metrics** button and change the Period to 1 min. Change the graph interval to a custom value of 30min and select Auto refresh of 1 min. 

3.11 Select All alarms and you should be able to see the configured alarms. We will also be receiving an email when the Alarm is triggered.

<img width="951" alt="8" src="https://github.com/user-attachments/assets/0442e6d3-78e0-42f8-8141-00be10a3f739" />

<img width="703" alt="9" src="https://github.com/user-attachments/assets/054c4fa7-8bcc-4d67-aa21-9b6b04e8c225" />

Step 4: Cleam up resources

4.1 Delete the CloudWatch Alarm

4.2 Delete the EC2 Instance

4.3 Delete the SNS Topic
































































 
    
























