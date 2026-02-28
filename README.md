# Cybersecurity Detection &amp; Monitoring Lab

Seasoned Web Developer (5+ years) transitioning into Cybersecurity, leveraging a deep understanding of application architecture to build and secure robust digital environments. This repository documents my hands-on SOC Home Lab, where I apply the principles earned through my Security+, Google Cybersecurity, and Splunk Core/Power User certifications. My goal is to bridge the gap between software development and threat hunting by simulating real-world attack vectors—such as SMB brute-forcing and persistence mechanisms—and engineering precise detection logic within Splunk to identify and mitigate them.

## 🛡️ Certifications

![Security+](https://img.shields.io/badge/Certification-Security%2B-blue)
![Splunk Power User](https://img.shields.io/badge/Splunk-Power%20User-green)
![Google Cyber](https://img.shields.io/badge/Google-Cybersecurity-orange)

<h2>Hardware Specs for the Host PC:</h2>

- CPU: AMD Ryzen 7 5700X3d (8 Core Procesor)
- RAM: 64GB
- Storage: 2TB SSD
- Graphics Card: AMD Radeon RX 6600
- Motherboard: TUF Gaming B550-Plus WIFI II
- Host Operating System: Microsoft Windows 11 Pro

<p align="center">
<img src="https://github.com/1travelintexan/my-SOC-lab/blob/main/images/pc.jpg?raw=true" width="400" height="800" style="object-fit: contain" />
<h1>Virtual Machine Setup:</h1>
<h3>I chose to use virtual box to create my internal Windows machine and the Kali Linux machine. With a bridged adaptor connection opposed to NAT connection</h3>
</p>
<h2>Part 1: Configuring Splunk</h2>
<div>
<section style=" height: 400px">
<p>Setting the inputs.conf file </p>
<img src="https://github.com/1travelintexan/my-SOC-lab/blob/main/images/splunk_config.png?raw=true"  />
</section>
<section style=" height: 550px">
<p>Allow the VM Windows machine firewalls to accept brute force requests</p>
<img src="https://github.com/1travelintexan/my-SOC-lab/blob/main/images/firewalls.png?raw=true" style="object-fit: contain; margin: 0px" />
</section>
<section style="height: 400px">
<p>Set IP address of the VM Windows machine to an easy number</p>
<img src="https://github.com/1travelintexan/my-SOC-lab/blob/main/images/IP_address.png?raw=true"  />
</section>
</div>

<h2>Part 2: Configuring Kali Linux as the Attack Machine</h2>

![2023-07-02_16-27-14](https://github.com/gavinpaul-6/SOC-Lab/assets/98987388/2cc51854-b589-4963-8d7b-c0595bb1c33b)

hydra on Kali linux Virtual Machine used to crack the login password of a known username.

![2023-07-02_23-29-20](https://github.com/1travelintexan/my-SOC-lab/blob/main/images/hydra.png?raw=true)

<h2>Command explained:</h2>

- '-l' is for the known username (ragnar)
- '-P' is for the path to the rockyou password list given by Kali
- 'smb2' is the new version of smb because the Windows 11 blocks the older version of smb
- '-V' is for the real time log of each attempt at cracking the password
- '-f' is for exiting as soon as a correct match is found
- '-t 1' is to not run in parallel, this makes each attempt serial instead

<h2>Part 3: Configuring SecurityOnion and SOC Analyst Machine</h2>

The next step was to install SecurityOnion. SecurityOnion is an open-source platform designed for network security monitoring and intrusion detection. It helps organizations analyze and defend their network infrastructure against potential threats and attacks.

I installed the Security Onion ISO from here: https://github.com/Security-Onion-Solutions/securityonion/blob/master/VERIFY_ISO.md

![2023-07-02_14-46-01](https://github.com/gavinpaul-6/SOC-Lab/assets/98987388/efb885b3-9625-456e-8bff-a544ae79d591)

I configured SecurityOnion as shown in the screenshot:

![2023-08-04_10-56-23](https://github.com/gavinpaul-6/SOC-Lab/assets/98987388/d62b059f-27a6-44a1-a3b8-8aedf6443807)

After finishing the Security Onion installation, I needed a web interface to connect to Security Onion using the Management IP given.

![2023-07-02_15-43-59](https://github.com/gavinpaul-6/SOC-Lab/assets/98987388/239d6c7e-a948-4a7a-ab2b-ebcecc7e18e2)

I downloaded and installed Ubuntu Desktop as my Security Analyst machine to simulate a SOC/Security Analyst accessing a SIEM. In this scenario to access Security Onion's web interface.

![2023-07-02_16-11-19](https://github.com/gavinpaul-6/SOC-Lab/assets/98987388/3ad77626-8685-4d84-b78d-99fe4105cb5d)

![2023-07-03_10-18-40](https://github.com/gavinpaul-6/SOC-Lab/assets/98987388/5d590acc-acec-4cbf-95ca-1905f2dc37f9)

![2023-07-03_10-17-27](https://github.com/gavinpaul-6/SOC-Lab/assets/98987388/9a672f3c-1838-4029-8270-5295a5e1247c)

<h2>Part 4: Configuring Windows Server 2019 as a Domain Controller</h2>

In this lab segment, I established an Active Directory domain using Windows Server 2019 as the Domain Controller, alongside two Windows 10 Pro virtual machines. Following this initial setup, I proceeded to rename the Domain Controller (GAVINPAUL-DC), initiated a restart, and then installed Active Directory Domain Services while configuring Active Directory Certificate Services through the Server Manager dashboard on the designated server.

<h2>Part 5: Automating Users with PowerShell</h2>

In this lab section, I aimed to simulate the Active Directory environment of a small to medium-sized business, opting for a more realistic approach by creating 1000 users, even though it wasn't a requirement. To accomplish this, I employed a Powershell script from Josh Madakor's AD Lab on YouTube and drew upon my investment in two Powershell books from the previous year to enhance my knowledge in the field.

![2023-07-16_18-29-39](https://github.com/gavinpaul-6/SOC-Lab/assets/98987388/dc8252d2-8177-47c4-a68e-ba97cf1e5e17)

![2023-07-16_18-37-43](https://github.com/gavinpaul-6/SOC-Lab/assets/98987388/af26d541-6eb6-49e5-a08b-a2bcd349caf0)

**The two resources I used to learn PowerShell:**

- [Learn Powershell in a Month of Lunches by Travis Plunk and James Petty](https://www.amazon.com/Learn-PowerShell-Month-Lunches-Windows/dp/1617296961/ref=sr_1_1?crid=2WCEOQMNNGTF3&keywords=learn+powershell+in+a+month+of+lunches&qid=1695219714&sprefix=learn+powershe%2Caps%2C239&sr=8-1)
- [Learn Powershell Scripting in a Month of Lunches by Dom Jones and Jeffery Hicks](https://www.amazon.com/sspa/click?ie=UTF8&spc=MTozNzg2OTk3MDYyODQ5NjI3OjE2OTUyMTk3MTU6c3Bfc2VhcmNoX3RoZW1hdGljOjIwMDE1NDM2MzU0OTQ5ODo6MDo6&url=%2FLearn-PowerShell-Scripting-Month-Lunches%2Fdp%2F1617295094%2Fref%3Dsxin_15_pa_sp_search_thematic_sspa%3Fcontent-id%3Damzn1.sym.021cacdc-698c-497f-aed0-8965849c4c44%253Aamzn1.sym.021cacdc-698c-497f-aed0-8965849c4c44%26crid%3D2WCEOQMNNGTF3%26cv_ct_cx%3Dlearn%2Bpowershell%2Bin%2Ba%2Bmonth%2Bof%2Blunches%26keywords%3Dlearn%2Bpowershell%2Bin%2Ba%2Bmonth%2Bof%2Blunches%26pd_rd_i%3D1617295094%26pd_rd_r%3Dce8f7f52-4263-4740-9f8b-5adffaf2ebd5%26pd_rd_w%3DGG1o2%26pd_rd_wg%3DsUGtF%26pf_rd_p%3D021cacdc-698c-497f-aed0-8965849c4c44%26pf_rd_r%3DA64X22YVY2FC940W14BN%26qid%3D1695219714%26sbo%3DRZvfv%252F%252FHxDF%252BO5021pAnSA%253D%253D%26sprefix%3Dlearn%2Bpowershe%252Caps%252C239%26sr%3D1-1-6caf8c80-d701-4184-90ff-f670949d61c2-spons%26sp_csd%3Dd2lkZ2V0TmFtZT1zcF9zZWFyY2hfdGhlbWF0aWM%26psc%3D1)

<h2>Part 6: Setting up Windows 10 PC and Adding to AD Domain </h2>

After generating the users, I added the two Windows 10 VMs to the Victim network **(VMnet3)**.

I navigated to Network Adapter settings, right clicked on Ethernet, selected Properties.

- Selected IPv4 and added IP Address <b>192.168.2.1</b> as the default gateway and <b>192.168.2.10 (Victim Network)</b> as the DNS Server.

Search "domain" in the search bar and select Access work or school option.

Selected 'Connect' > 'Join this device to local Active Directory Domain

Enter domain name.local (GAVINPAUL.local for me)
![2023-07-03_01-07-55](https://github.com/gavinpaul-6/SOC-Lab/assets/98987388/d5ffa57b-9fc5-40e5-a70e-995551d06fa5)

![2023-07-03_01-08-20](https://github.com/gavinpaul-6/SOC-Lab/assets/98987388/bee07866-5f59-4c8d-82a1-e771fdb71a2e)

<h2>Part 7: Installing Splunk on Ubuntu Server </h2>

The next step was setting up my Splunk instance. This was pretty straightforward. I installed Ubuntu Server from Ubuntu.com and followed the steps. To access the GUI, I needed to install tasksel and reboot.

Once I was able to access the GUI and get past the initial setup, I navigated to Firefox to install Splunk from their website.

![2023-07-03_01-31-52](https://github.com/gavinpaul-6/SOC-Lab/assets/98987388/8305afe5-0fa5-4c3b-bb57-e13141c8740d)

![2023-07-03_01-52-12](https://github.com/gavinpaul-6/SOC-Lab/assets/98987388/0065797d-4d89-4a61-a7bd-ac977a63079b)

![2023-07-03_02-08-25](https://github.com/gavinpaul-6/SOC-Lab/assets/98987388/3393b2e2-01a6-48e8-9dca-f54b34a5c096)

After installing Splunk to the Downloads folder, I unzipped it using the tar command using the following command:

<b>`tar -xvzf splunk`</b>

I navigated to bin directory and accepted the terms and created admin credentials to log into Splunk.

![2023-07-03_02-11-39](https://github.com/gavinpaul-6/SOC-Lab/assets/98987388/1aa052fb-b9f6-4c3f-a314-a410cc25a1e6)

Once Splunk is up in running, I navigated to web server URL using Firefox and logged into Splunk.

![2023-07-03_02-14-46](https://github.com/gavinpaul-6/SOC-Lab/assets/98987388/21c92a8d-d2ab-4e1d-b858-e9ac41a9447e)

![2023-07-03_02-21-09](https://github.com/gavinpaul-6/SOC-Lab/assets/98987388/2651ed82-621a-413b-8fc9-bf8945ec4675)

<h2>Part 8: Installing Universal Forwarder on Windows Server </h2>

With Splunk successfully installed, the subsequent task involved installing the Universal Forwarder to capture and transmit activity logs from my Domain Controller to the Splunk SIEM.

I navigated back to Windows Server VM and installed Google Chrome before downloading the Splunk Universal Forwarder.

![2023-07-03_02-37-34](https://github.com/gavinpaul-6/SOC-Lab/assets/98987388/184e0109-1312-44ca-a176-4c4d2a48fa3d)

![2023-07-03_02-40-16](https://github.com/gavinpaul-6/SOC-Lab/assets/98987388/d72335b3-fe9d-4ad9-98f3-d90815b8256b)

- Once downloaded, I logged back into my Splunk machine and logged into Splunk and navigated to Settings > Forward and Receiving > Configure receiving.

- Clicked the top right green 'New Receiving Port' button.

- Enter 9997 and then click save so Splunk will listen for inbound connections from the Universal Forwarder on port 9997.

<i>(Port 9997 is the default port for Splunk's data forwarding protocol. Universal Forwarders, which are lightweight Splunk components installed on source machines, use this port to send data (logs and other events) to the Splunk Indexer. It facilitates the secure and efficient transmission of data from source to destination.)</i>

The subsequent task involved establishing an index dedicated to Windows Event Logs within Splunk. In Splunk's context, an index serves as a storage repository for its data. Initially, data that hasn't yet been integrated into Splunk is considered raw data. Once ingested into Splunk, it undergoes indexing, which essentially means that Splunk processes and organizes the data, resulting in the formation of event data. These event data units are referred to as individual events.

![2023-07-03_02-21-09](https://github.com/gavinpaul-6/SOC-Lab/assets/98987388/2651ed82-621a-413b-8fc9-bf8945ec4675)
