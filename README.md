# LITA_EC2_INSTANCE_PROJECT_TEJIRI_AGANBI 
 A project that showcases the building of a Scalable Web Infrastructure for SmartShop and provisioning of EC2 instance in the AWS environment
## Installing GitHub for Documentation and Presentation
I installed and set up the GitHub for Desktop on my windows 11.
This is to help me document the process of building a scalable web application infrastructure for client SmartShop.
This document would serve these few of many important purposes:
##### 1. Clear Guidance for Everyone: 
This documentation will serve as a roadmap, to help everyone understand how the web application infrastructure is built which will make it easier to maintain consistency when updates or changes are needed.
##### 2. Faster Problem-Solving:
When something malfunctions, this documentation will show where things went wrong, so issues can be fixed quickly without unnecessary guesswork.
##### 3. Smooth Teamwork and Handoffs: 
With everything clearly written down in this document, new team members or partners of SmartShop can jump in without feeling lost, which makes collaboration and transitions much easier.
##### 4. Proof of Compliance and Security:
This documentation will serve as a record that shows everything was done by the book according to SmartShop's need and requirements, which is crucial for passing security checks, audits, and meeting legal requirements.
##### 5. Room to Grow: 
As SmartShop grows, this documentation will make it easier to upgrade or scale the system since there’s a clear guide showing what’s already in place and how to build on it.

With the above listed, I proceeded to create a new repository with a README.md, initialized and named : "LITA_EC2_INSTANCE_PROJECT_TEJIRI_AGANBI" in my system's GitHub from which I push changes made with notepad++ to the origin on the net. I then clicked on "published now".
Below is a picture of the created repository
![Repository Configuration](/GithubRepositoryCreation.jpg)

