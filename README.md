# Cybersecurity Detection &amp; Monitoring Lab

Seasoned Web Developer (5+ years) transitioning into Cybersecurity, leveraging a deep understanding of application architecture to build and secure robust digital environments. This repository documents my hands-on SOC Home Lab, where I apply the principles earned through my Security+, Google Cybersecurity, and Splunk Core/Power User certifications. My goal is to bridge the gap between software development and threat hunting by simulating real-world attack vectors—such as SMB brute-forcing and persistence mechanisms—and engineering precise detection logic within Splunk to identify and mitigate them.

## 🛡️ Certifications

![Security+](https://img.shields.io/badge/Certification-Security%2B-blue)
![Splunk Power User](https://img.shields.io/badge/Splunk-Power%20User-green)
![Google Cyber](https://img.shields.io/badge/Google-Cybersecurity-orange)

# Part 1: Hardware Specs for the Host PC:

- CPU: AMD Ryzen 7 5700X3d (8 Core Procesor)
- RAM: 64GB
- Storage: 2TB SSD
- Graphics Card: AMD Radeon RX 6600
- Motherboard: TUF Gaming B550-Plus WIFI II
- Host Operating System: Microsoft Windows 11 Pro

<p align="center">
<img src="https://github.com/1travelintexan/my-SOC-lab/blob/main/images/pc.jpg?raw=true" width="400" height="800" style="object-fit: contain" />
<h1>Virtual Machine Setup:</h1>
<h3>I chose to use virtual box to create my internal Windows machine and the Kali Linux machine. With a bridged adaptor connection opposed to NAT connection to allow them to communicate with each other.</h3>
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

# Part 2: Configuring Kali Linux as the Attack Machine

![2023-07-02_16-27-14](https://github.com/gavinpaul-6/SOC-Lab/assets/98987388/2cc51854-b589-4963-8d7b-c0595bb1c33b)

### Update hydra and its dependencies so it can use smb2 as the smb orginal was being blocked

![2023-07-02_23-29-20](https://github.com/1travelintexan/my-SOC-lab/blob/main/images/update-hydra.png?raw=true)

### hydra on Kali linux Virtual Machine used to crack the login password of a known username.

![2023-07-02_23-29-20](https://github.com/1travelintexan/my-SOC-lab/blob/main/images/hydra.png?raw=true)

## Command explained:

- '-l' is for the known username (ragnar)
- '-P' is for the path to the rockyou password list given by Kali
- 'smb2' is the new version of smb because the Windows 11 blocks the older version of smb
- '-V' is for the real time log of each attempt at cracking the password
- '-f' is for exiting as soon as a correct match is found
- '-t 1' is to not run in parallel, this makes each attempt serial instead

# Part 3: Use Splunk to detect the brute force attack

### The next step was to find the correct query on Splunk to find when and how the brute force attack was executed.

![2023-07-02_14-46-01](https://github.com/1travelintexan/my-SOC-lab/blob/main/images/splunk-failed-sql.png?raw=true)
![2023-07-02_14-46-01](https://github.com/1travelintexan/my-SOC-lab/blob/main/images/splunk-failed-chart.png?raw=true)

### Once I found the brute force attack and I knew the IP of the attacker, I can try to find if they sucessfully cracked the password of the user 'ragnar':

![2023-08-04_10-56-23](https://github.com/1travelintexan/my-SOC-lab/blob/main/images/splunk_failed_and_success.png?raw=true)

## Splunk Processing Language explained:

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

## The Result of detecting a brute force attack:

![2023-08-04_10-56-23](https://github.com/1travelintexan/my-SOC-lab/blob/main/images/splunk_count_graph.png?raw=true)

# Part 4: Creating Persistence:

### Create a file on the Kali machine (backdoor.ps1) that will add a new user in administrators named (haxor)

![2023-07-02_15-43-59](https://github.com/1travelintexan/my-SOC-lab/blob/main/images/create_backdoor.png?raw=true)

### Login to the Windows machine as Ragnar and use SMB to 'put' the backdoor file onto the Windows machine

![2023-07-02_15-43-59](https://github.com/1travelintexan/my-SOC-lab/blob/main/images/create_backdoor_file.png?raw=true)

### Use impacket to execute the backdoor file as Ragnar from the Kali machine

![2023-07-02_15-43-59](https://github.com/1travelintexan/my-SOC-lab/blob/main/images/execute_backdoor.png?raw=true)

### Use impacket to exfiltrate all the Users from the Windows machine with their password hashes

![2023-07-02_15-43-59](https://github.com/1travelintexan/my-SOC-lab/blob/main/images/exfiltrate_hashes.png?raw=true)

### Use John the Ripper from Kali machine to crack the Users hashes in the loot_hashes file

![2023-07-02_15-43-59](https://github.com/1travelintexan/my-SOC-lab/blob/main/images/cracking_hashes.png?raw=true)

### Show all the passwords cracked that were exfiltrated

![2023-07-02_15-43-59](https://github.com/1travelintexan/my-SOC-lab/blob/main/images/show_cracked_hashes.png?raw=true)

# Part 5: Detecting Persistence with Splunk

### With Sysmon installed on the Windows machine, I look for an EventCode of "1" to see if something is created.

![2023-07-16_18-29-39](https://github.com/1travelintexan/my-SOC-lab/blob/main/images/spl_EventCode_1.png?raw=true)

### Found suspicous process that added a new user to the administrators group

![2023-07-16_18-29-39](https://github.com/1travelintexan/my-SOC-lab/blob/main/images/user_added.png?raw=true)

### Now I can search for him and see when he is logged in and I have successfully found the backdoor.
