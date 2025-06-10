# Active Directory Shuffler and Slack Integration

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
_VPC network configuration showing private IP assignments for internal communication between VMs._

### 2.7 Active Directory Configuration
Deploy Active Directory Services on the AD Server using the private IP address on network adapter 2.
**Note:** I won't detail the AD installation steps here, as this should be fundamental knowledge for cybersecurity professionals. If needed, numerous online tutorials are available to cover Windows Server Active Directory deployment.

![image](https://github.com/user-attachments/assets/a4e7c89a-68d4-4a3f-91a7-5c6a8a85b66f) 
_Active Directory configuration completed with domain controller promotion successful._

After AD configuration and promotion, create a domain user for testing purposes. In this example, I created a user named **Jenny Smith (JSmith)** with a secure password, deselecting the options "Change password at first login" and "Password never expires" for lab convenience.

![image](https://github.com/user-attachments/assets/442981ff-56af-4021-bc86-672477439938) 
_Domain user creation interface showing the test user account setup for our lab environment._

### 2.8 Joining the Test Machine to the Domain
Configure the test machine to join our domain by setting up network adapter 2 with its VPC private IP address and using the AD server's private IP as the DNS server.

Navigate to Windows Settings → **Rename this PC (advanced)** → **Change** → select **Domain** → enter your domain name. The system will prompt for administrator credentials. Upon successful authentication, the machine will restart and join the domain.

![image](https://github.com/user-attachments/assets/8800341c-7d42-40b5-aa78-3c394da975af) 
_Domain join interface displaying successful integration of the test machine with our Active Directory domain._

### 2.9 Ubuntu Splunk Server Setup
SSH to the Ubuntu VM using PowerShell with the root user credentials. If the connection fails, verify that your firewall rules are properly configured.

![image](https://github.com/user-attachments/assets/a568d4bd-f485-44bb-a85d-a922b427ec21) 
_SSH connection establishment to the Ubuntu server for Splunk installation._

Verify network configuration using the `ip a` command. Ensure the second network interface displays the VPC private IP address.

![image](https://github.com/user-attachments/assets/1baa2429-e809-4460-9b6f-3b02dc73e5ef) 
_Network interface configuration showing both public and private IP assignments._

Before any installation, update and upgrade the Linux system using: `apt-get update && apt-get upgrade`

![image](https://github.com/user-attachments/assets/4314ecf3-72be-4464-a4ac-ba886fd32fe1) 
_System update and upgrade process, ensuring the latest security patches and software versions._

```bash
wget -O splunk-9.4.3-237ebbd22314-linux-amd64.deb "https://download.splunk.com/products/splunk/releases/9.4.3/linux/splunk-9.4.3-237ebbd22314-linux-amd64.deb"
```

![image](https://github.com/user-attachments/assets/a8282480-e72b-408d-b201-a87fb44827a9) 
_Splunk Enterprise download showing the installation package being retrieved from the official repository._

```bash
dpkg -i splunk-9.4.3-237ebbd22314-linux-amd64.deb
cd /opt/splunk/bin/
./splunk start
```

During installation, you'll be prompted to create administrator credentials for Splunk web access. Splunk runs on port 8000 by default.

**Access Methods:**

-   **Internal access:** Ubuntu\_Private\_IP:8000
-   **Remote access:** Ubuntu\_Public\_IP:8000

Enable port 8000 on the Ubuntu firewall: `ufw allow 8000`

![image](https://github.com/user-attachments/assets/0b9a589f-01dc-42cc-87be-ca07884ea6fb) 
_Splunk installation completed successfully with web interface accessible on port 8000._

![image](https://github.com/user-attachments/assets/1290fe34-397c-4922-9db1-432aee1d2484)
_Splunk Login Web Interface_

Just a quick note: when I tried to access the Splunk web interface remotely, my Bitdefender EDR interfered with the connection. If you run into the same problem, check your EDR settings first. 

This completes the virtual machine deployment phase. All three VMs are now configured with proper networking and are ready for the next phase of the project.

## Step 3: Splunk Web Interface Configuration
### 3.1 Accessing Splunk Web Interface
Now we'll make essential adjustments to Splunk through the web interface. You can access Splunk remotely through a web browser using the **Ubuntu public IP address** or from any Windows VM using the **Ubuntu private IP address**.
**Access URLs:**
-   **Remote access:** `http://[Ubuntu_Public_IP]:8000`
-   **Internal access:** `http://[Ubuntu_Private_IP]:8000`

![image](https://github.com/user-attachments/assets/348e0a7c-ade6-4c89-945f-21d404116afe) 
_Splunk web interface login page showing successful remote access to our SIEM platform._

### 3.2 Creating a Custom Index
We need to create a dedicated index where we'll collect telemetry from our Windows machines. Navigate to **Settings** → **Indexes**.

![image](https://github.com/user-attachments/assets/bc14f4af-6525-4cc7-a42f-b723b2d589ed) 
_Splunk Settings interface showing the path to index management for creating our custom data collection point._

Click **New Index** and create an index for our Windows event data (e.g., "WinEventLog://Security" or "WinEventLog://System"). This dedicated index will help organise and efficiently search through our Active Directory and test machine telemetry.

![image](https://github.com/user-attachments/assets/c08628fa-a7d3-4564-aabb-554488f656ae) 
_Index creation interface and confirmation showing our new Windows telemetry index is properly configured and active._

### 3.3 Configuring Time Zone Settings
For accurate log correlation and incident timeline analysis, we need to set our Time Zone to **(GMT) Greenwich Mean Time**.
Navigate to your **account menu** (top right) → **Preferences** → **Time Zone** → select **GMT** → click **Apply**.

![image](https://github.com/user-attachments/assets/a5fe7c10-c841-48a5-bb17-e047de6fc62e) 
_Time zone configuration ensuring all timestamps are standardised to GMT for consistent log analysis across our distributed environment._

### 3.4 Installing Windows Add-on
To correctly parse and analyse Windows event logs, we need to install the Microsoft Windows Add-on. Click on **Apps** → **Find More Apps**.

![image](https://github.com/user-attachments/assets/9309bebb-7f41-485b-b73f-4dd09b947702) 
_Splunk Apps management interface showing the path to browse and install additional applications._

In the search field, type **"windows"** and locate the **"Splunk Add-on for Microsoft Windows"**. Click **Install** to add this essential component for Windows log parsing and field extraction.

![image](https://github.com/user-attachments/assets/62d21621-be92-4b20-a928-0955a45a6e5b) 
_Splunk app store showing the Microsoft Windows Add-on that will enable proper parsing of Windows Event Logs and Active Directory security events._

**Why This Add-on is Important:**
-   Provides pre-built field extractions for Windows Event Logs
-   Includes knowledge objects for Active Directory security events
-   Enables proper parsing of authentication, logon, and security policy events
-   Essential for SOC analyst workflows involving Windows infrastructure

After installation, the add-on will automatically begin parsing Windows event data with proper field mapping, making our security event analysis much more efficient and accurate.

## Step 4: Installing and Configuring Splunk Universal Forwarder on Windows Machines
### 4.1 Downloading Splunk Universal Forwarder
Now it's time to install and configure Splunk on our Windows machines to send security telemetry to our Security Information and Event Management (SIEM) system. We need to install the **Splunk Universal Forwarder**, which serves as a lightweight agent for collecting and forwarding logs.
You can just head to the Splunk website and download the Universal Forwarder binary. You'll need a free Splunk account for the download. Navigate to **Platform** → **Free Trials & Downloads** → under **Universal Forwarder** → click **Get My Free Download**.

![image](https://github.com/user-attachments/assets/66a12f3f-a783-458b-81de-c1646e503bb5) 
_Splunk download portal displaying the Universal Forwarder option for Windows log collection and forwarding._

### 4.2 Installing Universal Forwarder on Both Windows Machines
**Install the forwarder on both MyLab-ADDC01 and MyLab-Test01:**
Double-click the downloaded executable to begin installation. Check the box to **accept the License Agreement**, then click **Next**.

![image](https://github.com/user-attachments/assets/a66883cb-b531-4758-bdec-1205aa319622) 
_Installation wizard displaying the license agreement that must be accepted to proceed with the forwarder installation._

For the username field, create a username of your choice (e.g., "splunk\_service"). For the password, leave it set to **'Generate random password'** for security.

![image](https://github.com/user-attachments/assets/ea5b8e0c-4e90-4d79-b8d6-04dd5df640d9) 
_Service account configuration showing username creation and automatic password generation for the Splunk service._

Since we're using Splunk for lab purposes, skip the deployment server configuration window and click **Next**.

![image](https://github.com/user-attachments/assets/70e50afd-21c0-46d7-b225-dc08e7d58691) 
_Deployment server configuration screen that can be skipped for our lab environment setup._

In the receiving indexer configuration, enter the **Splunk server's public IP address** and port **9997** (the default Splunk forwarding port).

**Format:** `[Splunk_Public_IP]:9997`

![image](https://github.com/user-attachments/assets/cac7ede9-ba0e-49f8-ae49-756195735445) 
_Receiving indexer configuration specifying our Splunk server's IP address and the standard forwarding port 9997._

### 4.3 Configuring Log Collection
Now we need to configure which logs the forwarder will send to Splunk manually. Navigate to: `C:\Program Files\SplunkUniversalForwarder\etc\system\default`

**Copy** the `inputs.conf` file from the default directory.

![image](https://github.com/user-attachments/assets/1f51c6f0-0571-4b58-9e37-29d508a2b446) 
_File system navigation showing the location of the default inputs.conf configuration file that needs to be copied._

**Paste** the `inputs.conf` file into: `C:\Program Files\SplunkUniversalForwarder\etc\system\local`

![image](https://github.com/user-attachments/assets/d1720b32-3d5b-4202-8c2e-843ee6748870) 
_Configuration file placement in the local directory where custom settings override default configurations._

### 4.4 Customising Input Configuration
Open the `inputs.conf` file with **Notepad** and scroll to the end of the file. Add the following configuration:
```ini
[WinEventLog://Security]
index=mylab
disabled=false
```

**Important Notes:**
-   Replace `mylab` with the index name you created in Splunk
-   This configuration collects Windows Security Event Logs
-   Additional logs (System, Application) can be added with similar entries
-   For this project, we focus on Security logs for incident detection

![image](https://github.com/user-attachments/assets/200f0bc3-9cc4-4396-8c07-4bf6b100e40a) 
_Configuration file modification showing the Windows Security Event Log collection settings pointing to our custom Splunk index._

Save and close the file after adding the configuration.

![image](https://github.com/user-attachments/assets/612f8990-2689-4d25-9acc-51e08dcad131) 
_File save confirmation showing the custom configuration has been properly applied._

### 4.5 Configuring Service Permissions
Access **Windows Services** to configure the Splunk service permissions. Search for **"SplunkForwarder"** service and open its properties.

![image](https://github.com/user-attachments/assets/bf692c74-fda0-41fb-a7f5-e22a847366ad) 
_Windows Services management interface displaying the SplunkForwarder service that needs permission configuration._

Navigate to the **Log On** tab and select **"Local System Account"** under "Log on as". This ensures the service has sufficient privileges to read Windows Security Event Logs. Click **OK** to apply.

![image](https://github.com/user-attachments/assets/01769e91-3ce1-4782-8e57-1155252ade8b) 
_Service configuration showing Local System Account selection, which provides necessary permissions for reading security event logs._

### 4.6 Starting the Forwarder Service
Right-click on the **SplunkForwarder** service and select **Restart**. If the restart fails, right-click again and choose **Start**.

_\[Fig 39 - Screenshot of service restart process and confirmation of running status\]_ 
_Service management showing the restart process and confirmation that the SplunkForwarder is running and collecting logs._

**Verification Steps:**
-   Confirm the service status shows "Running"
-   Check that no error messages appear in the service startup
-   Verify network connectivity to the Splunk server on port 9997
**Repeat this entire process on both Windows machines** (MyLab-ADDC01 and MyLab-Test01) to ensure comprehensive log collection from your Active Directory environment.





  Splunk forwarder download

 Splunk 1 install

 Splunk 2 install

 Splunk 3 skip

 Splunk 4 install

 (inputs default)

 (Inputs local)

 (inputs.conf)

 Windows services

 Log on as

 Restart






























Open port for forwarder (ufw allow 9997)
















