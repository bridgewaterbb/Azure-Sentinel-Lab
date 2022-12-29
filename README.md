<p align="center">
<img src="https://i.imgur.com/q9bCI13.png" alt="Microsoft Azure Sentinel"/>
</p>

<h1>Threat Mapping with Azure Sentinel</h1>
In this lab I created an Windows machine to act as a Honeypot in Microsoft Azure and used failed RDP logins to map attacks.<br />


<h2>Technology Utilized</h2>

- Microsoft Azure (Virtual Machine/Cloud)
- Microsoft Azure Sentinel (SIEM)
- Remote Desktop
- IPgeolocation (API)
- PowerShell
- Kusto Query Language (KQL)

<h2>Operating Systems Used </h2>

- Windows 10 (21H2)

<h2>Overview</h2>

- Create a Windows 10 Pro virtual machine and Resource Group
- Create a Log Analytics Workspace (LAW) in Resource Group
- Activate Azure Sentinel 
- Connect LAW to Sentinel
- Configure Windows machine as a Honeypot
- Use IPgeolcation API and PowerShell script to gather location data of attackers
- Configure logs
- Create custom fields for logs
- Create Sentinel Workbook to map data
- Gather data and observe on map

<h2>Configuration</h2>

<p>
I created a Windows 10 Pro machine in Microsoft Azure. When creating this machine I removed the default network security rules. I added a network security rule that allowed all traffic in, on every port, from anywhere. I then created a Log Analytics Workspace (LAW) within the same Resource Group as the machine. Once provisioned, the LAW was connected to the Windows virtual machine. Defender was then activated and set to collect data for all events. Azure Sentinel was provisioned and connected to the LAW
</p>
<br />

<p>
<img src="https://i.imgur.com/kZZGbwz.png" height="80%" width="80%" alt="script"/>
</p>

<p>
I connected to the Windows machine with RDP (Remote Desktop Protocol). I then turned the Firewall for this machine completely off. I then ran a PowerShell script (Thanks to Josh Madakor for the script) on the Windows machine. This script sends Windows Event Log information (Event ID 4625) for failed RDP attacks to a third party API (IPgeolocation). This API collects geolocation data from the attackers. The script creates a text log file in the Program Data folder. The script also populates the log with training data. I modified the script to include my own API key for the IPgeolocation app. 
</p>
<br />

<p>
<img src="https://i.imgur.com/jsDHZd2.png" height="80%" width="80%" alt="Fields"/>
</p>
<p>
To my surprise, as soon as I ran the script I saw entries populating from Netherlands. I did not expect the machine to already be under attack in such a short period of time. I then created a custom log in the LAW and imported the training and attack data from the log created on the Windows machine. I then created custom fields by extracting them from the file data. The fields I extracted were: latitude, longitude, destinationhost, username, sourcehost, state, country, label (country-ipaddress), timestamp. This would ensure any subsequent attack data would be parsed and displayed in the Azure log with these fields. 
</p>
<br />

<h2>Mapping the Attacks</h2>

<p>
<img src="https://i.imgur.com/Ufoy4dp.png" height="80%" width="80%" alt="Query"/>
</p>
<p>
I created a Workbook in Sentinel. I then configured the Workbook to plot the data collected by the LAW log onto a map. 
</p>
<br />


<p>
<img src="https://i.imgur.com/f6lqohk.png" height="50%" width="50%" alt="Map Init"/>
</p>
<p>
I then waited for around 72 hours for the Internet to attack my machine. This was the result.
</p>
<br />

<p>
<img src="https://i.imgur.com/h4Cdgpb.png" height="100%" width="100%" alt="Attack Map"/>
</p>

<p>
And this is after a little over one week.
</p>
<br />

<p>
<img src="https://i.imgur.com/IQv03dZ.png" height="100%" width="100%" alt="Attack Map"/>
</p>

<p>
<img src="https://i.imgur.com/3tizwQI.png" height="80%" width="80%" alt="API pulls"/>
</p>
