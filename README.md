<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Set up DC-1 & Client-1 Virtual Machines
- Set DC-1`s Private IP to static
- Enable ICMPv4 on DC-1
- Install Active Directory Domain Service on DC-1
- Set up our Domain in Active Directory on DC-1
- Setup Organizational Units
- Setting up an Admin user
- Set Client-1`s DNS to DC-1
- Add Host Domain to Client-1
- Set up so all employees can login

<h2>Deployment and Configuration Steps</h2>
<h3>Setting up the VM`s</h3>
<p>
We need to set up 2 Virtual Machines, To do this we need to first create a Windows Server 2022 and call it DC-1 and make sure to pick the vm with 2vcpus.</p>
 Then we need to make the Client-1 Virtual Machine with Windows 10 on it. Make sure to have the Client-1 in the same Resource group as DC-1 in the setup.
</p>
<p>
<img src="https://i.imgur.com/ojLPrbX.png" height="40%" width="40%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/8SUGIav.png" height="40%" width="40%" alt="Disk Sanitization Steps"/>
</p>
<h4>Setting DC-1`s Private IP to static</h4>
<p>Go in the Azure portal to DC-1 -> Networking -> Network Interface (click the blue text next to it) -> Ip-Configuration -> on the bottom click on "ipconfig1" and select "Static" and save</p>
<img src="https://i.imgur.com/mC7oeom.png" height="40%" width="40%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/7OhaoU8.png" height="40%" width="40%" alt="Disk Sanitization Steps"/>
<br />

<h3>Enable ICMPv4</h3>
<p>
Now inside of DC-1 open up Windows Defender Firewall with Advanced Security-> Inbound Rules and enable the 2 "Core Networking Diagnostics - ICMP Echo Request (ICMPv4-in). Client-1 can now ping DC-1`s Private IP Address." 
</p>
<p>
<img src="https://i.imgur.com/Gub82yH.png" height="60%" width="60%" alt="Disk Sanitization Steps"/>
</p>
<br />

<h2>Install Active Directory Domain Service on DC1</h2>
<p>
To install Active Directory open "Server Manager" -> Add roles and features -> Next -> Next -> Enable Active Directory Domain Service (press "Add Features" in the pop up) -> Next -> Next -> Install. Once its done installing press "Close"
</p>
<p>
<img src="https://i.imgur.com/eW90Zle.png" height="40%" width="40%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/dR1TEpC.png" height="40%" width="40%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/LP3LL7s.png" height="40%" width="40%" alt="Disk Sanitization Steps"/>
</p>
<br />

<h3>Setting up our new Domain</h3>
<p>
 Now there is a Yellow triangle sign in the top right corner with the flag, Click on it and click on "Promote this server to a domain controller" 
</p>
<p>
<img src="https://i.imgur.com/9I8klRo.png" height="60%" width="60%" alt="Disk Sanitization Steps"/>
<p> Select "Add a new forest" and type your new Root domain name</p>
  <img src="https://i.imgur.com/vMhbkrB.png" height="60%" width="60%" alt="Disk Sanitization Steps"/>
  <p>Then Next -> Add a password -> Next -> Next -> Next -> Next -> Install</p>
</p>
<br />

<h3>Setup Organizational Unit</h3>
<p>
 After the VM has restarted Active Directory is now up and running and we need to create an Organizational Unit for all the Employees we have. Inside Server Manager go to Tools in the top right and in the drop down go to "Active Directory Users and Computers. Right click on "Mydomain.com" and select "New" then "Organizational Unit" and name it "_EMPLOYEES" and one called "_ADMINS". Then right click on "Mydomain.com" again and click on "Refresh"
</p>
<p>
<img src="https://i.imgur.com/vJ2Zp4k.png" height="60%" width="60%" alt="Disk Sanitization Steps"/>
</p>
<p>you should now have 2 new folders inside of your Active Directory</p>
<img src="https://i.imgur.com/5rNMHau.png" height="60%" width="60%" alt="Disk Sanitization Steps"/>
<br />

<h2>Setting up an Admin user</h2>
<p>
Inside "_ADMINS" Right click inside the folder and select "new" and "user" and type in the information needed, then click next. Now we need to set up a password for our user and select the appropriate settings for password. Then clikc next and finish.
</p>
<p>
<img src="https://i.imgur.com/anOWRdu.png" height="40%" width="40%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/hFLtpau.png" height="30%" width="30%" alt="Disk Sanitization Steps"/>
  <p>Now to make the user an actual admin we need to add it to the "Domain Admin" security group. To do this right click on the user -> Properties -> Member of -> Add -> Type "Domain Admin" in the text box and click "Check Name" -> OK -> Apply -> OK. Now you can log out of DC-1 and login with your new admin account.</p>
<img src="https://i.imgur.com/lM0dNyn.png" height="40%" width="40%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>
In Command Prompt we can type "Whoami" to check what user you are signed in to. As you can see below i am now signed into my Admin user (a.ms)
</p>
<p>
<img src="https://i.imgur.com/ngT0Y0l.png" height="60%" width="60%" alt="Disk Sanitization Steps"/>
</p>
<br />

<h3>Setting up Client-1`s DNS</h3>
<p>
Now to be able to use Active Directory users to login to Client-1 we need to connect it to the DC-1 server with DNS. To do this go to Azure portal and find Client-1 -> Networking -> Network Interface -> DNS Servers -> Change DNS Servers from "Inherit from virtual network" to "custom" and type in DC-1`s Private IP address. Once you have typed it in press save. Then restart Client-1 from Azure Portal.
</p>
<p>
<img src="https://i.imgur.com/4VhiFyj.png" height="60%" width="60%" alt="Disk Sanitization Steps"/>
</p>
<h3>Adding Our Host Domain</h3>
<p>We can now add the Host Domain to Client-1, to do this right click start button -> System -> Rename this PC (advanced) -> Change -> Change Member of from Workgroup to Domain and type your domain "mydomain.com" then OK.  </p>
<img src="https://i.imgur.com/7dLG9kF.png" height="40%" width="40%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/uDxB5cH.png" height="40%" width="40%" alt="Disk Sanitization Steps"/>
<br />

<h3>Login Permission</h3>
<p>
In Client-1`s computer right click the start button and go to systems -> Remote Desktop -> Select users that can remotly access this PC -> Add -> Add inn "Domain Users" in the text box and click check names,
</p>
<p>
<img src="https://i.imgur.com/yCLNWde.png" height="60%" width="60%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>you can now login to Client-1 with Active Directory made users.</p>

<h4>Now we can see Client-1`s Computer in Active Directory from DC-1 and its connected up.</h4>
<img src="https://i.imgur.com/2Ye0CvU.png" height="40%" width="40%" alt="Disk Sanitization Steps"/>
