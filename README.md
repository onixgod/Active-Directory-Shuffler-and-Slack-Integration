/# Active Directory Shuffler and Slack Integration

## Objective
The Active Directory, Shuffler and Slack Integration project aimed to create an automated incident response workflow that bridges security monitoring with real-time team communication. The primary focus was to establish seamless integration between Active Directory security events, SOAR (Security Orchestration, Automation and Response) capabilities through Shuffler, and instant notification systems via Slack. This hands-on experience was designed to simulate enterprise-level security operations workflows, automate threat detection responses, and demonstrate proficiency in orchestrating multi-platform security tools essential for SOC Tier 1 analyst responsibilities.

## Acknowledgment:
A big thanks to Steven from the @MyDFIR for the walkthrough and guidance on YouTube. The invaluable insights greatly aided me in completing this project.
This shows proper attribution to the content creator who helped guide your learning, which is excellent practice in the cybersecurity community!

### Skills Learned
- Advanced understanding of SOAR (Security Orchestration, Automation and Response) concepts and practical implementation.
- Proficiency in configuring and managing Active Directory security monitoring and event correlation.
- Splunk installation and configuration in educational environments.
- Splunk data ingestion from multiple sources and log formats.
- Creation and management of Splunk alert rules for automated threat detection.
- Vultr cloud environment setup and configuration for security lab deployments.
- Implementation and management of firewall rules for network segmentation and security.
- Ability to design and implement automated incident response workflows using Shuffler.
- Enhanced knowledge of API integrations between security tools and communication platforms.
- Development of skills in creating custom playbooks for threat detection and automated alerting.
- Experience with real-time notification systems and escalation procedures in SOC environments.
- Understanding of security tool integration and workflow orchestration.
- Improved ability to streamline security operations through automation and reduce manual response times.

### Tools Used
- Splunk - SIEM platform for log ingestion, analysis, and alert management
- Linux Ubuntu - Operating system platform for Splunk deployment
- Active Directory - Directory service for user authentication and security event generation
- Windows Server 2022 - Two separate servers: one dedicated to AD deployment and one as a test machine
- Shuffler - SOAR platform for security orchestration and automated response workflows
- Slack - Team communication platform for real-time incident notifications
- Vultr - Cloud infrastructure provider for hosting the lab environment (3 VMs total)
- Vultr Network Firewall - Cloud-based firewall rules for secure access control
- PowerShell - For scripting and automation tasks
- APIs - For integrating different security tools and platforms
- Virtual Machines - Three VMs creating isolated testing environments

## Steps
### Step 1: Project Planning and Architecture Design
The first step in this project is to create a comprehensive diagram and playbook that will guide our deployment process. This planning phase is crucial for understanding the data flow, system interactions, and automation workflows before we begin the actual implementation.
### 1.1 Creating the Network Diagram
I will be using Draw.io (a free diagramming platform) to sketch the project architecture. This visual representation will provide clear guidance on how to approach each component of the project.
### Diagram Components:

- ### Three Virtual Machines:
    - **MyLab-ADDC01** - Active Directory Domain Controller (Windows Server 2022)
    - **MyLab-Test01** - Test/Target machine for attack simulation (Windows Server 2022)
    - **MyLab-Splunk** - SIEM platform for log collection and analysis (Ubuntu Linux)

- ### Cloud Services Integration:
    - **Shuffler** - SOAR platform for automation and orchestration
    - **Slack** - Communication platform for real-time notifications

- ### Network Infrastructure:
    - **Vultr Cloud** - Hosting platform for all three VMs
    - **Vultr Network Firewall** - Access control and security rules
    - **Attacker Machine** - External laptop representing the threat actor

