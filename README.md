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

In this lab, I learned how to create an AMI, included it in the setup of the Launch Template, created an EC2 Auto scaling group behind an Application Load Balancer















 
    
























