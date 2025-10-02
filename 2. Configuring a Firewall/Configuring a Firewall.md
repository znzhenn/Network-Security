# Configuring a Firewall

### I. Enabling Uncomplicated Firewalls (UFW)

#### 1. Checking the status of a Uncomplicated Firewall (UFW)

![check ufw status (1)](<check ufw status (1).png>)

#### 2. Ensure connection to ssh is permitted

    - When remotely accessing  server, it's important to allow traffic through port 22 before enabling UFW in order to prevent yourself from being locked out of your server

![enable connection to ssh](<enable connection to ssh.png>)

#### 3. Check open ports

![check open ports](<check open ports.png>)

#### 4. Enable the firewall

![enable firewall](<enable firewall.png>)

#### 5. Check firewall status

![check ufw status (5)](<check ufw status(5).png>)

#### 6. Allow ports for web server (80 & 443)

![enabling ports 80 & 443](<enabling ports 80 and 443.png>)


#### 7. Check firewall status

    - important info:
        firewall is active
        logging is on
        default status is listed

![check ufw status (verbose)](<check ufw status (verbose).png>)


#### 8. Blocking ex-employees P address

![deny ip](<deny ip.png>)

#### 9. Allowing 192.168.1.50 access to port 587

    - port 587 is typically used for sending email (SMTP)

![allow ip to smtp](<allow ip to smtp.png>)

#### 10. Check status

![check ufw status (10)](<check ufw status (10).png>)

### II. Enable UFW Logging

#### 1. Check UFW logging is enabled

![ufw logging enabled](<ufw logging enabled.png>)

#### 2. Change how detailed the logs are

Command: **sudo ufw logging `level`**

    low: minimal logging
    medium: includes blocked incoming packets with additional packet header details
    high: includes all blocked packets and connection information
    full: extensive logging of all ufw events

![logging status on full](<logging status on full.png>)

#### 3. Why is a typical UFW log entry useful to us?

     - a typical UFW log entry would be useful to us because it gives us detailed information about the network traffic that was blocked
    - allows us to figure out why something was blocked, where it came from

#### 4. View UFW logs

![view ufw logs](<ufw logs.png>)

#### 5. filter specific entries

    - there are no outputs for DENY because there's currently no deny rules active

![filter ufw logs](<filter ufw logs.png>)
