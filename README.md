<h1>Email Attachment Analysis (Credential Harvester)</h1>


<h2>Description</h2>
In this laboratory, I will investigate an authentic credential harvesting operation that, rather than employing a URL within the email, utilises an attached file of a particular type.
<br />


<h2>Platforms and Technology Used</h2>

- <b>Azure</b> 
- <b>OpenVAS</b>
- <b>Virtual Machines</b>
- <b>SSH</b>
- <b>Remote Desktop</b>
<h2>Environments Used </h2>

- <b>Windows 10</b> (21H2)

<h2>Lab walk-through:</h2>

![Lab Overview](https://i.imgur.com/t43Gcrn.png)         

## Setting Up an OpenVAS Scanner on Azure

1. **Navigating to the Marketplace**: 
    - Open your browser and head to the Azure portal. Once you're in, go to the Marketplace and search for "OpenVAS secured and supported by HOSSTED".
    ![Marketplace](https://i.imgur.com/rScCNAs.png)
    ![OpenVas Marketplace selection](https://i.imgur.com/gOZcIbf.png)

2. **Choosing a Pre-Set Configuration**: 
    - Pick the option that says "Start with a pre-set configuration" and opt for the least secure one.

3. **Creating the Virtual Machine (VM)**: 
    - Click "Continue to Create VM" and input the following details.
    ![OpenVas Defaults](https://i.imgur.com/UzOSIH9.png)
    - Resource Group: `Vulnerability-Management`
    - VM Name: `OpenVAS`
    - Region and VNet: East US 2
    - Authentication: Username as `azureuser` and password as `Cyberlab123!`
    - Monitoring: Turn off Boot Diagnostic.

4. **SSH into Your New VM**: 
    - After your VM is set up, you can SSH into it using PowerShell if you're on Windows or Terminal if you're on MacOS.
    ![OpenVas Powershell access](https://i.imgur.com/VaJiKTH.png)

5. **Wait for Deployment**: 
    - You'll see a message that says "Your OpenVAS is deploying, please wait" followed by "hossted stage done".

6. **Access the Web App**: 
    - You should see the web app URL along with the default username and password. Try logging in with those credentials.
    ![Greenbone login](https://i.imgur.com/2iNUFdn.png)

## Spinning Up a Vulnerable Windows 10 VM

1. **Creating a New VM**: 
    - Navigate back to [Azure Portal](https://portal.azure.com) and search for Virtual Machines. Create a new one.
    ![Create Win10 VM](https://i.imgur.com/lHt8zJi.png)

2. **Configuration**: 
    - Use the following details.
    ![RDP into Win10 Vulnerable](https://i.imgur.com/s7PV1S5.png)
    - Resource Group: `Vulnerability-Management`
    - VM Name: `Win10-Vulnerable`
    - Region: East US 2
    - Virtual Network: Match your OpenVAS VM
    - Image: Windows 10 Pro
    - Size: Any with at least 2 vCPUs
    - Username: `azureuser`
    - Password: `Cyberlab123!`

3. **Making the VM Vulnerable**: 
    - Log in and disable the Windows Firewall.
   -![Win10 VM turn off Firewall](https://i.imgur.com/Pc7ICuf.png)
    ![Win10 VM turn off Firewall](https://i.imgur.com/0xgHZXe.png)
    - Install some outdated software.
    ![Old software downloads](https://i.imgur.com/PVhO6oK.png)
    - Restart the VM.
    

## Configuring OpenVAS for Initial Unauthenticated Scan

1. **Adding a New Host**: 
    - Log into OpenVAS and navigate to `Assets -> Hosts -> New Host`. Add the private IP address of your Windows 10 VM.
    ![Add new host to OpenVas](https://i.imgur.com/7vCMG4R.png)
    ![OpenVas NewTarget](OpenVas_NewTarget.png)

2. **Setting up a New Target**: 
    - Create a new target from the host you've just added. Name it "Azure Vulnerable VMs".

3. **Creating a New Task**: 
    - Navigate to `Tasks -> New Task`. Set the name and comments to "Scan - Azure Vulnerable VMs" and select your target.
    
    ![OpenVas Task](https://i.imgur.com/YJxxjGZ.png)
    ![OpenVas create new task](https://i.imgur.com/2bzgx3A.png)
4. **Starting the Scan**: 
    - Launch the task and wait for it to finish.
    ![OpenVas Task Complete](https://i.imgur.com/WMZ6uMY.png)

5. **Reviewing the Results**: 
    - Once the scan is over, check the date under "Last Report" to review the findings.
    ![OpenVas Results Non-credentialed scan](https://i.imgur.com/4LSUkng.png)

## Setting Up Credentialed Scans on the Windows VM

1. **Deactivate Windows Firewall**: 
    ![Win10 VM turn off Firewall](https://i.imgur.com/0xgHZXe.png)

2. **Disable User Account Control**: 
    ![Win10 VM Vulnerable disable user account control](https://i.imgur.com/Yl4CXCG.png)

3. **Enable Remote Registry**: 
    ![Win10 VM enable remote registry](https://i.imgur.com/zSa3xFG.png)

4. **Registry Edits**: 
    - Launch the Registry Editor (`regedit.exe`) in admin mode and navigate to `HKEY_LOCAL_MACHINE -> SOFTWARE -> Microsoft -> Windows -> CurrentVersion -> Policies -> System` and create a new DWORD (32-bit) value: `Name: LocalAccountTokenFilterPolicy`, `Value: 1`.
    ![Win10 VM set registry key](https://i.imgur.com/paV0cGQ.png)

5. **Restart the VM**: Don't forget to reboot to apply the changes.

## Configuring Credentialed Scans in OpenVAS

1. **New Credential**: 
    - Navigate to `Configuration -> Credentials -> New Credential`.
    ![OpenVas Configurations Credential Scans](https://i.imgur.com/nUc5eG6.png)

2. **Clone Existing Target**: 
    - Go back to `Configuration -> Targets` and clone the target you created earlier.
    ![OpenVas Configurations Credential Scans Clone](https://i.imgur.com/OJdmd6x.png)

## Running the Credentialed Scan

1. **Clone the Task**: 
    - In OpenVAS, go to `Scans -> Tasks`. Clone the previous task and edit its details.
    ![OpenVas Task](https://i.imgur.com/YJxxjGZ.png)

2. **Start the Scan**: Hit the play button to kick off the credentialed scan.

## Credentialed Scan Results

![OpenVas Results](https://i.imgur.com/hI07RMi.png)
![OpenVas Results1](https://i.imgur.com/bOaRDx5.png)

    
## Remediation Steps

1. **Uninstall the Software**: Log back into your Windows VM and uninstall Adobe Reader, VLC Player, and Firefox.
  
2. **Restart the VM**: Give the machine a quick restart.

## Verifying the Fixes

1. **Run the Credentialed Scan Again**: Head back to OpenVAS and rerun the `Scan - Azure Vulnerable VMs - Credentialed` task. Check the results to confirm that the vulnerabilities have been remedied.



<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
