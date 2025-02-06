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

2.8 Enter the code in the **User Data** field. The code is stored in a file called userdata1.txt

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

2.4 Click the JSON tab and paste the JSON code. The code is stored in a file named policy.json, click the **Next: Tags** when finished

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

4.7 Click JSON next to the policy editor and paste the json code. The code is stored in a file called policy2.json. 

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

2.5 Expand the **Advanced Details** section and paste the code into the **User Data** field. The code can be found in a file called userdata2.txt. Click the **Launch instance** button when finished. 

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

## Amazon RDS MySQL Hands on Lab

Amazon RDS is a web service that makes it easy to set up, operate and scale relational database in the cloud. 

Prerequisite:
* Launch an EC2 web server instance with a security group that allows TCP traffic on port 80

<img width="957" alt="1" src="https://github.com/user-attachments/assets/0b661867-604a-46e6-83bc-7efc39f30a9b" />

Step 1: Create VPC Security grouo

1.1 Open the VPC dashboard, click **Security Groups** and then click **Create Security Group** button

1.2 Type in a security group name and description, use the same VPC that you used when creating the Ec2 instance

1.3 Under "Inbound Rules", click the **Add rule** button. Add a new inbound rule for the EC2 server:
* Type: **MySQL/Aurora(3306)**
* Protocol: **TCP(6)**
* Source: **The security of the EC2 instance**

1.4 Scroll down and click the **Create security group** button

<img width="947" alt="2" src="https://github.com/user-attachments/assets/243858d1-1999-4f61-b27a-fc5a9a13847a" />

Step 2: Launch an RDS instance

2.1 Open the Amazon RDS console and click on **Create database**

2.2 Under **Choose a database creation method**, select Standard option

2.3 Select **MySQL** in Engine options

2.4 Select **Free Tier** in Template options

2.5 In the **Settings** section, create an DB Instance identifier, Master username and Master Password

2.6 In **Instance configuration** section, select burstable classes-db.t3.micro

2.7 In the **Storage** section, select General Purpose SSD as the Storage Type

2.8 In the **Connectivity** section
* VPC: Default VPC
* Subnet group: No
* VPC Security group: Select the security group that was previously created
* Database port: 3306

2.9 For the **Database authentication**, select **Password Authentication**

2.10 Expand **Additional Configuration**, create an initial database name and select default.mysql8.0 for **DB Parameter group and option group**

2.11 In the Backup section, select the following:
* Check on **enable automatic backups**
* Provide **Backup retention period** as 7 days
* **Backup Window: No preference**
* Leave the rest as default

2.12 Review the settings and then click **Create database**

<img width="955" alt="3" src="https://github.com/user-attachments/assets/3d471944-ef45-4c47-b9bd-7ccbed470bb6" />


Step 3: Save RDS credentials

3.1 Open AWS Secrets Manager console and click **Store a new secret**

3.2 Under **Secret type**, select **Credentials for Amazon RDS database**. Provide the username and password that you to create the database. 

3.3 Under **Database**, select the database that was just created and click **Next**

3.4 Create a name for the secret and click **Next**

3.5 Leave the **Secret rotation** with its default values and click **Next**. Review the settings and click **Store**

<img width="955" alt="4" src="https://github.com/user-attachments/assets/a7bf78f0-cfe1-4744-87e2-2b2c7406e170" />

Step 4: Access RDS from the EC2 instance

4.1 Create an IAM Instance Profile and attach it to the EC2 Instance

<img width="948" alt="5" src="https://github.com/user-attachments/assets/36e1dd93-4294-4af3-8932-32cd269502d0" />

<img width="948" alt="6" src="https://github.com/user-attachments/assets/ca25f7a2-8025-477c-9791-49a4951dacf9" />

4.2 Open the IAM console, select **Policies** and then click **Create policy**

4.3 Click **Choose a service**. Type **Secrets Manager** in the search bar and click **Secrets Manager**

4.4 In the **Access level** section, select the arrow next to **Read** and then the check box by **GetSecretValue**

4.5 Click on the arrow next to **Resources** and select **Alls**. Click the **Next** button twice 

4.6 On the **Review Policy** page, create a name for the policy and click **Create policy**

<img width="950" alt="7" src="https://github.com/user-attachments/assets/fea44e9e-7473-447e-b6be-494d298e9479" />

4.7 On the navigation menu, select **Roles** and type the name of the Instance profile in the search box, click the instance profile

4.8 Under **Permission policies**, click **Attach policies**

4.9 Search for the policy that we just created. Check the box and click **Attach policy**

<img width="952" alt="8" src="https://github.com/user-attachments/assets/a79afee3-ccf0-4849-b8d5-4129e7d5f6eb" />

