# Exploring Ubuntu Home Lab 

### 1. Identify Network Interfaces and IP Addresses

    Command: ip a or ifconfig

**ip a**: displays detailed networking information
- ip address show
- modern tool, actively maintained

**ifconfig**: displays basic networking information
- legacy tool
- only shows the basics

![Identifying Network Interfaces and IP Addresses](<Identifying Network Interfaces and IP Addresses.png>)

---

### 2. Check Open Ports

    Command: sudo netstat -tuln or ss -tuln
        
**ss -tuln**: displays more detailed information about ports and their states
- modern tool, actively maintained iproute2
- faster, and more efficent

**sudo netstat -tuln**: displays open tcp/udp ports and their states
- slower in comparison to ss -tuln

**Shared flags (`-tuln`):**
- `-t`: only shows TCP
- `-u`: only shows UDP  
- `-l`: shows ports in listening
- `-n`: states without resolving names   

![Checking Open Ports](<Checking Open Ports.png>)
    
---

### 3. Analyze Network Connections

Command: **sudo lsof -i -P -n**

**sudo lsof -i -P -n**: lists all open files and network connections  
- shows which processes are using which ports  
- maps ports to specific applications 
- `-i`: lists all network files and associated processes
- `-P` and `-n` avoids converting port numbers to port names

![Analyze Network Connections](<Analyze Network Connections.png>)

---

### 4. Perform Network Scanning with Nmap

Command: **sudo nmap -sS -O localhost**

**sudo nmap -sS -O localhost**: scans the server for open ports
- `-sS`: stealth TCP SYN scan 
- `-O`: determines the operating system of the target  

![Perform Network Scanning with NMap](<Perform Network Scanning with Nmap.png>)

---

### 5. Check for Open Ports on the Server's Network

Command: **sudo nmap -sP 192.168.1.0/24**

**sudo nmap -sP 192.168.1.0/24**: scans the local subnet to see which hosts are live  
- `-sP`: ping scan (checks which devices respond)  
- does not probe ports, only finds active hosts  
- useful for network discovery and inventory  

![Checking for Open Ports on Network pt1](<Checking for Open Ports pt1.png>)
![Checking for Open Ports on Network pt2](<Checking for Open Ports pt2.png>)

---

### 6. Check for Services and Versions

Command: **sudo nmap -sV localhost**

**sudo nmap -sV localhost**: scans open ports and identifies service versions  
- `-sV`: attempts to determine software version (e.g., Apache 2.4.54)  
- useful for vulnerability assessments  
- provides more detail than a basic port scan  

![Check for Services and Versions](<Check for Services and Versions.png>)

---

### 7. Identify Potential Vulnerabilities

Command: **sudo nmap --script vuln localhost**

**sudo nmap --script vuln localhost**: uses Nmap’s vulnerability detection scripts to identify known vulnerabilities on a server
- checks for known exploits or misconfigurations   
- good first step for security assessments  

![Identify Potential Vulnerabilities pt1](<Identify Potential Vulnerabilities pt1.png>)
![Identify Potential Vulnerabilities pt2](<Identify Potential Vulnerabilities pt2.png>)

---

### 8. Inspect Network Traffic

Command: **sudo tcpdump -i eth0**

**sudo tcpdump -i eth0**: captures and displays live packet data on the `eth0` interface  
- shows source/destination IPs, protocols, ports  
- useful for debugging or investigating suspicious activity 
- use `ens160` instead of `eth0`

![Inspect Network Traffic](<Inspect Network Traffic.png>)

---

### 9. Monitor Network Connections in Real-Time

Command: **sudo watch -n 1 netstat -tulnp**

**sudo watch -n 1 netstat -tulnp**: repeatedly runs netstat every second  
- `watch -n 1`: refreshes output every 1 second  
- `-p`: shows process IDs using the ports  
- useful for real-time monitoring of changing connections  

![Monitor Network Connections in Real-Time pt1](<Monitor Network Connections in Real-Time pt1.png>)
![Monitor Network Connections in Real-Time pt2](<Monitor Network Connections in Real-Time pt2.png>)

---

### 10. Check Firewall Rules

Command: **sudo ufw status verbose**

**sudo ufw status verbose**: displays the current firewall rules and their states  
- shows allowed/denied ports and services  
- ufw (uncomplicated firewall) is designed to make it easier to configure a firewall
- “verbose” gives more detail (e.g., logging, default rules)  
- useful for confirming firewall configurations  

![Check Firewall Ryles](<Check Firewall Rules.png>)