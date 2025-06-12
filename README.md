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

![image](https://github.com/user-attachments/assets/2bb4cc57-cfe6-48ee-a406-5f01b2443825)<br>
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

![image](https://github.com/user-attachments/assets/aa511060-d23d-49db-b1cf-c557b173fefb)<br>
*This playbook shows the decision tree for automated incident response, from initial event detection through final notification and documentation.*

This foundational step ensures we have a clear roadmap before deploying any infrastructure, making the implementation process much smoother and more organised.

### Step 2: Virtual Machine Deployment on Vultr
### 2.1 Creating a Vultr Account
The first step is to open an account on Vultr. As mentioned previously, a new account comes with $300 in credits, which is more than enough to complete this project and provides excellent value for learning cybersecurity lab deployments.
### 2.2 Deploying the Active Directory Domain Controller (MyLab-ADDC01)
**Creating the First VM - Windows Server 2022 for AD:**

![image](https://github.com/user-attachments/assets/d476d42a-8335-4ba8-8f1b-fd8d2aa5a6ee)<br>
*This shows the main Vultr dashboard where we'll begin our VM deployment process.*

To create a VM, click on the **Compute** link on the left panel, then click on the **Deploy** button in the top right corner.

![image](https://github.com/user-attachments/assets/56a11c40-ce58-45ce-948d-740bacd2f418)<br>
*The Compute section shows the deploy button that initiates the VM creation process.*

On the deployment page, select **Shared CPU**. Then, choose a location and city close to your region to improve the speed at which the VM is accessed remotely. For the server type, select **VC2-2c-4GB**, which provides sufficient resources for our Active Directory server.

![image](https://github.com/user-attachments/assets/1e966c38-d9b0-42bf-af2d-71f14699d6d3)<br>
*Server configuration showing the recommended specifications for our AD server deployment.*

Click on the **Configure Software** button located in the bottom left corner. Select **Windows Server 2022 Standard x64** as our operating system.

![image](https://github.com/user-attachments/assets/63bcb0c4-396f-4802-948c-579f00c22774)<br>
_Operating system selection interface displaying Windows Server 2022 options._

Label the machine with your preferred hostname for the AD Server (e.g., **"MyLab-ADDC01"**). **Deselect Automatic Backups** (this feature isn't needed for this project and will save credits), then click the **Deploy** button in the bottom right corner to initiate the deployment.

![image](https://github.com/user-attachments/assets/0ac51e83-ed7f-44b4-ab03-2fa005046725)<br>
_Final configuration screen before deployment, showing hostname setup and backup options._

When deployment is finished, you can access the server overview and **take note of your public IP, username, and password** for future remote access.

![image](https://github.com/user-attachments/assets/b7506cf1-c6b0-4bc8-8db7-9ef1ac04d969)<br>
_VM overview displaying essential connection information, including public IP and credentials._

### 2.3 Deploying the Test Machine (MyLab-Test01)
Now we'll deploy the test machine following similar steps. You can choose an appropriate hostname for this machine, but select **VC2-1c-2GB** for the server specifications (lower resources are sufficient for the test machine role).

![image](https://github.com/user-attachments/assets/9e72b9dc-2411-40ac-976f-8eeb642ba153)<br>
_Test machine configuration showing the smaller resource allocation appropriate for its role._

Access the deployed machine and **record the Public IP address, username, and password** for later use.

![image](https://github.com/user-attachments/assets/13563669-ac3a-4b19-9109-6a33f9d3e808)<br>
_Test machine deployment summary showing connection information._

### 2.4 Deploying the Splunk Server (MyLab-Splunk)
For our SIEM platform, select **Shared CPU**, choose your preferred location, and then select **VC2-4c-8GB** (higher specifications are required for log processing and analysis).

![image](https://github.com/user-attachments/assets/14348738-8545-47cd-9bac-412513d59961)<br>
_Splunk server configuration displaying the higher resource allocation needed for SIEM operations._

Select **Ubuntu 22.04 x64** as the operating system. Remember to deselect automatic backups and click deploy.

![image](https://github.com/user-attachments/assets/856b5c56-5c44-4c97-b0cb-1550a82be2bb)<br>
_Ubuntu operating system selection for our Splunk platform deployment._

Access the newly deployed Linux machine and **take note of the public IP address, username, and password**.

![image](https://github.com/user-attachments/assets/a6c8c075-0a68-4924-a82b-b6655f873bab)<br>
_Ubuntu server deployment summary showing SSH connection details._

### 2.5 Configuring Firewall Rules
Now we'll set up the firewall group to grant secure remote access to our VMs. We need to allow access to:
-   **SSH port 22** (for Linux administration)
-   **RDP port 3389** (for Windows remote access)
-   **Port 8000** (for Splunk web interface)
For security during testing, set the Source for all connections to **"My IP"** to restrict access to your current location.

![image](https://github.com/user-attachments/assets/7085be3a-3ea8-4c0d-aa50-1526ac7d947c)<br>
_Firewall rules configuration displaying the essential ports needed for remote administration and Splunk access._

### 2.6 Setting Up VPC Network
To establish secure internal communication between VMs, we'll attach a VPC (Virtual Private Cloud) to each machine. This provides private network connectivity within the cloud environment.
For each VM, select the machine, click on **Settings**, then **VPC Networks**, and click the **Attach VPC** button. The process takes a few seconds to complete.
**Important:** Record the **Private IP address and Network Mask** for each machine - these will be needed to configure network adapters on the Windows machines.

![image](https://github.com/user-attachments/assets/3e6af083-bdab-4e8c-a8ca-64430d570d05)<br>
_VPC network configuration showing private IP assignments for internal communication between VMs._

### 2.7 Active Directory Configuration
Deploy Active Directory Services on the AD Server using the private IP address on network adapter 2.
**Note:** I won't detail the AD installation steps here, as this should be fundamental knowledge for cybersecurity professionals. If needed, numerous online tutorials are available to cover the deployment of Windows Server Active Directory.

![image](https://github.com/user-attachments/assets/a4e7c89a-68d4-4a3f-91a7-5c6a8a85b66f)<br>
_Active Directory configuration completed with domain controller promotion successful._

After AD configuration and promotion, create a domain user for testing purposes. In this example, I created a user named **Jenny Smith (JSmith)** with a secure password, deselecting the options "Change password at first login" and "Password never expires" for lab convenience.

![image](https://github.com/user-attachments/assets/442981ff-56af-4021-bc86-672477439938)<br>
_Domain user creation interface showing the test user account setup for our lab environment._

### 2.8 Joining the Test Machine to the Domain
Configure the test machine to join our domain by setting up network adapter 2 with its VPC private IP address and using the AD server's private IP as the DNS server.

Navigate to Windows Settings → **Rename this PC (advanced)** → **Change** → select **Domain** → enter your domain name. The system will prompt for administrator credentials. Upon successful authentication, the machine will restart and join the domain.

![image](https://github.com/user-attachments/assets/8800341c-7d42-40b5-aa78-3c394da975af)<br>
_Domain join interface displaying successful integration of the test machine with our Active Directory domain._

### 2.9 Ubuntu Splunk Server Setup
SSH to the Ubuntu VM using PowerShell with the root user credentials. If the connection fails, verify that your firewall rules are correctly configured.

![image](https://github.com/user-attachments/assets/a568d4bd-f485-44bb-a85d-a922b427ec21)<br>
_SSH connection establishment to the Ubuntu server for Splunk installation._

Verify network configuration using the `ip a` command. Ensure the second network interface displays the VPC private IP address.

![image](https://github.com/user-attachments/assets/1baa2429-e809-4460-9b6f-3b02dc73e5ef)<br>
_Network interface configuration showing both public and private IP assignments._

Before any installation, update and upgrade the Linux system using: `apt-get update && apt-get upgrade`

![image](https://github.com/user-attachments/assets/4314ecf3-72be-4464-a4ac-ba886fd32fe1)<br>
_System update and upgrade process, ensuring the latest security patches and software versions._

```bash
wget -O splunk-9.4.3-237ebbd22314-linux-amd64.deb "https://download.splunk.com/products/splunk/releases/9.4.3/linux/splunk-9.4.3-237ebbd22314-linux-amd64.deb"
```

![image](https://github.com/user-attachments/assets/a8282480-e72b-408d-b201-a87fb44827a9)<br>
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

![image](https://github.com/user-attachments/assets/0b9a589f-01dc-42cc-87be-ca07884ea6fb)<br>
_Splunk installation completed successfully with web interface accessible on port 8000._

![image](https://github.com/user-attachments/assets/1290fe34-397c-4922-9db1-432aee1d2484)<br>
_Splunk Login Web Interface_

Just a quick note: when I tried to access the Splunk web interface remotely, my Bitdefender EDR interfered with the connection. If you run into the same problem, check your EDR settings first. 

This completes the virtual machine deployment phase. All three VMs are now configured with proper networking and are ready for the next phase of the project.

## Step 3: Splunk Web Interface Configuration
### 3.1 Accessing Splunk Web Interface
Now we'll make essential adjustments to Splunk through the web interface. You can access Splunk remotely through a web browser using the **Ubuntu public IP address** or from any Windows VM using the **Ubuntu private IP address**.
**Access URLs:**
-   **Remote access:** `http://[Ubuntu_Public_IP]:8000`
-   **Internal access:** `http://[Ubuntu_Private_IP]:8000`

![image](https://github.com/user-attachments/assets/348e0a7c-ade6-4c89-945f-21d404116afe)<br>
_Splunk web interface login page showing successful remote access to our SIEM platform._

### 3.2 Creating a Custom Index
We need to create a dedicated index where we'll collect telemetry from our Windows machines. Navigate to **Settings** → **Indexes**.

![image](https://github.com/user-attachments/assets/bc14f4af-6525-4cc7-a42f-b723b2d589ed)<br>
_Splunk Settings interface showing the path to index management for creating our custom data collection point._

Click **New Index** and create an index for our Windows event data (e.g., "WinEventLog://Security" or "WinEventLog://System"). This dedicated index will help organise and efficiently search through our Active Directory and test machine telemetry.

![image](https://github.com/user-attachments/assets/c08628fa-a7d3-4564-aabb-554488f656ae)<br>
_Index creation interface and confirmation showing our new Windows telemetry index is properly configured and active._

### 3.3 Configuring Data Receiving (Port Forwarding)
**CRITICAL STEP:** Before configuring forwarders, we must enable Splunk to receive data from remote machines.

Navigate to **Settings** → **Forwarding and receiving**.

![image](https://github.com/user-attachments/assets/c319c223-b84d-45ba-a850-f97cc364ab10)<br>
_Settings navigation showing the Forwarding and receiving configuration needed for data collection setup._

Under **"Receive data"** section, click on **Configure receiving**.
![image](https://github.com/user-attachments/assets/fdbc6ad0-f7d5-45a9-b46e-c1e181683cb0)<br>
_Forwarding and receiving interface displaying the Configure receiving option for setting up data collection ports._

Click on **New Receiving Port** to create a new data input port.

![image](https://github.com/user-attachments/assets/1fc0d1a7-4515-48b6-a7fa-d16fe62b1d1d)<br>
_Receiving configuration interface showing the option to create a new port for data collection from forwarders._

Under **Configure receiving**, set **"Listen on this port"** to **9997** and click **Save**. This configures Splunk to receive data on TCP port 9997, which is the standard port for Splunk Universal Forwarders.

![image](https://github.com/user-attachments/assets/c830a7fc-3a9c-4cc1-b621-e55c983b31e2)<br>
_Port configuration interface displaying port 9997 setup, enabling Splunk to receive forwarded data from our Windows machines._

### 3.4 Configuring Time Zone Settings
For accurate log correlation and incident timeline analysis, we need to set our Time Zone to **(GMT) Greenwich Mean Time**.
Navigate to your **account menu** (top right) → **Preferences** → **Time Zone** → select **GMT** → click **Apply**.

![image](https://github.com/user-attachments/assets/a5fe7c10-c841-48a5-bb17-e047de6fc62e)<br>
_Time zone configuration ensuring all timestamps are standardised to GMT for consistent log analysis across our distributed environment._

### 3.5 Installing Windows Add-on
To correctly parse and analyse Windows event logs, we need to install the Microsoft Windows Add-on. Click on **Apps** → **Find More Apps**.

![image](https://github.com/user-attachments/assets/9309bebb-7f41-485b-b73f-4dd09b947702)<br>
_Splunk Apps management interface showing the path to browse and install additional applications._

In the search field, type **"windows"** and locate the **"Splunk Add-on for Microsoft Windows"**. Click **Install** to add this essential component for Windows log parsing and field extraction.

![image](https://github.com/user-attachments/assets/62d21621-be92-4b20-a928-0955a45a6e5b)<br>
_Splunk app store showing the Microsoft Windows Add-on that will enable proper parsing of Windows Event Logs and Active Directory security events._

**Why This Add-on is Important:**
-   Provides pre-built field extractions for Windows Event Logs
-   Includes knowledge objects for Active Directory security events
-   Enables proper parsing of authentication, logon, and security policy events
-   Essential for SOC analyst workflows involving Windows infrastructure

After installation, the add-on will automatically begin parsing Windows event data with proper field mapping, making our security event analysis much more efficient and accurate.
**Important:** The port 9997 configuration is essential - without this step, the Universal Forwarders won't be able to send data to Splunk, and you'll see connection errors in the forwarder logs.
This configuration prepares Splunk to receive and properly process the security telemetry we'll be generating from our Active Directory environment and test machine in the subsequent steps.

## Step 4: Installing and Configuring Splunk Universal Forwarder on Windows Machines
### 4.1 Downloading Splunk Universal Forwarder
Now it's time to install and configure Splunk on our Windows machines to send security telemetry to our Security Information and Event Management (SIEM) system. We need to install the **Splunk Universal Forwarder**, which serves as a lightweight agent for collecting and forwarding logs.
You can head to the Splunk website and download the Universal Forwarder binary. You'll need a free Splunk account for the download. Navigate to **Platform** → **Free Trials & Downloads** → under **Universal Forwarder** → click **Get My Free Download**.

![image](https://github.com/user-attachments/assets/66a12f3f-a783-458b-81de-c1646e503bb5)<br>
_Splunk download portal displaying the Universal Forwarder option for Windows log collection and forwarding._

### 4.2 Installing Universal Forwarder on Both Windows Machines
**Install the forwarder on both MyLab-ADDC01 and MyLab-Test01:**
Double-click the downloaded executable to begin installation. Check the box to **accept the License Agreement**, then click **Next**.

![image](https://github.com/user-attachments/assets/a66883cb-b531-4758-bdec-1205aa319622)<br>
_Installation wizard displaying the license agreement that must be accepted to proceed with the forwarder installation._

For the username field, create a username of your choice (e.g., "admin"). For the password, leave it set to **'Generate random password'** for security.

![image](https://github.com/user-attachments/assets/ea5b8e0c-4e90-4d79-b8d6-04dd5df640d9)<br>
_Service account configuration showing username creation and automatic password generation for the Splunk service._

Since we're using Splunk for lab purposes, skip the deployment server configuration window and click **Next**.

![image](https://github.com/user-attachments/assets/70e50afd-21c0-46d7-b225-dc08e7d58691)<br>
_Deployment server configuration screen that can be skipped for our lab environment setup._

In the receiving indexer configuration, enter the **Splunk server's private IP address** and port **9997** (the default Splunk forwarding port).

**Format:** `[Splunk_Private_IP]:9997`

![image](https://github.com/user-attachments/assets/b5fda341-9a3a-4f46-8eba-8bf7519c1599)<br>
_Receiving indexer configuration specifying our Splunk server's IP address and the standard forwarding port 9997._

### 4.3 Configuring Log Collection
Now we need to configure which logs the forwarder will send to Splunk manually. Navigate to: `C:\Program Files\SplunkUniversalForwarder\etc\system\default`

**Copy** the `inputs.conf` file from the default directory.

![image](https://github.com/user-attachments/assets/1f51c6f0-0571-4b58-9e37-29d508a2b446)<br>
_File system navigation showing the location of the default inputs.conf configuration file that needs to be copied._

**Paste** the `inputs.conf` file into: `C:\Program Files\SplunkUniversalForwarder\etc\system\local`

![image](https://github.com/user-attachments/assets/d1720b32-3d5b-4202-8c2e-843ee6748870)<br>
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

![image](https://github.com/user-attachments/assets/200f0bc3-9cc4-4396-8c07-4bf6b100e40a)<br>
_Configuration file modification showing the Windows Security Event Log collection settings pointing to our custom Splunk index._

Save and close the file after adding the configuration.

### 4.5 Configuring Service Permissions
Access **Windows Services** to configure the Splunk service permissions. Search for **"SplunkForwarder"** service and open its properties.

![image](https://github.com/user-attachments/assets/612f8990-2689-4d25-9acc-51e08dcad131)<br>
_Windows Services management interface displaying the SplunkForwarder service that needs permission configuration._

Navigate to the **Log On** tab and select **"Local System Account"** under "Log on as". This ensures the service has sufficient privileges to read Windows Security Event Logs. Click **OK** to apply.

![image](https://github.com/user-attachments/assets/bf692c74-fda0-41fb-a7f5-e22a847366ad)<br>
_Service configuration showing Local System Account selection, which provides necessary permissions for reading security event logs._

### 4.6 Starting the Forwarder Service
Right-click on the **SplunkForwarder** service and select **Restart**. If the restart fails, right-click again and choose **Start**.

![image](https://github.com/user-attachments/assets/01769e91-3ce1-4782-8e57-1155252ade8b)<br> 
_Service management showing the restart process and confirmation that the SplunkForwarder is running and collecting logs._

**Verification Steps:**
-   Confirm the service status shows "Running"
-   Check that no error messages appear in the service startup
-   Verify network connectivity to the Splunk server on port 9997

**Repeat this entire process on both Windows machines** (MyLab-ADDC01 and MyLab-Test01) to ensure comprehensive log collection from your Active Directory environment.<br>
Once both forwarders are configured and running, they will begin sending Windows Security Event Logs to your Splunk server, providing the telemetry needed for our SOC analysis and automated response workflows.

### 4.7 Open port 9997 on the Ubuntu Splunk Machine
The final step is to open port 9997 on the Ubuntu machine, using the following command `ufw allow 9997`.

## Step 5: Verifying Telemetry and Creating Security Alerts
### 5.1 Verifying Data Collection

Now we need to verify that Splunk is receiving telemetry from our Windows machines. Log in to your Splunk interface using either the **Ubuntu public IP** from your local machine or the **Ubuntu private IP** from one of the Windows VMs, then enter your credentials.

![image](https://github.com/user-attachments/assets/f399d2a9-9c81-46a9-ab9d-bcdc3c8796fc)<br> 
_Splunk web interface, login page._

Click on **Search & Reporting** to access the search interface.

![image](https://github.com/user-attachments/assets/90d37d45-729c-4c8d-8e6d-e80992111f53)<br> 
_Splunk main dashboard displaying the Search & Reporting application needed for data analysis._

In the search box, type `index="[your_index_name]"` (replace with the index you created) and press Enter or click the magnifying glass icon.

![image](https://github.com/user-attachments/assets/0c800814-9617-4114-9353-2d9a1d3feeb3)<br>
_Search interface displaying the index query to verify data collection from our Windows forwarders._

You should see logs from both Windows machines from the last 24 hours. **If no telemetry appears, verify:**
-   Port 9997 is correctly configured in Splunk
-   `inputs.conf` files are correctly set up on both Windows machines
-   SplunkForwarder services are running with the  correct IP and port
-   Index names match between forwarders and the Splunk server

![image](https://github.com/user-attachments/assets/b2af2626-a61b-4492-a2aa-3aaa7ed9b90d)<br> 
_Search results displaying Windows Event Logs successfully collected from both MyLab-ADDC01 and MyLab-Test01._

### 5.2 Confirming Multi-Host Data Collection

Check that you're receiving telemetry from both Windows machines. In the **Selected Fields** panel on the right, click on **host**. Both machine names should be listed to confirm data collection from your entire environment.

![image](https://github.com/user-attachments/assets/6b0ec405-02f9-4231-9c82-2c588ddb7db4)<br>
_Host showing MyLab-ADDC01 and MyLab-Test01_

### 5.3 Creating Unauthorised Login Detection Rule
To create an alert for unauthorised successful logins, we need to identify the appropriate Windows Event IDs. For successful logins, we use:
-   **Event Code 4624** (Successful Logon)
-   **Logon Types 7 and 10** (Remote Interactive and RemoteInteractive)

First, filter by Event Code: `index="mylab" EventCode=4624`

![image](https://github.com/user-attachments/assets/647164f2-0431-4e55-9f33-d0545b42ec9f)<br> 
_Search results displaying Windows Event ID 4624 logs representing successful authentication events._

### 5.4 Refining the Detection Logic
Add Logon Type filtering: `index="mylab" EventCode=4624 (Logon_Type=7 OR Logon_Type=10)`

This filters events containing logon types 7 or 10 for Event Code 4624, focusing on remote interactive sessions.

![image](https://github.com/user-attachments/assets/7d5909d0-0b13-4b2c-bdc7-73c41461d8d6)<br> 
_Enhanced search query targeting specific remote logon types for more precise alerting._

**Note:** If you've been working on this project for several days, change the time range from "Last 24 hours" to "All time" to see all ingested data.

### 5.5 Analysing Source IP Addresses
Could you look over the **Source\_Network\_Address** field to identify connection sources? This field shows all IP addresses that have connected to your Windows machines.

![image](https://github.com/user-attachments/assets/62810018-27e9-4ece-b93b-5cccaf8d444b)<br> 
_Enhanced search query targeting specific remote logon types for more precise alerting._

**Note:** The IP values in your environment will differ from the screenshots, as this represents accumulated data from multiple days of testing and external connections.

### 5.6 Cleaning the Data
Remove null values by filtering out empty Source\_Network\_Address entries: `index="mylab" EventCode=4624 (Logon_Type=7 OR Logon_Type=10) Source_Network_Address!="-"`

![image](https://github.com/user-attachments/assets/c471065e-f769-4ddf-8483-bbcd02e78c40)<br> 
_Data refinement removing null values for cleaner analysis and more accurate alerting. The Source IP analysis reveals connection patterns and potential unauthorised access attempts._

### 5.7 Excluding Authorised Connections
Exclude your authorised public IP address to prevent false positives: `index="mylab" EventCode=4624 (Logon_Type=7 OR Logon_Type=10) Source_Network_Address!="-" Source_Network_Address!="[Your_Public_IP]"`

**Security Note:** In this example, only the first three octets are used for demonstration. In production, use your complete public IP address for precise filtering.

![image](https://github.com/user-attachments/assets/cb83c459-6596-4a1b-9c87-95d45559f55a)<br> 
_Alert logic excluding legitimate administrative connections to reduce false positive alerts._


### 5.8 Creating a Formatted Alert Query
Transform the results into a clean table format:

```splunk
index="mylab" EventCode=4624 (Logon_Type=7 OR Logon_Type=10) Source_Network_Address!="-" Source_Network_Address!="203.*"
| stats count by _time, ComputerName, Source_Network_Address, user, Logon_Type
```

![image](https://github.com/user-attachments/assets/11b57184-65f0-4ee5-9a6f-a04cc39c56b3)<br> 
_Structured alert output showing essential fields for incident response: time, computer, source IP, user, and logon type._

### 5.9 Saving the Alert
Click **Save As** → **Alert** to create the detection rule.
![image](https://github.com/user-attachments/assets/877244a2-5ecb-428c-81c3-d1d86e934489)<br> 

Configure the alert settings:
-   **Title:** "MyLab-Unauthorized-Successful-Login-RDP"
-   **Permissions:** Private
-   **Alert Type:** Scheduled
-   **Schedule:** Run on Cron Schedule
-   **Time Range:** Past 60 minutes
-   **Cron Expression:** `* * * * *` (runs every minute)
-   **Trigger Actions:** Add to Triggered Alerts
-   **Severity:** Medium

Click **Save** and close any warning windows.

![image](https://github.com/user-attachments/assets/36a43555-c0de-45f6-8cf0-1d4c4dd95236)<br> 
_Alert creation interface for converting our search query into an automated detection rule._

### 5.10 Configuring the Firewall for Testing
Modify Vultr firewall rules to allow external connections for testing. Navigate to **Network** → **Firewall** and edit the existing rules. Remove the restrictive RDP rule and create a new one allowing broader access for testing purposes.

![image](https://github.com/user-attachments/assets/3fd8ef2e-2842-466d-816f-800ff629e947)<br>
_Firewall modification enabling external access for alert testing while maintaining security controls._

### 5.11 Testing the Alert System
Test the alert using a device with a different public IP address. Use mobile data or a VPN service to connect from an IP outside your authorised range.

Connect to the test machine via RDP using:
-   **IP:** MyLab-Test01 public IP
-   **Username:** JSmith
-   **Password:** \[Domain user password\]
   
![IMG_1312](https://github.com/user-attachments/assets/b5c2e4c6-9bfe-4e1c-b0ed-d79cbb5b5f88)<br>
_Mobile device RDP connection demonstrating unauthorised access simulation for alert testing._

### 5.12 Verifying Alert Triggers
After successful login, navigate to **Activity** → **Triggered Alerts** to confirm the detection rule is working.

![image](https://github.com/user-attachments/assets/301e7607-5e30-4405-be97-47342f4cc0a4)<br> 
_Triggered Alerts dashboard displaying successful detection of unauthorized login attempts._

You should see alerts triggered by the unauthorised connections. Just to remind you, alerts will continue to trigger every minute as configured.

![image](https://github.com/user-attachments/assets/abc68997-38ac-40a1-b256-381749863466)<br>
_Alert details displaying the mobile device's public IP address detected by our security rule._


![image](https://github.com/user-attachments/assets/efa00f90-5655-4d09-903a-782ea5399d2b)<br>
_Additional alert showing VPN connection detected, confirming multi-source threat detection capability._

### 5.13 Validation Complete
Now that we've confirmed our detection rule is working and alerts are being appropriately triggered, we can proceed to the final integration step with Shuffler and Slack for automated incident response.

The alert system successfully detects unauthorised remote logins and will serve as the trigger for our SOAR automation workflow.



















































![image](https://github.com/user-attachments/assets/7d969d9f-381e-41bb-8f7f-fedf7ed469e5)

![image](https://github.com/user-attachments/assets/ec44f6d9-db41-4747-862d-b7604cd2283e)

![image](https://github.com/user-attachments/assets/38a10e5f-d51f-4247-beed-cef33078655a)

![image](https://github.com/user-attachments/assets/3ea1c622-fe78-4baa-aa6b-f0be86fc807f)

![image](https://github.com/user-attachments/assets/58951da8-f8f6-46b0-803e-f327d894c44a)

![image](https://github.com/user-attachments/assets/d6df7c0e-542d-4e7a-a942-4931bd155d00)

![image](https://github.com/user-attachments/assets/8448e8c0-36f8-4498-b405-d5f7bf20692a)

![image](https://github.com/user-attachments/assets/37c701c7-6b84-4d61-8302-6a0536a9594e)

![image](https://github.com/user-attachments/assets/98d5b8d6-ecfa-4a68-b22c-95fe3b9b4291)



dsdsddssdds

-----------------------------------------------------------------------------------------------------------
-------------------------------------------------------------------------------------------------------------

## Step 6: SOAR Automation with Shuffler and Slack Integration

### 6.1 Setting Up Shuffler Workflow

First, create an account on Shuffler and log in to your newly created account. Click on **Create Workflow**, choose a descriptive name for your workflow (e.g., "MyLab-Unauthorized-Login-Response"), and save changes.

![image](https://github.com/user-attachments/assets/5007944d-8044-4921-9b3f-a8c3728d112b) 
_Shuffler main dashboard showing the workflow creation interface for building our automated incident response process._

### 6.2 Adding Webhook Connector

You'll be directed to the workflow design area. From the left panel, grab a **Webhook** component and drop it onto the work area. The Webhook serves as the connection bridge between Shuffler and Splunk, receiving filtered alert data.

![image](https://github.com/user-attachments/assets/b43e1d44-5cfe-4377-96a8-d6b9e81b67de) 
_Workflow designer showing the Webhook component that will receive alert data from our Splunk detection rule._

Click on the webhook component. In the properties panel on the right, assign a descriptive name for the hook (e.g., "Splunk-Alert-Receiver").

![image](https://github.com/user-attachments/assets/9e1460f4-63ea-49be-bef3-d49e74ae0bc4) 
_Webhook configuration interface showing the naming and basic setup for receiving Splunk alerts._

### 6.3 Configuring Splunk Webhook Integration

Copy the **Webhook URI** from Shuffler and open the Splunk web interface in another tab.

![image](https://github.com/user-attachments/assets/4f4f6c58-8044-4090-b801-91e2d8e766f2) 
_Webhook URI generation showing the endpoint that Splunk will use to send alert data to our SOAR platform._

In Splunk, navigate to **Search and Reporting** → **Alerts** → **Edit** the alert you created earlier.

![image](https://github.com/user-attachments/assets/60f6fe46-9ce3-4ce1-bce7-5f34548b6aae) 
_Splunk alert management interface showing the path to modify our existing unauthorised login detection rule._

Scroll down to **Actions** and select **Webhook** from the available trigger actions.

![image](https://github.com/user-attachments/assets/e691800a-d6a5-45f8-91a7-aad3696c645e)
_Alert action configuration displaying the webhook option for external system integration._

Paste the URI copied from Shuffler into the webhook URL field and verify the alert status shows **Enabled**.

![image](https://github.com/user-attachments/assets/3cdd95b0-9210-49b5-93e9-3baff6d29ecd) 
_Webhook integration showing the connection between Splunk alerts and our Shuffler automation workflow._

### 6.4 Testing Webhook Connectivity

Return to Shuffler and click the **Start** button to begin listening for data from Splunk.

![image](https://github.com/user-attachments/assets/0cf948b3-8e11-4365-bf54-9875295d0774) 
_Shuffler workflow activation showing the webhook listener ready to receive alert data from Splunk._

If no remote connections have been generated in the last 60 minutes, create new telemetry by connecting from an unauthorised IP address (the alert runs every minute for 60-minute intervals).

![image](https://github.com/user-attachments/assets/3f558153-e82b-442d-805e-ac3b7da82dcd) 
_Alert generation demonstration showing how unauthorised connections trigger our detection rule._

### 6.5 Verifying Data Reception

After generating new alerts, check if the Webhook is receiving data by clicking **Explore Runs** in Shuffler.

![image](https://github.com/user-attachments/assets/98db5b2b-89c5-417e-96c8-8d5608d29054) 
_Workflow execution history displaying successful alert reception from our Splunk detection system._

Open one of the alerts to confirm the data structure. You should see the public IP address of your connection and the username (JSmith) in the alert payload.

![image](https://github.com/user-attachments/assets/ef467126-8619-4b4b-b67f-2c2a18b830a8) 
_Alert payload analysis confirming proper data transmission, including source IP, user, and connection details._

### 6.6 Disabling Alert for Development

Now that we've confirmed data flow, return to Splunk and **disable** the alert temporarily to prevent continuous triggering while building the workflow.

![image](https://github.com/user-attachments/assets/f01f4075-e061-48ac-bf68-995e04d7f494) 
Alert management is temporarily disabled to prevent workflow interference during development._

### 6.7 Adding Slack Integration

In Shuffler's search field (top left), type **"Slack"**, click on it, and drag the Slack connector to the work area.

![image](https://github.com/user-attachments/assets/85e9d293-b892-4ef3-8295-a371fe12f4ca) 
_Workflow expansion showing Slack integration for real-time security alert notifications._

Click on the Slack component. In the properties panel, change the name to something descriptive and select **"Chat post message"** from the Actions dropdown.

![image](https://github.com/user-attachments/assets/7b7a9165-fbb2-4cf7-a94b-171adc7e1ae6)
_Slack connector configuration displaying the message posting capability for alert notifications._

### 6.8 Setting Up Slack Workspace

You need a Slack account for authentication. If you don't have one, go to the Slack website and click **Get Started**.

![image](https://github.com/user-attachments/assets/d8b638c8-e878-4b21-8fcb-4543a93f3c66) 
_Slack account creation showing the registration process needed for workspace integration._

Enter your email and continue. Enter the verification code received in your email, then create your workspace by selecting **"Create a workspace"**.

![image](https://github.com/user-attachments/assets/6122d062-c568-45f5-848c-4a9b060d8869)  
_Slack workspace setup for receiving security alerts from our automated response system._

Pick a company name, enter your details, skip additional setup options, and choose **"Start with the Limited Free Version"**.

![image](https://github.com/user-attachments/assets/4bf43e5c-9aa6-4029-b983-59f56edd161b)
![image](https://github.com/user-attachments/assets/406bdc60-d7c9-4959-857d-cbf14500e1b7)
![image](https://github.com/user-attachments/assets/1f4b4ee3-815b-475c-90fd-b4268333d1ea) 
_Slack workspace finalisation showing the free tier setup sufficient for our security alert system._

### 6.9 Authenticating Shuffler with Slack

Return to Shuffler and click **"One-click Login"** to authenticate with Slack.

![image](https://github.com/user-attachments/assets/52b6a2bc-93a9-4402-9aab-b826817bd99b)
_Authentication interface connecting our workflow automation with the Slack notification system._

When the pop-up appears, select your workspace and click **"Allow"** in the top right corner.

![image](https://github.com/user-attachments/assets/cac9d40b-7d5f-4810-9fa1-d177eacb9a9b)
_OAuth authorisation showing permission grant for Shuffler to send messages to our Slack workspace._

### 6.10 Creating Security Alert Channel

In Slack, navigate to **Channels** → **Create Channel**. Select **"Blank Channel"**, choose a name (e.g., "security-alerts"), and create the channel.

![image](https://github.com/user-attachments/assets/e7d97cab-9846-4594-8eee-1ed7a6e2fd6a)
_Slack channel setup for dedicated security alert notifications from our SOAR automation._
![image](https://github.com/user-attachments/assets/63c84d30-8ea3-4714-8aba-5529dfce501c) 
_Labelling the channel._

Copy the **Channel ID** from the browser's address bar when viewing the channel.

![image](https://github.com/user-attachments/assets/83c931e9-c067-4061-a219-0bb5eff9684e)
_Channel identification process for configuring Shuffler to send alerts to the correct Slack channel._

### 6.11 Connecting Workflow Components

Paste the Channel ID into the **Channel** field in Shuffler's Slack connector configuration.

![image](https://github.com/user-attachments/assets/772e847b-53f3-4576-b2f0-927323e5117d) 
_Slack connector finalisation showing the channel targeting for automated alert delivery._

Connect the Webhook output to the Slack input by drawing a line between the components.


_Workflow connection demonstrating data flow from Splunk alerts to Slack notifications._

### 6.12 Testing Slack Integration

Click **"Explore Runs"**, select one of the previous runs, and **rerun** it. This generates the field mappings needed for the next step.

![image](https://github.com/user-attachments/assets/aa10c152-638f-434c-be3f-5bdc344b3b0a) 
_Workflow testing showing rerun capability for validating component integration and data flow._

Click on the Slack component, find the **Text** field in properties, click the **+** button, and select **"Run Argument"**.

_\[Fig 74 - Screenshot of Slack message field configuration with run arguments\]_ 
_Dynamic message configuration showing how to include alert data in Slack notifications._

You can select the arguments you want to send to Slack (e.g., user, source IP, computer name, time).

_\[Fig 75 - Screenshot of argument selection for Slack message content\]_ 
_Alert data selection for creating comprehensive security notifications with relevant incident details._

Rerun one of the logs from **Explore Runs** to test the complete integration.

_\[Fig 76 - Screenshot of successful Slack message delivery\]_ 
_Slack notification verification showing successful alert delivery with incident details._

### 6.13 Adding Email Trigger for Response Decision

Add a **Trigger** component from the left panel and drag it to the work area to create interactive response options.

_\[Fig 77 - Screenshot of Trigger component being added to workflow\]_ 
_Workflow enhancement showing the addition of interactive response capabilities for security analysts._

Click on the Trigger component, name it appropriately, set **Input options** to **"email"**, and click the arrow on **"Information"**.

_\[Fig 78 - Screenshot of Trigger configuration for email-based response\]_ 
_Interactive trigger setup enabling email-based decision making for incident response actions._

### 6.14 Crafting Response Email Template