4.10 Open the EC2 console, select the EC2 instance and copy the public IP address

4.11 Open a new tab and navigate to the web server using the public IP address

<img width="948" alt="9" src="https://github.com/user-attachments/assets/261edf57-511e-4394-9047-3bf6e0232d83" />

Step 5: Create an RDS Snapshot

5.1 Navigate to RDS management console, select the RDS instance, click on Instance actions and select **Take a snapshot**

5.2 Create a name for the snapshot and click on **Take a snapshot**

<img width="955" alt="10" src="https://github.com/user-attachments/assets/a891f21c-69fd-4edc-9fd9-f1981d62574d" />

Step 6: Clean up resources

6.1 Delete the RDS instance and its snapshot

6.2 Delete the Ec2 Instance

##Amazon RDS RQL Server Hands On Lab

Prerequisites:
* Launch an EC2 windows server instance with the security group that allows TCP on port 80 and 3389

<img width="956" alt="1" src="https://github.com/user-attachments/assets/8345dccf-49a5-48dc-b53a-e4ec5a47ca46" />

Step 1: Launch an RDS Instance

1.1 Open the Amazon RDS Console and click on **Create database**

1.2 For **Choose a database creation method**, select Easy Create option

1.3 For Engine options, select **Microsoft SQL Server** and for Database instance size, select **Dev/Test**

1.4 Create a DB Instance identifier, Master username and Master password

1.5 Open **Set up EC2 connection - optional** and select **Connect to an EC2 compute resource**. Choose the EC2 windows instance that was created in the prerequisite. 

1.6 Review the settings and click **Create database**

<img width="956" alt="2" src="https://github.com/user-attachments/assets/04a17c0f-0ae6-4d0c-a4ef-01e888a4b264" />

Step 2: Access RDs from the EC2 instance

2.1 Open the Amazon RDS, select **Databases** and select the SQL Server instance that was just created

2.2 On the **Connectivity** tab, copy the endpoint and the port number 

2.3 Connect to the EC2 instance using RDP

<img width="950" alt="3" src="https://github.com/user-attachments/assets/a8eebc56-2406-4bef-8be6-d6dae3caa261" />

2.4 When the remote desktop loads, open Powershell and paste the installer script into the terminal. You can find the script in a file called SSMSinstaller.txt

2.5 Microsoft SQL Server Management Studio will automatically open, leave the location path as default and click install

<img width="950" alt="4" src="https://github.com/user-attachments/assets/1fcdd5ff-393b-462f-b986-0fed8a40e97a" />

Step 3: Connect to your SQL Server DB Instance using SSMS

3.1 Open SQL Server Management Studio (SSMS)

3.2 The Connect to Server dialog box will apear, input the following information:
* Server type: Database Engine
* Server Name: the database DNS name, followed by a comma and the port number
* Authentication: select SQL Server Authentication

3.3 You will be prompted to Login, enter the username and password that you used when creating the database

Step 4: Explore SQL Server DB Instance

4.1 On the File menu, select New and then choose Query with Current connection

4.2 Enter the following SQL query in the query editor: select @VERSION

4.3 Run the query. SSMS will return the SQL Server version of the Amazon RDS DB Instance

<img width="954" alt="5 1" src="https://github.com/user-attachments/assets/184c955f-cfc9-4f73-b109-b75b236e6e05" />

<img width="953" alt="5" src="https://github.com/user-attachments/assets/e8722234-3ca9-4837-8429-01f24ae733a7" />

Step 5: Clean up resources

5.1 Delete the RDS DB instance

5.2 Delete the EC2 instance

## Amazon Elastic File System (EFS) Hands On Lab

Amazon EFS is designed to provide serverless, fully elastic file storage that lets you share file data without provisioning or managing storage capacity and performance. EFS automatically grows and shrinks as you add and remove files with no need for management or provisioning. 

Step 1: Create a VPC with two Public subnets

1.1 Open the VPC console and click the **Create VPC** button

1.2 In the VPC settings, fill in the following information, click **Create VPC** when finished:
* **Resources to create** : VPC and more
* **Name tag auto-generate**: create a name for the VPC
* **IPv4 CIDR Block**: Leave this as 10.0.0.0/16
* **Tenancy**: leave this as default
* **Number of AZs**: 2
* **Number of Public subnets**: 2
* **Number of Private Subnets**: 0
* **Nat Gateway**: None
* **VPC EndPoints**: None
* **DNS Options**: leave both as checked

<img width="953" alt="1" src="https://github.com/user-attachments/assets/e85be587-420a-401e-8499-4af468ecf470" />

