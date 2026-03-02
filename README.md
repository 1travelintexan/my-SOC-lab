# Cybersecurity Detection &amp; Monitoring Lab

Seasoned Web Developer (5+ years) transitioning into Cybersecurity, leveraging a deep understanding of application architecture to build and secure robust digital environments. This repository documents my hands-on SOC Home Lab, where I apply the principles earned through my Security+, Google Cybersecurity, and Splunk Core/Power User certifications. My goal is to bridge the gap between software development and threat hunting by simulating real-world attack vectors—such as SMB brute-forcing and persistence mechanisms—and engineering precise detection logic within Splunk to identify and mitigate them.

## 🛡️ Certifications

![Security+](https://img.shields.io/badge/Certification-Security%2B-blue)
![Splunk Power User](https://img.shields.io/badge/Splunk-Power%20User-green)
![Google Cyber](https://img.shields.io/badge/Google-Cybersecurity-orange)

<h1>Part 1: Hardware Specs for the Host PC:</h1>

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

<h1>Part 2: Configuring Kali Linux as the Attack Machine</h1>

![2023-07-02_16-27-14](https://github.com/gavinpaul-6/SOC-Lab/assets/98987388/2cc51854-b589-4963-8d7b-c0595bb1c33b)

Update hydra and its dependencies so it can use smb2 as the smb orginal was being blocked

![2023-07-02_23-29-20](https://github.com/1travelintexan/my-SOC-lab/blob/main/images/update-hydra.png?raw=true)

hydra on Kali linux Virtual Machine used to crack the login password of a known username.

![2023-07-02_23-29-20](https://github.com/1travelintexan/my-SOC-lab/blob/main/images/hydra.png?raw=true)

<h2>Command explained:</h2>

- '-l' is for the known username (ragnar)
- '-P' is for the path to the rockyou password list given by Kali
- 'smb2' is the new version of smb because the Windows 11 blocks the older version of smb
- '-V' is for the real time log of each attempt at cracking the password
- '-f' is for exiting as soon as a correct match is found
- '-t 1' is to not run in parallel, this makes each attempt serial instead

<h1>Part 3: Use Splunk to detect the brute force attack</h1>

The next step was to find the correct query on Splunk to find when and how the brute force attack was executed.

![2023-07-02_14-46-01](https://github.com/1travelintexan/my-SOC-lab/blob/main/images/splunk-failed-sql.png?raw=true)
![2023-07-02_14-46-01](https://github.com/1travelintexan/my-SOC-lab/blob/main/images/splunk-failed-chart.png?raw=true)

<h3>
Once I found the brute force attack and I knew the IP of the attacker, I can try to find if they sucessfully cracked the password of the user 'ragnar':
</h3>

![2023-08-04_10-56-23](https://github.com/1travelintexan/my-SOC-lab/blob/main/images/splunk_failed_and_success.png?raw=true)

<h2>Splunk Processing Language explained:</h2>

- index= "lab_attacks" => is the specfic index that I directed the attacks to
- (EventCode=4624 OR EventCode=4625) => is for failed or successful logon attempts
- eval is_ragnar=if(searchmatch("ragnar"), "YES", "NO") => is to create a new feild that establishes if the event has the string 'ragnar' in it.
- where is_ragnar="YES" => is to filter out all events that do not include the string 'ragnar'
- stats
  count(eval(EventCode=4625)) as Total_Fails,
  count(eval(EventCode=4624)) as Total_Successes
  by index' is to count all the failed attempts and all the successful attempts
- eval User="ragnar" => is to create a feild named User that is always 'ragnar'
- table User, Total_Fails, Total_Successes => creates a table with the three feilds that I need to see

<h2>The Result of detecting a brute force attack:</h2>

![2023-08-04_10-56-23](https://github.com/1travelintexan/my-SOC-lab/blob/main/images/splunk_count_graph.png?raw=true)

<h1>Part 4: Creating Persistence:</h1>

<p>Create a file on the Kali machine (backdoor.ps1) that will add a new user in administrators named (haxor)</p>

![2023-07-02_15-43-59](https://github.com/1travelintexan/my-SOC-lab/blob/main/images/create_backdoor.png?raw=true)

<p>Login to the Windows machine as Ragnar and use SMB to 'put' the backdoor file onto the Windows machine </p>

![2023-07-02_15-43-59](https://github.com/1travelintexan/my-SOC-lab/blob/main/images/create_backdoor_file.png?raw=true)

<p>Use impacket to execute the backdoor file as Ragnar from the Kali machine</p>

![2023-07-02_15-43-59](https://github.com/1travelintexan/my-SOC-lab/blob/main/images/execute_backdoor.png?raw=true)

<p>Use impacket to exfiltrate all the Users from the Windows machine with their password hashes </p>

![2023-07-02_15-43-59](https://github.com/1travelintexan/my-SOC-lab/blob/main/images/exfiltrate_hashes.png?raw=true)

<p>Use John the Ripper from Kali machine to crack the Users hashes in the loot_hashes file</p>

![2023-07-02_15-43-59](https://github.com/1travelintexan/my-SOC-lab/blob/main/images/cracking_hashes.png?raw=true)

<p>Show all the passwords cracked that were exfiltrated</p>

![2023-07-02_15-43-59](https://github.com/1travelintexan/my-SOC-lab/blob/main/images/show_cracked_hashes.png?raw=true)

<h1>Part 5: Detecting Persistence with Splunk</h1>

After successfully attacking the Windows machine with SMB and creating a new admin user named haxor, I need to find these logs in splunk to follow the attack path.

<h2>Part 6: Automating Users with PowerShell</h2>

### With Sysmon installed on the Windows machine, I look for an EventCode of "1" to see if something is created.

![2023-07-16_18-29-39](https://github.com/1travelintexan/my-SOC-lab/blob/main/images/commandline.png?raw=true)

### Found suspicous process that added a new user to the administrators group

![2023-07-16_18-29-39](https://github.com/1travelintexan/my-SOC-lab/blob/main/images/user_added.png?raw=true)

**The two resources I used to learn PowerShell:**

- [Learn Powershell in a Month of Lunches by Travis Plunk and James Petty](https://www.amazon.com/Learn-PowerShell-Month-Lunches-Windows/dp/1617296961/ref=sr_1_1?crid=2WCEOQMNNGTF3&keywords=learn+powershell+in+a+month+of+lunches&qid=1695219714&sprefix=learn+powershe%2Caps%2C239&sr=8-1)
- [Learn Powershell Scripting in a Month of Lunches by Dom Jones and Jeffery Hicks](https://www.amazon.com/sspa/click?ie=UTF8&spc=MTozNzg2OTk3MDYyODQ5NjI3OjE2OTUyMTk3MTU6c3Bfc2VhcmNoX3RoZW1hdGljOjIwMDE1NDM2MzU0OTQ5ODo6MDo6&url=%2FLearn-PowerShell-Scripting-Month-Lunches%2Fdp%2F1617295094%2Fref%3Dsxin_15_pa_sp_search_thematic_sspa%3Fcontent-id%3Damzn1.sym.021cacdc-698c-497f-aed0-8965849c4c44%253Aamzn1.sym.021cacdc-698c-497f-aed0-8965849c4c44%26crid%3D2WCEOQMNNGTF3%26cv_ct_cx%3Dlearn%2Bpowershell%2Bin%2Ba%2Bmonth%2Bof%2Blunches%26keywords%3Dlearn%2Bpowershell%2Bin%2Ba%2Bmonth%2Bof%2Blunches%26pd_rd_i%3D1617295094%26pd_rd_r%3Dce8f7f52-4263-4740-9f8b-5adffaf2ebd5%26pd_rd_w%3DGG1o2%26pd_rd_wg%3DsUGtF%26pf_rd_p%3D021cacdc-698c-497f-aed0-8965849c4c44%26pf_rd_r%3DA64X22YVY2FC940W14BN%26qid%3D1695219714%26sbo%3DRZvfv%252F%252FHxDF%252BO5021pAnSA%253D%253D%26sprefix%3Dlearn%2Bpowershe%252Caps%252C239%26sr%3D1-1-6caf8c80-d701-4184-90ff-f670949d61c2-spons%26sp_csd%3Dd2lkZ2V0TmFtZT1zcF9zZWFyY2hfdGhlbWF0aWM%26psc%3D1)