![image](https://github.com/user-attachments/assets/2bb4cc57-cfe6-48ee-a406-5f01b2443825)
*This diagram illustrates the complete lab architecture, with data flow arrows showing how telemetry travels from the Windows machines to Splunk, and then triggers automated responses through Shuffler to send Slack notifications.*

### 1.2 Cloud Platform Setup
We will be using Vultr cloud services for our virtual machine deployment. Vultr offers a $300 voucher for new users, which is perfect for this lab environment. As mentioned by @MyDFIR, you can access the promotional link through his YouTube channel or visit (hxxps://www.vultr.com/?ref=9632889-9J).
**Note:** If you don't have a Shuffler or Slack account, please create one before you start, because it will be essential for automation and  receiving automated notifications from our SOAR platform.
### 1.3 Playbook Development
The final part of this planning phase involves creating a detailed playbook that defines how Shuffler will automatically respond to security events detected by Splunk. This playbook will establish:

- **Trigger conditions** - What events will initiate automated responses
- **Response actions** - What steps Shuffler will take when events are detected
- **Notification procedures** - How and when alerts will be sent to Slack
- **Escalation paths** - When and how incidents should be elevated

|Successful Unauthorised Login Playbook|
| ------ |
|Send an alert notification and email to the SOC Analyst asking if they want to disable the user|
|If YES - Disable Domain Account and send notification of account disabled|
|If NO - Do nothing|

![image](https://github.com/user-attachments/assets/aa511060-d23d-49db-b1cf-c557b173fefb)
*This playbook shows the decision tree for automated incident response, from initial event detection through final notification and documentation.*
This foundational step ensures we have a clear roadmap before deploying any infrastructure, making the implementation process much smoother and more organised.

### Step 2: Virtual Machine Deployment on Vultr
### 2.1 Creating a Vultr Account
The first step is to open an account on Vultr. As mentioned previously, a new account comes with $300 in credits, which is more than enough to complete this project and provides excellent value for learning cybersecurity lab deployments.
### 2.2 Deploying the Active Directory Domain Controller (MyLab-ADDC01)
**Creating the First VM - Windows Server 2022 for AD:**
![image](https://github.com/user-attachments/assets/d476d42a-8335-4ba8-8f1b-fd8d2aa5a6ee)
*This shows the main Vultr dashboard where we'll begin our VM deployment process.*
To create a VM, click on the **Compute** link on the left panel, then click on the **Deploy** button in the top right corner.
![image](https://github.com/user-attachments/assets/56a11c40-ce58-45ce-948d-740bacd2f418)
*The Compute section shows the deploy button that initiates the VM creation process.*
On the deployment page, select **Shared CPU**. Then, choose a location and city close to your region to improve the speed at which the VM is accessed remotely. For the server type, select **VC2-2c-4GB**, which provides sufficient resources for our Active Directory server.
![image](https://github.com/user-attachments/assets/1e966c38-d9b0-42bf-af2d-71f14699d6d3) 
*Server configuration showing the recommended specifications for our AD server deployment.*
Click on the **Configure Software** button located in the bottom left corner. Select **Windows Server 2022 Standard x64** as our operating system.
![image](https://github.com/user-attachments/assets/63bcb0c4-396f-4802-948c-579f00c22774)  
_Operating system selection interface displaying Windows Server 2022 options._
Label the machine with your preferred hostname for the AD Server (e.g., **"MyLab-ADDC01"**). **Deselect Automatic Backups** (this feature isn't needed for this project and will save credits), then click the **Deploy** button in the bottom right corner to initiate the deployment.
![image](https://github.com/user-attachments/assets/0ac51e83-ed7f-44b4-ab03-2fa005046725) 
_Final configuration screen before deployment, showing hostname setup and backup options._
When deployment is finished, you can access the server overview and **take note of your public IP, username, and password** for future remote access.
![image](https://github.com/user-attachments/assets/b7506cf1-c6b0-4bc8-8db7-9ef1ac04d969) 
_VM overview displaying essential connection information, including public IP and credentials._
### 2.3 Deploying the Test Machine (MyLab-Test01)
Now we'll deploy the test machine following similar steps. Choose an appropriate hostname for this machine, but select **VC2-1c-2GB** for the server specifications (lower resources are sufficient for the test machine role).
![image](https://github.com/user-attachments/assets/9e72b9dc-2411-40ac-976f-8eeb642ba153) 
_Test machine configuration showing the smaller resource allocation appropriate for its role._
Access the deployed machine and **record the Public IP address, username, and password** for later use.
![image](https://github.com/user-attachments/assets/13563669-ac3a-4b19-9109-6a33f9d3e808) 
_Test machine deployment summary showing connection information._
### 2.4 Deploying the Splunk Server (MyLab-Splunk)
For our SIEM platform, select **Shared CPU**, choose your preferred location, then select **VC2-4c-8GB** (higher specifications needed for log processing and analysis).
![image](https://github.com/user-attachments/assets/14348738-8545-47cd-9bac-412513d59961) 
_Splunk server configuration displaying the higher resource allocation needed for SIEM operations._
Select **Ubuntu 22.04 x64** as the operating system. Remember to deselect automatic backups and click deploy.
![image](https://github.com/user-attachments/assets/856b5c56-5c44-4c97-b0cb-1550a82be2bb) 
_Ubuntu operating system selection for our Splunk platform deployment._
Access the newly deployed Linux machine and **take note of the public IP address, username, and password**.
![image](https://github.com/user-attachments/assets/a6c8c075-0a68-4924-a82b-b6655f873bab) 
_Ubuntu server deployment summary showing SSH connection details._
### 2.5 Configuring Firewall Rules
Now we'll set up the firewall group to grant secure remote access to our VMs. We need to allow access to:
-   **SSH port 22** (for Linux administration)
-   **RDP port 3389** (for Windows remote access)
-   **Port 8000** (for Splunk web interface)
For security during testing, set the Source for all connections to **"My IP"** to restrict access to your current location.
![image](https://github.com/user-attachments/assets/7085be3a-3ea8-4c0d-aa50-1526ac7d947c)
_Firewall rules configuration displaying the essential ports needed for remote administration and Splunk access._
### 2.6 Setting Up VPC Network
To establish secure internal communication between VMs, we'll attach a VPC (Virtual Private Cloud) to each machine. This provides private network connectivity within the cloud environment.
For each VM, select the machine, click on **Settings**, then **VPC Networks**, and click the **Attach VPC** button. The process takes a few seconds to complete.
**Important:** Record the **Private IP address and Network Mask** for each machine - these will be needed to configure network adapters on the Windows machines.
![image](https://github.com/user-attachments/assets/3e6af083-bdab-4e8c-a8ca-64430d570d05)







 (Deploy area)


 (Win AD Deploy)


(Select win 2022 server)

 (Disable backup and deploy)

 (AD server login details, public IP)


 (Win Test deploy)


 (Select win 2022 server) (Disable backup and deploy)


  (Test server login details, public IP)


 (Linux Deploy)

 (Disable backup and deploy)


 (Ubuntu login details, public IP)


 (Vultr Firewall Group)


 (attached VPC for each machine, locap IP)


![image](https://github.com/user-attachments/assets/a4e7c89a-68d4-4a3f-91a7-5c6a8a85b66f) (Domin controler deployment, MyLab.local, Net1 public IP and Net2 private IP, setup prive IP also for MyLab-Test01)


![image](https://github.com/user-attachments/assets/442981ff-56af-4021-bc86-672477439938) (Create an AD user for testing)


![image](https://github.com/user-attachments/assets/8800341c-7d42-40b5-aa78-3c394da975af) (Join test machine to AD server)


![image](https://github.com/user-attachments/assets/a568d4bd-f485-44bb-a85d-a922b427ec21) (SSH to Ubunto machine)

![image](https://github.com/user-attachments/assets/1baa2429-e809-4460-9b6f-3b02dc73e5ef) (check network adaptor, check private IP)

![image](https://github.com/user-attachments/assets/4314ecf3-72be-4464-a4ac-ba886fd32fe1) (Update Ubuntu) (apt-get update && apt-get upgrade)


![image](https://github.com/user-attachments/assets/a8282480-e72b-408d-b201-a87fb44827a9) (get Splunk command) (wget -0 splunk-9.4.3-237ebbd22314-linux-amd64. deb "https://download. splunk.com/products/splunk/releases/9.4.3/linux/splunk-9.
4.3-237ebbd22314-linux-amd64.deb")

![image](https://github.com/user-attachments/assets/0b9a589f-01dc-42cc-87be-ca07884ea6fb) (Unpack Splunk) (dpkg -i splunk-9.4.3-237ebbd22314-linux-amd64. deb)

Navigate to cd /opt/splunk/bin/

Install ./splunk start

Open port for Splunk connection from public IP (ufw allow 8000)

Open port for forwarder (ufw allow 9997)
























*Ref 1: Network Diagram*