Step 2: Creating a security group for EC2

2.1 Open the EC2 Service console

2.2 Scroll down to **Network and Security** and click on **Security Groups**

2.3 In the security groups console, click **Create Security group** button

2.4 Create a name and description for the security group

2.5 In the VPC section, select the VPC that was previously created

2.6 Create an **Inbound rule** by selecting **SSH** from the type list. In the Source, select **Anywhere IPV4**. You can optionally add a description for what the rule does.

2.7 Leave the **Outbound rules** as default and click the **Create Security Group** button

<img width="953" alt="2" src="https://github.com/user-attachments/assets/70f4805c-c9f2-4fcd-afb4-75b6fa1795dc" />

Step 3: Create a Security group for EFS

3.1 Repeat steps 2.1 to 2.5

3.2 Create an **Inbound rule** by selecting **NFS** from the type list. In the Source, select **Custom** and then select the security group that was created previously

3.3 Click **Create Security Group**

<img width="955" alt="3" src="https://github.com/user-attachments/assets/fe8f1df6-6f6c-4c38-a338-3a5298547efb" />

3.4 Click on the **Edit Inbound Rules** button

3.5 Click the **Add rule** button and select **NFS** in the Type Dropdown. In the Source, select **Custom** and the **EFS Security group**. Click **Save Rule** when finished

<img width="953" alt="4" src="https://github.com/user-attachments/assets/5c73310f-b6bc-4e58-a868-085ba37a2fc5" />

Step 4: Create the Elastic File System

4.1 Open the EFS console and click on **Create File System**

4.2 When the Popup message appears, click on **Customize*

4.3 Create a name for the file system

4.4 Select the **Regional** storage class

4.5 Check the **Enable automatic backups** box

4.6 Lifecyle Management
* Transition into infrequent access (IA): 30 day(s) since last access
* Transition into archive: None
* Encryption: Uncheck **Enable Encryption of data at rest**

4.7 For Performance settings, select the Throughput mode as **Bursting**, click the **Next** button

4.8 Select the VPC that was created previously 

4.9 Remove the Security groups

4.10 In the Availability zone section, click the **Security group** drop down and add the EFS security group. Do the same for the second availability zone. 

4.11 Click the **Next** button twice. On the review page, check the settings and then click **Create**

<img width="955" alt="5" src="https://github.com/user-attachments/assets/789a031e-a91c-4502-a3ca-8d7a7004821c" />

Step 5: Create the first EC2 Instance and Mount the EFS drive

5.1 Open the EC2 console and click on the **Launch Instance** button

5.2 Create a name for the file server. In the AMI section, leave the default AMI that is selected.

5.3 Select a **t2.micro** instance type section and select **Proceed without a key pair** in the Key Pair(Login) section

5.4 Click **Edit** in the Network settings section. 
* Select the VPC that was created previously
* Select the first subnet that was created previously
* Enable **Auto-Assign Public IP**
* Click on **Select existing Security Group** and choose both of the security groups that were previously created

5.5 Click **Edit** in the Configure Storage section.
* Make sure that **EFS** is selected and then click on **Add shared file system**
* Uncheck automatically create and attach Security groups
* Check Automatically Mount shared file system by attaching required user data script

5.6 Review the settings and click **Launch Instance** when finished

<img width="957" alt="7" src="https://github.com/user-attachments/assets/3964fd48-7a0c-4875-92b5-04991f1e3332" />

Step 2: Create the second EC2 instance and Mount the EFS drive

2.1 Repeat the previous steps to create an instance but change the following:
* Use a different name for the new instance
* Place the instance in the second subnet that was previously created

<img width="953" alt="8" src="https://github.com/user-attachments/assets/498b4fb7-5ccd-4327-b3ef-4fddc4a89d11" />

Step 3: Connect to our EC2 instances using Instance Connect

3.1 On the Instances page, click the checkbox next to the first instance and click **Connect**

3.2 On the **Connect to instance** page, select **EC2 Instance connect** and click the **Connect** button

<img width="944" alt="9" src="https://github.com/user-attachments/assets/e148fee7-92f5-4194-90e5-05cdd927e7e7" />

3.3 On a seperate tab, repeat the previous steps to connect to the second instance using Instance connect

<img width="959" alt="10" src="https://github.com/user-attachments/assets/84aeb512-fcc0-4521-903f-31a6b085a177" />

Step 4: Create a file on the EFS drive

4.1 Open the tab of the first instance's terminal, input the following command to change directories to the EFS mount: cd /mnt/efs/fs1

4.2 Enter the following command to create a new file on EFS: sudo touch newfile.txt

