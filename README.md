<p align="center">
<img src="https://i.imgur.com/Ua7udoS.png" alt="Traffic Examination"/>
</p>

<h1>Network Security Groups (NSGs) and Inspecting Traffic Between Azure Virtual Machines</h1>
In this tutorial, we observe various network traffic to and from Azure Virtual Machines with Wireshark as well as experiment with Network Security Groups. <br />

<h2>Environments and Technologies Used</h2>

- Windows 10 and Ubuntu 20.04 LTS
- Remote Desktop Protocol (RDP)
- Wireshark
- Command Prompt / PowerShell
- SSH

<h2>Operating Systems Used</h2>

- Windows 10 Pro (22H2)
- Ubuntu Linux 20.04 LTS

<h2>Part 1 – Deploy Virtual Machines in Azure</h2> <h3>1. Create Azure Resources</h3>

- Sign in to the Azure Portal: https://portal.azure.com
- Create a new Resource Group named: NetworkLab-RG

Create a Windows 10 VM:

- Assign it to NetworkLab-RG
- Allow it to create a new Virtual Network and Subnet

Create a Linux VM (Ubuntu):

- Assign it to the same NetworkLab-RG
- Select the same Virtual Network and Subnet
- Use Username/Password for authentication

<img src="https://i.imgur.com/XOBm0X9.png" width="80%" /> <p> Creating two VMs on the same virtual network allows direct internal communication between them using private IP addresses. This is essential for observing peer-to-peer traffic and testing firewall behaviors in later steps. </p> <br />
<h2>Part 2 – Observe Network Traffic Using Wireshark</h2>

<h3>2. Connect to Windows VM</h3>

- Use Microsoft Remote Desktop (or RDP on Windows) to connect to your Windows 10 VM
- Log in using the credentials you created

<p> Remote Desktop Protocol (RDP) provides access to the virtual machine GUI, allowing you to install applications and monitor network activity. </p> <br /> 

<h3>3. Install and Launch Wireshark</h3>

- On the Windows VM, download and install Wireshark
- Open Wireshark and begin a packet capture on your main network interface

<img src="https://i.imgur.com/bD2USwo.png" width="80%" /> <p> Wireshark is a powerful tool for real-time packet analysis. It lets us filter, inspect, and capture network traffic based on protocols like ICMP, SSH, DHCP, etc. </p> <br /> 

<h3>4. Observe ICMP Traffic</h3>

- Retrieve the private IP address of your Ubuntu VM via the Azure Portal
- In the Windows VM, open Command Prompt and run: ping <Ubuntu IP>
- In Wireshark, filter for icmp

<img src="https://i.imgur.com/SIS58Ra.png" width="80%" /> <p> ICMP traffic is used for operations like pinging. This step helps visualize the echo request/reply behavior between the two VMs. </p> <br /> <h3>5. Ping External Website</h3>
From the Windows VM, ping an external address: ping www.google.com

Observe both internal and external ICMP traffic in Wireshark

<p> This shows the difference between local (private network) and public (internet) ICMP activity. </p> <br />
<h2>Part 3 – Configure Network Security Group and Observe More Protocols</h2> <h3>6. Block ICMP with NSG</h3>
Start a continuous ping to the Ubuntu VM: ping -t <Ubuntu IP>

In Azure, navigate to the Network Security Group (NSG) for the Ubuntu VM

Add an inbound rule to deny ICMP

Back in the Windows VM, observe packet loss in Command Prompt and Wireshark

<p> Blocking ICMP in the NSG demonstrates Azure’s built-in firewall controls and how they impact network traffic in real time. </p> <br /> <h3>7. Re-enable ICMP</h3>
Delete or disable the ICMP blocking rule

Observe the return of successful ping responses and ICMP packets in Wireshark

<p> This reinforces how changes in NSG rules can instantly impact traffic behavior without rebooting the VM. </p> <br /> <h3>8. Observe SSH Traffic</h3>
In Wireshark, filter for ssh

From Windows PowerShell, SSH into the Ubuntu VM:
ssh labuser@<Ubuntu IP>

Enter your credentials, type commands, then type exit to disconnect

<p> SSH traffic includes encrypted commands and responses. Even though it's encrypted, Wireshark can still show the metadata and packet exchanges. </p> <br /> <h3>9. Observe DHCP Traffic</h3>
In Wireshark, filter for dhcp

In PowerShell (Admin), run: ipconfig /renew

<p> DHCP is responsible for assigning IP addresses. Renewing the IP address triggers a DHCP Discover/Offer/Request/Ack sequence, visible in Wireshark. </p> <br /> <h3>10. Observe DNS Traffic</h3>
Filter in Wireshark: dns

In PowerShell, run:
nslookup google.com
nslookup disney.com

<p> DNS converts domain names to IP addresses. These queries and their responses are clearly visible in packet capture. </p> <br /> <h3>11. Observe RDP Traffic</h3>
Filter in Wireshark: tcp.port == 3389

<p> RDP traffic is constant because it maintains an active visual session. Unlike protocols like SSH, RDP sends continuous screen updates, making it easy to identify. </p> <br />
<h2>Lab Cleanup</h2>
Close your Remote Desktop connection

Delete the Resource Group NetworkLab-RG in the Azure Portal

Verify that all associated resources (VMs, disks, NICs, etc.) are removed

<p> Proper cleanup is essential to avoid ongoing charges and to practice good cloud resource hygiene. </p> <br />
