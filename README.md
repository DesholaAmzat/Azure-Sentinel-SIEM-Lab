
<h1>Azure-Sentinel-SIEM-Lab
<br/> 

<h2>Description</h2>
In this lab, I was able to set up my own virtual machine in Azure, and turn off the firewall on the VM. This allows anyone across the world to try to access my VM, effectively turning it into a honeypot. From there, I was able to set up a SIEM in Azure Sentinel to ingest the Security logs, which I used a custom Powershell script to extract their IP address from the log, and then the script would forward the address to an API to derive geolocation data. After this, I configured the Sentinel workbook to display all the failed RDP brute force attempts on a world map, using the logs generated from Windows event viewer and the Powershell script.

<h2> Languages and Utilities Used</h2>

- Micosoft Azure Portal
- Microsoft Azure Sentinel
- Powershell
- Kusto Query Language (KQL)


<h2> Environments Used</h2>

- Windows 10 

<h2> Program Walk Through</h2>

Setup Azure             |  landing page          |             Create a VM
:-------------------------:|:-------------------------:|:-------------------------:
![](https://i.imgur.com/NSur3hf.png)  |  ![](https://i.imgur.com/F5U0Qbn.png)  |  ![](https://i.imgur.com/GLRFaD4.png)

The first thing you want to do, is navigate to the Azure website and start a free trial. You will need a credit card on hand, but, you are given $200 of free credits which is more than enough for this lab. The link to create an Azure account can be found  <br>[here.](https://azure.microsoft.com/en-us/pricing/purchase-options/azure-account/search?ef_id=_k_Cj0KCQiAqL28BhCrARIsACYJvkfx82ByVL_YMl4RFQUMCFTN8gHeh54rLKU29G_bHIgplZ4fTXFKWq4aAgZdEALw_wcB_k_&OCID=AIDcmmfq865whp_SEM__k_Cj0KCQiAqL28BhCrARIsACYJvkfx82ByVL_YMl4RFQUMCFTN8gHeh54rLKU29G_bHIgplZ4fTXFKWq4aAgZdEALw_wcB_k_&gad_source=1&gclid=Cj0KCQiAqL28BhCrARIsACYJvkfx82ByVL_YMl4RFQUMCFTN8gHeh54rLKU29G_bHIgplZ4fTXFKWq4aAgZdEALw_wcB)</br> Once you get to this landing page and have your Azure account set up, you want to search in the search bar up top for virtual machines. Once you're brought to the virtual machine page click on create.
 
 
 Basics             |  Network Security Group         |             Networking          
:-------------------------:|:-------------------------:|:-------------------------:
![](https://i.imgur.com/DMMZpUy.png)  |  ![](https://i.imgur.com/XrHz1bA.png)  |  ![](https://i.imgur.com/vEUeJFI.png)   

These are the settings you would want to use to configure your VM for this lab. Notice the username and password section. Be sure to use a secure username and password for logging into your virtual machine. This is important as in this lab, Hackers from around the world will be trying to Brute Force their way in by guessing the password. On the networking page, choose the advanced option for NIC Network Security Group, and select the create new option to create a new network security group. Once your VM is created, it will take a while to load up, so in the meantime we will create a log analytics workspace.

<img src="https://i.imgur.com/PibSXip.png" height="88%" width="80%" /> 
Search for log analytics workspace, and then click create. Be sure to match your settings to the picture above. Now we need to enable logs from our virtual machine to be collected


<img src="https://i.imgur.com/IfseotM.png" height="88%" width="80%" /> 
<img src="https://i.imgur.com/6XjsFKr.png" height="88%" width="80%" /> 

Go to the "Microsoft Defender for Cloud" page and navigate to "Environment Settings". Select our honeypot, then under defender plans, be sure to turn "SQL server of machines" off. Save this, then under the data collection tab on the left, be sure to select "All events" and save

<img src="https://i.imgur.com/rUqgDZa.png" height="88%" width="80%" /> 
Go back to the log analytics workspace page, select virtual machine, and click on the VM we created earlier. We need the workspace to pull logs from the VM so click on the connect button once you have the specific VM selected. This will take a while so in a new tab go back to the azure portal page.

 <img src="https://i.imgur.com/1qgh2T6.png" height="88%" width="80%" /> 
On the Azure Portal Page, search for sentinel, and click on "Create MS Sentinel" and select the log analytics workspace from earlier

 VM IP ADDRESS             |  RDP INTO VM
 :-------------------------:|:-------------------------:|
 ![](https://i.imgur.com/052xxLk.png)  |  ![](https://i.imgur.com/yghWZR9.png)  

 Now that the log analytics workspace is done connecting to the VM, notice and copy the IP address of our VM. Now open a remote desktop protocol window and try to log into the VM via IP. Be sure to go to "More choices" and "Use a different account" to specify the username. For this step, we will be entering random characters for the password, as we want a failed attempt. I used the "ADMAAMZAT" username, which is the correct username, but for comprehension be sure to log in with a username like "TestLogin" so you can easily identify your attempts. 
 
 1             |  2         |             3          
:-------------------------:|:-------------------------:|:-------------------------:
![](https://i.imgur.com/lqmEROK.png)  |  ![](https://i.imgur.com/7U0Pmzu.png)  |  ![](https://i.imgur.com/c1MD0Xr.png)  

1. Log into the virtual machine and open the windows Event Viewer
2. Here you can see the highlighted of that is an audit failure and has an event code of 4625, which means login failed
3. If you pay attention under Network information, we can find out more about the device that attempted to log in.


<img src="https://i.imgur.com/mYKdpnp.png" height="88%" width="80%" /> 
Now in our actual machine, we will attempt to Ping the IP address of our virtual machine. Notice the request timed out because our firewall on the virtual machine is blocking traffic.

<img src="https://i.imgur.com/Eu5rxvo.png" height="88%" width="80%" /> 
On the virtual machine let's open Windows Firewall by typing “wf.msc” in the search bar. Click on Windows Defender Firewall properties and turn the firewall State off for the domain, public, and private profiles 


<img src="https://i.imgur.com/kMLBD2g.png" height="88%" width="80%" /> 
Now you'll see our ping command which has been running from earlier is now successful, since we've turned off the firewall, and echo requests are allowed. This is great, because it means our honey pot is now ready to be discovered by hackers.

<img src="https://i.imgur.com/Mkm2wxG.png" height="88%" width="80%" /> 
Now in our virtual machine, let us open up Powershell ISE, and paste our custom Powershell script into the terminal. This script is made to collect every failed login attempt from the event Viewer security log, take the IP address from that event, and plug it into a third party API, in order to derive the geolocation of the event in latitude, longitude, as well as state and Country. Be sure to save this script, I saved it under the name "Log_Exporter"

<img src="https://i.imgur.com/A8CqETE.png" height="88%" width="80%" /> 

<br>[Click this link to be taken to the website for resolving IPs to their geolocation. Once you create an account you will be taken to the page with your API key](https://ipgeolocation.io/)</br>

<img src="https://i.imgur.com/Zu6lt3Q.png" height="88%" width="80%" /> 
Now plug your API key into the powershell script and let it run. Here you can see it already pulled the first failed log in attempt, from my IP 69.244.83.49. The powershell script automatically writes all this information to the Program data folder in the Windows(C:) folder on the "failed_rdp" file

<img src="https://i.imgur.com/bEWD5ql.png" height="88%" width="80%" /> 
Here you can see more failed log in attempts. All these attempts were by me. Pay attention to the username, as I used the actual username "ADMAAMZAT" and "TestLogin". Later on you will see the failed attempts from actual hackers try to brute force their way in with user names like "admin" and "Administrator". This highlights the importance of changing the default usernames and passwords on internet accessible devices.

<img src="https://i.imgur.com/szhtxyN.png" height="88%" width="80%" /> 
<img src="https://i.imgur.com/Z8nfcxn.png" height="88%" width="80%" /> 
I continued to let the script run overnight, and here we can see a greater number of attempts have occured from many different locations. Even though these events are not in the powershell terminal like the previous pictures, this is expected because our script writes them to a .txt file saved on our VM. Now we need to copy and paste the text from our VM to the notepad app on our actual machine and save this file. 