4.3 Enter the following command to confirm that the file was created: ls

<img width="948" alt="11" src="https://github.com/user-attachments/assets/6e875bd8-afe2-4fd0-a4e5-c67f9d3e92d0" />

Step 5: Demonstrate the EFS mount from the second instance

5.1 Open the tab of the second instance's terminal. Input the following command to change the directory to the mount point: cd /mnt/efs/fs1

5.2 Input the following command to see the files and folders on the mount: ls

<img width="941" alt="12" src="https://github.com/user-attachments/assets/a94a61c4-8676-403d-97ab-c1da90d1eb68" />

5.3 Enter the following command to create a new file: sudo touch SecondNewFile.txt

5.4 Navigate to the first instance's tab and enter the following command to confirm that we can see both files: ls

<img width="947" alt="14" src="https://github.com/user-attachments/assets/2123f210-e875-4aa9-bcad-ca9ac069430e" />

Step 6: Clean up resources

6.1 Delete the EFS drive

6.2 Delete the 2 EC2 instances

6.3 Delete the 2 subnets 

6.4 Delete the VPC

## Amazon S3 Hands On Lab

Amazon Simple Storage Service (Amazon S3) is an object storage service that offers scalability, data availability, security and performance. It can be used to store and retrieve any amount of data, at any time, from anywhere in the world. 

Prerequsite steps:
1. Download the CloudFormation template, the file is called "S3-General-ID-Lab.yaml" and save it to your local hard drive
2. Open the CloudFormation service and select the **Create stack** button and then select **With new resources (standard)**
3. In "Specify template" section, select **Upload a template file** and then select **Choose file** button. Select the template file that was previously downloaded and then click the **Next** button
4. On the Specify stack details page, fill in the following:
* **Stack name**: Create a name for the stack
* **ANID**: leave as default
* **InstanceType**: select t2.micro
* **MyIp**: input the IP address of your machine followed by /32
* **MyVPC**: select the VPC that you want to use to setup the instance
* **PublicSubnet**: select a subnet within the VPC
5. Click on **Next**, the everything on this page as defaults and click **Next** again
6. Review the settings and click on **Submit** to create the web server

<img width="958" alt="1" src="https://github.com/user-attachments/assets/f58feace-6607-4612-95cf-11da9cef170b" />

7. Open the EC2 instance console and select "Instances" on the menu.
<img width="959" alt="2" src="https://github.com/user-attachments/assets/f224414a-5969-4352-8ecc-10725df17b25" />

8. On the Instances console, select the instance and copy the "Public IPv4 DNS"

9. Paste this address into a new tab on the web browser
 
11. <img width="947" alt="2 1" src="https://github.com/user-attachments/assets/d3eb08ed-6351-4fd0-8689-f7e6f8ded12c" />

Step 1: Create a bucket in S3 

1.1 Navigate to the S3 Service console and click the **Create bucket** button

1.2 On the "Create bucket" page, input the following information:
* Confirm that you are using the correct region
* Select **General purpose** bucket type
* Create a unqiue name for the bucket
* On the "Block public access settings for this bucket" section, leave **Block all public access** as checked

1.3 Leave the rest of the settings as default and click on the **Create bucket** button

<img width="952" alt="3" src="https://github.com/user-attachments/assets/56047bf0-438f-41a5-a2c9-5d95970753fb" />

1.4 On the Buckets page, click the name of the bucket that was just created. 

Step 2: Adding objects to the buckets

2.1 On the bucket's overview page, click the **Upload** button and then click the **Add files**. Upload all of the photos stored inside of the zip folder. 

2.2 Once all of the images have been uploaded, click the **Upload** button at the bottom of the page

<img width="956" alt="4" src="https://github.com/user-attachments/assets/b45c76c1-9aac-45c2-be40-bbb5517ccc2c" />

Step 3: Working with objects in the S3 console

3.1 On the bucket overview page, click the **Create folder** button

3.2 Create a name for the folder and then click the **Create folder** button

<img width="953" alt="5" src="https://github.com/user-attachments/assets/c98b31a4-3d60-4510-86fc-f7c0e86570db" />

3.3 Select the "photo7.jpg" object and click on the **Actions** dropdown, and select **Move**

3.4 In the "Destination type" section, select **General purpose bucket** and then click **Browse S3**

3.5 A pop named Destination will appear, select the folder that was previously created and then click **Choose destination**. Now that the destination is set, click on **Move** to move the object. 

<img width="958" alt="6" src="https://github.com/user-attachments/assets/341003e7-8776-47ef-81a4-0fee6897715d" />

Step 4: Accessing objects stored in S3

