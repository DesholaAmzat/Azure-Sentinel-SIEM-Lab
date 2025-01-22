
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

The first thing you want to do, is navigate to the Azure website and start a free trial. You will need a credit card on hand, but, you are given $200 of free credits which is more than enough for this lab. The link to create an Azure account can be found  <br>[here.](https://azure.microsoft.com/en-us/pricing/purchase-options/azure-account/search?ef_id=_k_Cj0KCQiAqL28BhCrARIsACYJvkfx82ByVL_YMl4RFQUMCFTN8gHeh54rLKU29G_bHIgplZ4fTXFKWq4aAgZdEALw_wcB_k_&OCID=AIDcmmfq865whp_SEM__k_Cj0KCQiAqL28BhCrARIsACYJvkfx82ByVL_YMl4RFQUMCFTN8gHeh54rLKU29G_bHIgplZ4fTXFKWq4aAgZdEALw_wcB_k_&gad_source=1&gclid=Cj0KCQiAqL28BhCrARIsACYJvkfx82ByVL_YMl4RFQUMCFTN8gHeh54rLKU29G_bHIgplZ4fTXFKWq4aAgZdEALw_wcB)</br> Once you get to this landing page and have your Azure account set up, you want to search in the search bar up top for virtual machines. Once you're bought to the virtual machine page click on create.
 
 
 Basics             |  Network Security Group         |             Networking          
:-------------------------:|:-------------------------:|:-------------------------:
![](https://i.imgur.com/DMMZpUy.png)  |  ![](https://i.imgur.com/XrHz1bA.png)  |  ![](https://i.imgur.com/vEUeJFI.png)   

These are the settings you would want to use to configure your VM for this lab. Notice the username and password section. Be sure to use a secure username and password for logging into your virtual machine. This is important as in this lab, Hackers from around the world will be trying to Brute Force their way in by guessing the password. On the networking page, choose the advanced option for NIC Network Security Group, and select the create new option to create a new network security group.

<img src="https://i.imgur.com/osYPbxc.png" height="88%" width="80%" /> 
Once you have reached this landing page, we now need to set our logs up to be ingested within the tool. Navigate to the settings tab in the top right corner, then select Data Inputs, and click on the edit button next to local event log collection. In this lab we will only deal with local events, instead of a remote system. 
<img src="https://i.imgur.com/DnktRws.png" height="88%" width="80%" />
In this project, we will only focus on the application, security, and system logs, because those are the basic logs used in most, if not all SIEMS, Once you have selected these three be sure to click on Save.
<img src="https://i.imgur.com/KTJxTmS.png" height="88%" width="80%" />
Navigate to the apps button in the top left corner, and click on the drop down to go to search and reporting. Start a new search by entering the asterisk or star key in the search bar and pressing enter. This is to display all the known events on the local system .
<img src="https://i.imgur.com/K3jpxDq.png" height="88%" width="80%" />
Next open up the Event Viewer in Windows, click on the Windows log drop-down, right click on security, and then select clear logs. We would like to clear the logs so that we can view the event of logs being cleared within Splunk.
<img src="https://i.imgur.com/eJZgufC.png" height="88%" width="80%" />Now to add more parameters to our search, you can hover your mouse over any part of a specific event and then click on the “Add to Search” . For this lab, we will be adding the host, source, and event code parameters to the search. This means we are specifying events only on this computer, from the windows event viewer security logs, With an event code of 1102. Notice the message on the event says the audit log was cleared. Now we have an event that shows an action we did ourselves in Splunk. Be sure to copy the search parameters from the search bar and paste it into a notepad, as we will need this specific query later on. Now click on the "Create Table View" button in the top right corner.
<img src="https://i.imgur.com/HI9qgSE.png" height="88%" width="80%" />
We now have a table created showing a simplified version of all the fields we have selected.You can use the left hand side to add or remove fields to this table. the raw field is added by default, but I removed it for simplicity. Once you are finished adding or removing fields, click on "Done" on the left hand side.
<img src="https://i.imgur.com/pNCHXuR.png" height="88%" width="80%" />
  Select the dashboard tab in the top left corner, then select create new dashboard. I have labeled my dashboard clear logs, but you can name it whatever you want. In this project we use dashboard Studio to create the dashboard after this I selected the grid option.
<img src="https://i.imgur.com/D0ok6OC.png" height="88%" width="80%" />
Select the add Charts button, which is the icon of two bars and it is directly under the analytics tab in the top left corner. From here, select table, and notice on the right hand side, once the table is selected there is a field titled “search with SPL” , this is where you want to paste the query we copied from earlier. Once you enter your search select "Apply and close"
<img src="https://i.imgur.com/bLp8oJx.png" height="88%" width="80%" />
I wanted to remove the bkt and CD fields and add a field for the error code and message. Using search processing language, I was able to complete that as you can see on the right hand side.  With a better understanding of search processing language, you too can customize your table to better fit your needs. 
