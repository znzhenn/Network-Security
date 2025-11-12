# **Configuring Snort for Server**

### I. **Update the System**
#### 1. Ensure the system is up to date

    Command: sudo apt update
    Command: sudo apt upgrade -y


---
### II. **Install Snort**
#### 1. Install Snort directly using apt:
    Command: sudo apt install snort -y

#### 2. FiTo find your network interface

    Command: ip a

---
### III. **Configure Snort**
#### 1. Customize the snort configuration file

    Command: sudo nano /etc/snort/snort.conf 

    ![configure snort](<configure snort.png>)

#### 2. Adjust the ipvar HOME_NET if needed:
    Command: ipvar HOME_NET 192.168.1.0/24 # Or whatever network range is appropriate



---
### IV. **Update and Manage Snort Rules**
#### 1. Download the community rules if necessary

    Command: sudo wget https://www.snort.org/downloads/community/community-rules.tar.gz
    Command: sudo tar -xvzf community-rules.tar.gz
    Command: sudo cp community-rules/* /etc/snort/rules/

    ![update and manage snort rules](<update and manage snort rules.png>)

#### 2. Manually edit the local rule file:
    
    Command: sudo nano /etc/snort/rules/local.rules

    ![test snort](<test snort.png>)
    
#### 3. Adding custom rules if need

    Command: alert icmp any any -> any any (msg:"ICMP detected"; sid:1000001; rev:1;)

---
### V. **Test Snort Configuration**
#### 1. Running a configuration test

---
### VI. **Running Snort in IDS Mode**
#### 1. Run snort in IDS mode to monitor traffic
    Command: sudo snort -c /etc/snort/snort.conf -i ens160

![running snort in ids mode](<running snort in ids mode.png>)

---
### VII. **Viewing Snort Logs**
#### 1. What files are fond in /var/log/snort?
![viewing snort logs](<viewing snort logs.png>)

#### 2. Do any of them contain any content?
- several files contain content

#### 3. Why or why not?
- snort has detected network traffic or rule matches, and has logged them in these alerts


---
### VIII. **Running Snort as a Daemon**
#### 1. Specify the interface to the monitor
    Command: sudo snort -D -c /etc/snort/snort.conf -i eth0

    ![using snort as a daemon](<using snort as a daemon.png>)