4.1 Open the IAM Service console and select **Policies** on the menu. Click the **Create policy** button

4.2 On the "Specify permissions" page, select the **Visual** button on the "Policy editor". On the "Select a service" section, select **S3**

4.3 Under "Actions allowed" search bar, enter GetObject and in "Filter actions" put a check next to "GetObject"

4.4 Under "Resources" make sure the **Specific** is selected and then click on **Add ARNs link. 

4.5 A dialogue box will appear, paste the name of the bucket in the "Resource bucket name" field. In the "Resource object name" field, select the check box next to "Any object name" and click the **Add ARNs** button. Click the **Next** button.

4.6 Under "Review and create", create a name for the policy and click the **Create policy** button

<img width="949" alt="7" src="https://github.com/user-attachments/assets/c97a62d7-c849-4054-8bdb-6458ad70d5f1" />

4.7 On the menu, select **Roles** and click the **Create role** button

4.8 Under "Trustes entity type", select **AWS Service** and then under "Use case" select **EC2**

4.9 Under "Choose a use case for the specified service", select **EC2** and then click the **Next** button

4.10 Search for **S3** in the "Filter policies" search bar. Select the policy that was previously created and then click **Next**

4.11 Create a name for the role and then click the **Create role** button

<img width="956" alt="8" src="https://github.com/user-attachments/assets/fca7792b-e4f8-4850-8cb0-7852ced68102" />

Step 5: Attach the role to the EC2 Instance

5.1 Open the EC2 service console and click on **Instances** 

5.2 Find the EC2 instance that was previously created, right click on the instance and select **Security** > **Modify IAM role**

5.3 Select the role that was previously created from the "IAM role" dropdown and click on the **Update IAM role** button

<img width="956" alt="9" src="https://github.com/user-attachments/assets/2f7d3cc1-c2d7-47c8-8954-33b09d980da4" />

5.4 Open the tab where you accessed the EC2 instance on the broswer. Enter the name of the bucket that was created and the region that the bucket was created in and then click the **Submit** button. We are able to see the images that we uploaded in a gallery, which means that EC2 instance has the correct permissions to access the S3 bucket.

<img width="956" alt="10" src="https://github.com/user-attachments/assets/4bc83c06-a64f-4ce6-a910-a79b78d35c0b" />


Steo 6: Enable bucket versioning

6.1 Open the S3 console and click on **Buckets** on the menu. Click the name of the bucket that was previously created and select the "Properties" tab. In the "Bucket versioning" section, click the **Edit** button. 

6.2 Select **Enable** and then click the **Save changes** button

<img width="951" alt="11" src="https://github.com/user-attachments/assets/5a5706f0-23b0-456a-8a25-964d3c5957dc" />

6.3 Return to the "Objects" tab and click the **Upload** button. Go to the images folder that was downloaded and upload the "photo1.jpg" file that is inside of the "V2" folder. 

6.4 Flip the "Show Versions" toggle to display all of the versions of the objects in the bucket. A version ID and two versions of "photo1.jpg" are now visible. 

<img width="922" alt="12" src="https://github.com/user-attachments/assets/e6dde736-138e-417e-9fa0-2fb24353947a" />

6.5 Return the web browser tab and refresh the page. The first photo is now visible and no longer covered with a red x. 

<img width="947" alt="13" src="https://github.com/user-attachments/assets/b4faa739-7885-4ee7-8fdb-4d1823289b85" />

Step 7: Setting up a Lifecycle policy

7.1 On the bucket overview page, select the **Management** tab. Under "Lifecycle rules", click the **Create lifecycle rule** button. 

7.2 Create a name for the rule and select the scope as **This rule applies to all objects in the bucket**, put a check in the box to acknowledge the warning. 

7.3 Under "Lifecycle rule actions", put a check in the bos next to **Move noncurrent versions of objects between storage classes & Permanently delete noncurrent versions**

7.4 Under "Transition noncurrent versions of objects betweeb storage classes" select **Standard-IA**. For "Choose a storage class transitions" enter 30 for "Days after objects noncurrent"

7.5 Under "Permanently delete noncurrent versions of objects" enter 30. Review the timeline summary of the rule and click **Create rule** when finished. 

<img width="953" alt="14" src="https://github.com/user-attachments/assets/a6215494-7c23-42aa-887e-c53723ee67d4" />

Step 8: Clean up resources

8.1 Delete objects in the bucket using the Empty feature

8.2 Delete the bucket

8.3 Delete the cloudformation stack

## Provisioning on Cloud Formation

AWS CloudFormation is a service that helps you model and set up your AWS resources so that you can spend less time managing those resources. You can create a template that describes all of the AWS resources that you want and CloudFormation takes care of provisioning and configuring the resources for you.