## CREATING THE VPC
The VPC was created in the AWS platform under the region of North Virginia,
![VPC Region](/RegionNorthVirginiaSelected.jpg) 
Choosing the IPv4 CIDR Classless Inter-Domain Routing) block of 10.0.0.0/16
![VPC Used](/LitaVPCSelected.jpg)
###### Configuration and Purpose
![VPC Overview](/VPCDetails.jpg)
This CIDR block encompass a range of up to 10.0.255.255  which is about 65,536 IP addresses some of which are reserved for AWS internal usage(5 out of 256, so we're left with 251 IP addresses). 
This makes for the inexhaustion of IP addresses which 
allows multiple test environments, eliminates future request for a larger IP block for VPC configuration when SmartShop grows, reduces conflict with public IPs ensuring better isolation and security, and guarantees scalability of applications, adding instances, easier integration with other services.
This also allows for further division into multiple smaller subnets within the VPC.
######## These therefore meeting the specified needs of SmartShop.
## CREATING THE SUBNETS
###### Configuration and Purpose 
Under Subnets, after selecting the created VPC, I proceeded to create 2 subnets: one public (CIDR block 10.0.1.0/24) and one private (CIDR block 10.0.2.0/24) named clearly with: 
The instances launched on the public subnet with large number of IP addresses enables communication between the instances and the Internet via an internet gateway(IGW). This is useful because SmartShop's web server needs to be accessible from the Internet.
![Public Subnet](/PublicSubnet.jpg)
The instances on the private subnet on the other hand will not have a direct communication with the Internet except through a NAT gateway.
![Private Subnet](/PrivateSubnet.jpg)
This is ideal for SmartShop as it provides the needed security of sensitive resources like databases and internal severs that's is not for public interaction or consumption reducing attacks and potential breaches.
There is also separation of services and control of access through route tables and security groups.
## ADDING INTERNET GATEWAY
###### Configuration and Purpose 
Under Internet gateways, an IGW was created and attached to the created VPC for SmartShop.
![Internet Gateway](/InternetGateway.jpg)
A route table was created and associated to the public subnet ONLY with a route of destination 0.0.0.0/0 and the Internet Gateway selected as Target.
This was done to control which subnet has direct internet access (via the Internet Gateway).
![RouteTable](/PublicRouteTable.jpg)
![RouteTable2](/PrivateRouteTable.jpg)
This destination, 0.0.0.0/0 ensures that all traffic not otherwise routed will go through the specified gateway( Internet Gateway) for public internet access, meeting SmartShop's need for high availability and accessibility.
## CONFIGURING NACLs And SECURITY GROUPS
###### Configuration and Purpose
Under Security, I set up Network Access Control Lists (NACLs) at the subnet level to control inbound and outbound traffic and a Security group (SG) named : "TejiriAganbi_Lita_SG" at the instance level (attached to the EC2 instances) acting as a virtual firewall, with two specified inbound rules on both NACLs and SG.
![Security Group Config](/SecurityGroup1.jpg)
These rules I set to allow inbound SSH traffic (port 22) to securely access my instances via SSH from my specific IP address and HTTP traffic (port 80) which allows public access to SmartShop's web server over HTTP, enabling users of SmartShop's services to visit the website from any IP.
![Security Group Config](/SecurityGroup2.jpg)
SSH prevents a unauthorized access to SmartShop's instances as it is restricted to their IP only while making it publicly accessible globally via HTTP.
![Security Group Config](/SecurityGroupcreated.jpg)
These also adds two level of security  at the NACLs level controlling what gets to SmartShop's instances and what gets through into and out of the instances at the SG level.
## LAUNCHING AN INSTANCES WITH APACHE WEB SERVER.
### LAUNCHING INSTANCES.
###### Configuration and Purpose
I navigated to the EC2 dashboard, ensuring that the region is still set to North Virginia, I clicked on launch instance to begin the configuration process for the EC2 instance launch.
I named the Instance "TejiriAganbi_Lita".
![EC2 Instance Creation](/EC2instance1.jpg)
I proceeded to select Amazon Linux 2 as specified for SmartShop's infrastructure as the Operating System and selected t2 micro as the instance type to be created.
![EC2 Instance Creation](/EC2instance2.jpg)
Amazon Linux 2 was selected because it is AWS-optimized for seamless integration with other AWS services, should SmartShop decide to integrate more services as they grow. This ensures better performance, compatibility and security.
While t2 micro is selected as an affordable option for lightweight workloads or testing environments meeting SmartShop's needs as a small business, while minimizing cost to "as needed per time" but has the ability to grow to higher levels when needed.
I proceeded to configure the instance to the public subnet under the "Lita_project_vpc", to enable access to and from the Internet.
![EC2 Instance Creation](/EC2instance3.jpg)
I assigned to the instance, the security group created "TejiriAganbi_Lita_SG" in the configuration  and proceeded to creating a keypair which I named, "Tejiri_Lita_KP".
I clicked on create new keypair that will be used to access SmartShop's EC2 instance, with configurations set to RSA (the encryption algorithm as keypair type and .pem as the file format.
![Keypair Configuration](/KeyPairCreation.jpg)
This provides extra security as only authorized users can access the instances using the private keypair.
The file was automatically downloaded to my computer's download folder.
Using gitbash, I confirmed that the keypair was indeed private and not publicly viewable using the commands "chmod 400 "Tejiri_Lita_KP.pem" which gave me nothing stating a successfully hidden keypair.
######## Under Network Settings,
I configured the following settings:
VPC was selected to Lita_Project_Vpc
The public subnet for Lita_project_vpc was selected and not the private subnet.
Auto-assign public IP was set to "Enabled" which automatically assigns the public IPv4 address to the instance, making it accessible from the internet.
![EC2 Instance Creation](/EC2instance4.jpg)
Configure storage" was left at "8gb"
![EC2 Instance Creation](/EC2instance5.jpg)
I reviewed my configuration and proceeded to click on "launch instance".
![EC2 Instance Creation](/EC2ConfigurationSummary.jpg)
The instance launched and after a few minutes of refreshing, the 2/2 checks passed became visible.
![EC2 Instance Launched](/RunningEC2Instance.jpg)
## INSTALLING APACHE WEB SERVER TO MY INSTANCE.
Under the SSH tab of my instance, I copied the code "ssh -i "Tejiri_Lita_kp.pem" ec2-user@ec2-52-90-180-115.compute-1.amazonaws.com"
![Keypair to Instance](/ConnectingInstancetokeypair.jpg)
I navigated to my computer's download folder, with my mouse, I right-clicked on an empty space and proceeded to select "git bash here". 
I pasted the copied code in the gitbash window opened and clicked "enter". The command ran and typed in "yes" to the question asked and clicked "enter"
The command ran and typed in "yes" to the question asked and clicked "enter" 
It ran the command and produced a bird like command on the gitbash window, showing the details of my EC2 instance and overall confirming my EC2 instance has been connected.
![Installing Apache Web Server] (/InstallingApacheOnEC2Instance.jpg)
I proceeded to run the following commands to install Apache:
"sudo yum update -y" Enter key
This  command returned as "done" with no packages marked for update.
"sudo yum install https -y". Enter key.
This installed Apache and left a feedback stating "installed"
![Installing Apache Web Server] (/ApacheInstallationCompleted.jpg)
"sudo systemctl start httpd"
"sudo systemctl enable httpd"
This finished the set up and I proceeded to confirm the Apache is running as should be by visiting the instance's public IP address "52.90.180.115" using a Chrome browser.
This showed the Apache test page.
![Apache Test Page](/LiveApacheTestPage) 