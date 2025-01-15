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