### Setting up a VPC using CloudFormation Hands on Lab

Step 1: Create a VPC using CloudFormation

1.1 Open a text editor (Notepad) or IDE (Visual studio code) and create a yaml file called sfid-cfn-vpc.yaml

1.2 Copy the following code into the yaml file and save the file
```
Resources:
  # Create a VPC
  MainVPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: 10.0.0.0/16
```

1.3 Open the AWS CloudFormation console and click on **Create stack**

1.4 In the prepare template section, select **Template is ready**

1.5 In the Template source section, select **Upload a template file**. Click on the **Choose file** button and navigate to where the yaml file was saved. Select the file and click **Open**. Click on the **Next** button. 

1.6 Create a name for the stack and click the **Next** button twice

1.7 On the review page, review the settings and click the **submit** button when finished.

<img width="958" alt="1" src="https://github.com/user-attachments/assets/0b8ca9cb-084f-4e1e-b2bf-4a7c9ec65c66" />

1.8 Open the AWS VPC console to check the VPC that was created using CloudFormation

<img width="953" alt="4" src="https://github.com/user-attachments/assets/66bd0ef8-c117-4e6a-bfe8-9919b37f1d32" />

1.9 Add the following code to the bottom of the YAML file and save the file

```
      EnableDnsHostnames: 'true'
      EnableDnsSupport: 'true'
      Tags:
      - Key: Name
        Value: VPC for SFID CFN
```

1.10 Steps to update the template stack
* Open the AWs CloudFormation Stacks console and select “SFID-CFN-VPC" in the Stack List
* Click on the **Update** button
* In the Prepare template section, select **Replace current template**
* In template source section, select **Upload a template file**
* Click on **Choose file** and navigate to the the YAML file was saved
* Select the YAML file and click **Open**
* Click **Next**
* Leave the Parameters and Configure stack options as default, click **Next** twice
* Click Submit

1.11 Leave the Parameters and Configure stack options as default and click the **Next** button twice.

1.12 Review the settings and click the **Submit** when finished

<img width="958" alt="3" src="https://github.com/user-attachments/assets/f4c1f5da-2e12-48af-a4c9-c88372f327bc" />

1.13 Navigate to the AWS VPC console, the VPC Tag and DNS options was enabled using CloudFormation

<img width="776" alt="Screenshot 2025-02-06 111826" src="https://github.com/user-attachments/assets/871fb1ff-f27a-4dae-a44e-6f956c2c508a" />

Step 2: Create an Internet Gateway

2.1 Add the following code to the bottom of the YAML file and save the file

```
  # Create and attach InternetGateway
  InternetGateway:
    Type: AWS::EC2::InternetGateway
    DependsOn: MainVPC

  AttachIGW:
    Type: AWS::EC2::VPCGatewayAttachment
    Properties:
      VpcId: !Ref MainVPC
      InternetGatewayId: !Ref InternetGateway
```

2.2 Repeat the previous steps for updating the stack template

2.3 Navigate to the AWS VPC console to see the Internet gateway that has been attached to the VPC using CloudFormation

<img width="954" alt="6" src="https://github.com/user-attachments/assets/ce1ce657-de2d-42c8-b9da-efc161e0db4e" />

Step 3: Create first subnet

3.1 Add the following code to the bottom of the YAML file and save the file

```
  # Create First Subnet
  FirstSubnet:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref MainVPC
      CidrBlock: 10.0.10.0/24
      AvailabilityZone: "us-east-1a"
      Tags:
      - Key: Name
        Value: Public Subnet A - SFID
```

3.2 Repeat the previous steps for updating the stack template

<img width="951" alt="8" src="https://github.com/user-attachments/assets/da89bf7d-dc55-4589-b758-dfe358eddbae" />

3.3 Navigate to the VPC console to see the subnet that was added to the VPC using CloudFormation

<img width="953" alt="9" src="https://github.com/user-attachments/assets/f1bdca15-bf2a-4bbc-a35d-e9debffeeb55" />

Step 4: Create an additional subnet 

4.1 Add the following code to the buttom of the YAML file and save the file

```
  # Creating additional subnet
  SecondSubnet:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref MainVPC
      CidrBlock: 10.0.20.0/24
      AvailabilityZone: "us-east-1b"
      Tags:
      - Key: Name
        Value: Public Subnet B - SFID
```

4.2 Repeat the previous steps for updating the stack template

4.3 Navigate to the VPC console to see the additional subnet

<img width="953" alt="11" src="https://github.com/user-attachments/assets/28b696b3-65df-40a5-a8cc-b928a1eb2c07" />

Step 5: Setting up a route table

5.1 Add the following code to the bottom of the YAML file and save the file

```
  # Create and Set Public Route Table
  PublicRouteTable:
    Type: AWS::EC2::RouteTable
    Properties:
      VpcId:  !Ref MainVPC
      Tags:
      - Key: Name
        Value: Public Route Table

  PublicRoute:
    Type: 'AWS::EC2::Route'
    DependsOn: AttachIGW
    Properties:
      RouteTableId: !Ref PublicRouteTable
      DestinationCidrBlock: 0.0.0.0/0
      GatewayId: !Ref InternetGateway

  # Associate Public Subnets to Public Route Table
  PublicSubnet1RouteTableAssociation:
    Type: 'AWS::EC2::SubnetRouteTableAssociation'
    Properties:
      SubnetId: !Ref FirstSubnet
      RouteTableId: !Ref PublicRouteTable

  PublicSubnet2RouteTableAssociation:
    Type: 'AWS::EC2::SubnetRouteTableAssociation'
    Properties:
      SubnetId: !Ref SecondSubnet
      RouteTableId: !Ref PublicRouteTable
```

5.2 Repeat the previous steps for updating the stack template

5.3 Navigate to the VPC console to see the Route table that was created using CloudFormation

<img width="953" alt="13" src="https://github.com/user-attachments/assets/85fd7ab7-fd00-4b86-a189-78ab95676960" />

Step 6: Create a Security group

6.1 Add the following code to the bottom of the YAML file, replace the IP address with your own IP address, then save the file

```
  # Create Security Group for the following:
  MainSecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: Security Group for Web Server
      VpcId: !Ref MainVPC
      SecurityGroupIngress:
      - IpProtocol: tcp
        FromPort: 80
        ToPort: 80
  # Replace the IP address below by your Public IP Address:
        CidrIp: 127.0.0.1/32
      Tags:
      - Key: Name
        Value: Web Server Security Group - SFID
```
6.2 Repeat the previous steps for updating the stack template

6.3 Navigate to the VPC console to see the security group that was created using CloudFormation

<img width="953" alt="15" src="https://github.com/user-attachments/assets/a969d516-3e4d-4659-83cb-6f18d4552dd8" />

Step 7: Add a description to the CloudFormation template and Add Outputs

7.1 Add the following code to the top of the YAML file

```
Description: Introduction to CloudFormation SFID - Virtual Private Cloud (VPC)
```

7.2 Add the foollowing code to the bottom of the YAML file and then save the file

```
Outputs:
  MainSubnet:
    Value: !Ref FirstSubnet
    Description: Public Subnet ID with Direct Internet Route

  MainSecurityGroup:
    Value: !Ref MainSecurityGroup
    Description: Security Group ID for the Web Server
```

7.3 Repeat the previous steps for updating the stack template

7.3 On the CloudFormation console, navigate to the "Stack info" and "Outputs" tabs to see the changes that were just added to the template

<img width="481" alt="18" src="https://github.com/user-attachments/assets/589d6e83-13aa-429f-ae8e-ff83f62f0668" />

<img width="462" alt="19" src="https://github.com/user-attachments/assets/bd037071-ef61-4182-8b00-c37125524ac4" />


### Setting up an EC2 instance using CloudFormation on Lab

Step 1: Launch an EC2 Instance

1.1 Open a text editor or IDE and create an empty YAML file called sfid-cfn-ec2.yaml

1.2 Copy and paste the following code into the YAML file and save the file

```
Resources:
# Create EC2 Linux
  WebServerInstance:
    Type: AWS::EC2::Instance
    Properties:
      ImageId: "ami-07caf09b362be10b8"
      InstanceType: t3a.micro
```
1.3 Open the CloudFormation console and click on the **Create stack** button

1.4 In the Prepare template, select **Template is ready**

1.5 In the Template source section, select **Upload a template file**. Click on the **Choose file** button and navigate to where the yaml file was saved. Select the file and click **Open**. Click on the **Next** button. 

1.6 Create a name for the stack anc click the **Next** button. Leave the Configure stack options as default and click the **Next** button

1.7 Review the settings and click the **Submit** button when finished

<img width="948" alt="2" src="https://github.com/user-attachments/assets/21f5b61e-67df-46fd-b3be-273e322d8ff3" />

1.8 Open the AWs EC2 console to check the EC2 instance that was created using CloudFormation

<img width="954" alt="3" src="https://github.com/user-attachments/assets/04f15b9b-721f-478b-8714-0371195ddfb0" />

Step 2: Tag and pass User Data to the EC2 instance

2.1 Add the following code to the bottom of the YAML file and save the file

```
      Tags:
          - Key: Name
            Value: Web Server for IMD
      UserData: 
        Fn::Base64:
          !Sub |
          #!/bin/sh
          yum -y install httpd
          chkconfig httpd on
          systemctl start httpd
          echo '<html><center><text="#252F3E" style="font-family: Amazon Ember"><h1>AWS CloudFormation is Fun !!!</h1>' > /var/www/html/index.html
          echo '<h3><img src="https://d0.awsstatic.com/logos/powered-by-aws.png"></h3></html>' >> /var/www/html/index.html
```
2.2 Steps to update the CloudFormation template stack
* Open the AWs CloudFormation Stacks console and select “SFID-CFN-EC2" in the Stack List
* Click on the **Update** button
* In the Prepare template section, select **Replace current template**
* In template source section, select **Upload a template file**
* Click on **Choose file** and navigate to the the YAML file was saved
* Select the YAML file and click **Open**
* Click **Next**
* Leave the Parameters and Configure stack options as default, click **Next** twice
* Click Submit

<img width="535" alt="5" src="https://github.com/user-attachments/assets/92363a90-ad9e-42e4-b86d-1e55cb72471a" />

Step 3: Terminate the EC2 instance

3.1 Open the CloudFormation stacks console

3.2 Select the stack in the stack list and click on the **Delete** button. A prompt will appear to confirm that you want to delete the stack and its resources, click on **Delete stack**

<img width="953" alt="7" src="https://github.com/user-attachments/assets/9f98ddf8-d7ee-48df-be13-3ead339da3b3" />

3.3 Navigate to the EC2 Dashboard to the confirm that the EC2 instance was deleted

<img width="953" alt="8" src="https://github.com/user-attachments/assets/020da7e1-5909-4f9a-a073-ad639f4c98f2" />

Step 4: Launch EC2 instance inside of the VPC

4.1 Add the following code to the top of the YAML file

```
Parameters:
  PublicSubnet:
    Description: Select a Public Subnet created in the "VPC for SFID CFN" Lab (Hint - Search for "SFID")
    Type: 'AWS::EC2::Subnet::Id'
  SecurityGroup:
    Description: Select the Security Group created in the "VPC for SFID CFN" Lab (Hint - Search for "SFID")
    Type: 'AWS::EC2::SecurityGroup::Id'
```

4.2 Add the following to the bottom of the YAML file and save the file

```
      NetworkInterfaces:
        - GroupSet:
            - !Ref SecurityGroup
          AssociatePublicIpAddress: 'true'
          DeviceIndex: '0'
          DeleteOnTermination: 'true'
          SubnetId: !Ref PublicSubnet
```

4.3 Open the CloudFormation console and click on the **Create stack** button

4.4 In the Prepare template, select **Template is ready**

4.5 In the Template source section, select **Upload a template file**. Click on the **Choose file** button and navigate to where the yaml file was saved. Select the file and click **Open**. Click on the **Next** button. 

4.6 Provide a stack name, set the desired paramaters (select a subnet and a security group) and click **Next**

4.7 Review the settings and click on the **Submit** button when finished

<img width="955" alt="10" src="https://github.com/user-attachments/assets/441708a8-e564-4864-8d29-dbcdc9f2effd" />

4.8 Open the EC2 console to check the EC2 instance that was created using CloudFormation. Copy and paste the Public Ip address to see the web server in the browser.

<img width="953" alt="11" src="https://github.com/user-attachments/assets/7d956787-6798-4402-b8da-6818a6cca1e0" />

Step 5: Adding a description to the CloudFormation template and add Outputs

5.1 Add the following code to the top of the YAML file

```
Description: Introduction to CloudFormation SFID - Elastic Compute Cloud (EC2)
```

5.2 Add the following code to the bottom of the YAML file and save the file

```
Outputs:
  PublicDNS:
    Value: !Join 
      - ''
      - - 'http://'
        - !GetAtt 
          - WebServerInstance
          - PublicDnsName
    Description: Web Host Public URL
```

5.3 Repeat the steps to update the CloudFormation template stack 

5.4  On the CloudFormation console, navigate to the "Stack info" and "Outputs" tabs to see the changes that were just added to the template

<img width="485" alt="13" src="https://github.com/user-attachments/assets/8e2b99e8-b459-494c-83c4-c70749338f13" />

<img width="493" alt="15" src="https://github.com/user-attachments/assets/b3bb4d95-e385-401b-90d1-bdbcce0b1de9" />

Step 6: Clean up resources

6.1 Delete the EC2 stack firts and then delete the VPC stack. This will delete the stacks and its resources. 
