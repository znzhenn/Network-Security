# **Building a Lightweight SIEM and IDPS**

### **I. Key Concepts & Tools**
#### 1. Suricata -- IDPS
    Purpose: An open-source intrusion detection & prevention system that inspects packets and emits alerts in JSON.
Used to generate realistic network security events for our SIEM.

#### 2. Loki -- Log Database
    Purpose: Grafana’s lightweight log aggregation system. It stores logs with labels instead of heavy indexes. This is similar to Elasticsearch but is much lighter.
Acts like our SIEM-like backend (Centralizes and indexes log data for searching and correlation).

#### 3. Promtail -- Log Shipper
    Purpose: A small agent that tails log files and sends them to Loki.
Promtail reads Suricata’s JSON log ( eve.json ) and forwards it to Loki.

#### 4. LogCLI -- Query Tool
    Purpose: Command-line client for Loki queries, written by Grafana Labs.
Used to run searches, extract JSON fields, and perform quick analyses. There is no heavy web UI needed.

#### 5. Docker -- Container Platform
    Purpose: A lightweight virtualization platform that lets you run applications in isolated containers. Each container bundles an app and all its dependencies, so it runs the same way everywhere
 Docker is used to run Loki and Promtail without installing them directly on Ubuntu.

---
### **II. Setup and Tasks**

**Install the necessary tools**


`curl:` Command-line tool used for transferring data via HTTP/S, FTP, etc.
- Used to trigger HTTP requests (for Suricata's custom alert) aand to download files li ke LogCLI.

`jq:`  Lightweight JSON processor
- Used to pretty-print and filer Suricata's eve.json logs

`unzip:` Utility to extract .zip archives
- Needed because the LogCLI binary is packaged as a zip file

#### 1. Install curl, jq and unzip:
    Commands: 
    sudo apt update && sudo apt upgrade -y
    sudo apt -y install curl jq unzip

![install curl, jq, unzip](<install curl, jq, unzip.png>)


#### 2. Installing docker
    Command: curl -fsSL https://get.docker.com | sudo sh

![installing docker](<installing docker.png>)

#### 3. Adding myself to the docker group 
    Command: sudo usermod -aG docker "$USER"
allows us to run docker without sudio
    Command: newgrp docker
starts a fresh shell with the new group, docker

![add to docker group](<add to docker group.png>)


#### 4. Enable anad start the docker service
    Commands:
    sudo systemctl enable --now docker
    docker --version

![enable and start docker service](<enable and start docker service.png>)

---
### **III. Suricata** [Suricata vs Snort: detailed guide to the programs](https://medium.com/@redfanatic7/suricata-vs-snort-detailed-guide-to-the-programs-c331cff452a1)
#### 1. What is Suricata?
A high performance Network IDS, IPS and Network Security Monitoring engine
**Summary:** Suricata and Snort are both open-source Network Intrusion Detection Systems (NIDS) used to monitor network traffic for malicious activity. While both detect threats using signature- and anomaly-based methods, Suricata features a multi-threaded design that enables high-speed packet processing, scalability, and efficient rule management. Snort is a single-threaded, rule-based system known for its compatibility and extensive community support. Suricata performs better in high-traffic environments and offers easier setup with YAML configuration, while Snort uses fewer resources, making it more suitable for smaller networks. Both are free and well-documented.


#### 2. Preflight Setup for Suricataa
 Install the community rule set, create a local rules file, anad aligning Suricata's configuration so it can load and apply both sets of rules successfully.   
 
    Commands:
    sudo apt -y install suricata
    sudo apt -y install suricata-update
    sudo suricata-update

Find the interface name

    Command: ip -br a | awk '$1!="lo"{print $1, $3}'

![interface name](<interface name.png>)


Create a directory and file for our custom rules:

    Commands:
    sudo mkdir -p /etc/suricata/rules
    sudo touch /etc/suricata/rules/local.rules

![custom rule directory](<custom rule directory.png>)


Update the suricata.yaml file to the correct rule path and  open the file

    Command: sudo nano /etc/suricata/suricata.yaml

Enter the following command to test and validate your Suricata connection:

    Command: sudo suricata -T -c /etc/suricata/suricata.yaml -v
    man suricata

![validate suricata](<validate suricata.png>)

`-T:` Tells Suricata to test the configuration file  (suricata.yaml) for errors without actually starting the IDS/IPS engine

`-c:` Specifies which configuration file to use

`-v:` Enables verbose mode which prints detailed output to the terminal about what Suricata is doing


#### 3. Run Suricata
Stop the default service to control it manually

    Command: sudo systemctl stop suricata

![stop suricata](<stop suricata.png>)

Starts Suricata in the background 

    Command: sudo suricata -i $(ip -br a | awk '$1!="lo"{print $1; exit}') -D

![starts suricata in the background](<starts suricata in the background.png>)

Confirm it writes logs  "control + c" to exit

    Command: sudo tail -f /var/log/suricata/eve.json | jq .

Seperates each object to make the file more easily readible

    Command: sudo jq . /var/log/suricata/eve.json


**Question 1: What types of events (fields under "event_type" ) do you see in eve.json ?**
- Answer here : )



---
### **IV. Loki** [Loki: Effective Logging and Log Aggregation with Grafana](https://medium.com/@gpiechnik/loki-effective-logging-and-log-aggregation-with-grafana-c3356e7f13ad)
Loki acts as the central log database in the SIEM setup. It recieves logs, stores them,, and allows us to  search and analyze them using simple queries.
**Summary:** Loki is an efficent way to collect, store, and visualize logs. It works with Promtail to gather logs anad Gradana to display them, only indexing metadata to save resources while maintaining fast search capabilities. Loki also supports various log types for better system monitoring and analysis.

#### 1. Create directories for loki
- /etc/loki: where Loki’s configuration file will live.
- /var/lib/loki: Loki’s data directory. Used for storing log chunks and index files.
- {chunks,rules}: creates two subdirectories:
    - chunks/: where Loki stores compressed log data.
    - rules/: where Loki stores alerting or recording rules (not used in this lab but required by the config).
- The -p flag ensures that missing directories are created without errors.

    Command: sudo mkdir -p /etc/loki /var/lib/loki/{chunks,rules}

![create loki directories](<create loki directories.png>)

#### 2. Create a default Loki configuration file
// add an image of the yaml file here

- auth_enabled: false - turns off authentication for simplicity.
- server.http_listen_port: 3100 - tells Loki to listen on port 3100 for incoming connections.
- common.path_prefix and storage.filesystem - define where Loki should store logs (/var/lib/loki/chunks and /var/lib/loki/rules).
- schema_config: tells Loki how to structure and index the stored logs.
**In short:** This YAML file is Loki's instruction manual. It defines how it runs and where it saves data.


#### 3. Fix Permissions so Loki can write data. Give Loki's container user (UID 10001) permission to write  /var/lib/loki:

    Commands:
    sudo chown -R 10001:10001 /var/lib/loki
    sudo chmod -R u+rwX /var/lib/loki

![allow loki to write files](<allow loki to write files.png>)

#### 4. Start Loki in Docker

    Commands:
    sudo docker run -d --name loki -p 3100:3100 \
    -v /etc/loki:/etc/loki \
    -v /var/lib/loki:/var/lib/loki \
    grafana/loki:2.9.8 -config.file=/etc/loki/loki-config.yml

![start loki in docker](<start loki in docker.png>)

- -d: runs in detached (background) mode.
- --name loki: names the container “loki” for easy reference.
- -p 3100:3100: maps port 3100 from the container to your VM, so you can reach Loki at http://localhost:3100 .
- -v /etc/loki:/etc/loki: mounts your host’s config directory into the container.
- -v /var/lib/loki:/var/lib/loki: mounts the data directory so logs persist even if the container restarts.
- grafana/loki:2.9.8: specifies the Loki image and version.



#### 5. Check that Loki is running and ready
    Commands:
    docker ps
    curl -s http://localhost:3100/ready; echo

**Question 2: What port does Loki expose, and what API path recieves log data?**
- Answer here later : )


---
### **V. Run Promtail (Log Shipper)** [Promtail Agent](https://grafana.com/docs/loki/latest/send-data/promtail/)
Promtail is an agent that collects logs from your system and sends them to Loki. It’s designed to work hand-in-hand with Loki.
Promtail’s main job is to find, read, and forward log files. It attaches helpful labels, such as job="suricata" , to each log line before sending it to Loki. These labels make your logs easier to filter, group, and search later.
Promtail also keeps track of which parts of a file it has already read using a small positions file,
so if you restart it, it won’t resend old logs or miss new ones.
#### 1. Create Promtail  folders:
- /etc/promtail: where we’ll store Promtail’s config file.
- /var/lib/promtail: where Promtail keeps its positions file. This is a bookmark so it knows
how far it has read in each log and doesn’t resend lines as mentioned above.

    Command: sudo mkdir -p /etc/promtail /var/lib/promtail

#### 2. Write the Promtail config file:
//insert pic of the file here


- server.http_listen_port: 9080: small local status server (Promtail’s own HTTP endpoint).
    - clients.url: where Promtail sends logs — Loki’s HTTP push API on localhost:3100 .
    - positions.filename: the file that stores read offsets (prevents duplicates and missed lines).
    - scrape_configs: what to read and how to label it.
        - job_name: suricata: a logical name for this scrape job.
        - static_configs: we’re tailing a known file path.
            - labels.job: suricata: adds a queryable label ( {job="suricata"} ).
            - labels.path: /var/log/suricata/eve.json: the file to tail (Promtail reads this path).

#### 3. Run the Promtail container:

    Commands:
    sudo docker run -d --name promtail -p 9080:9080 \
    -v /etc/promtail:/etc/promtail \
    -v /var/log/suricata:/var/log/suricata:ro \
    -v /var/lib/promtail:/var/lib/promtail \
    grafana/promtail:2.9.8 \
    -config.file=/etc/promtail/promtail-config.yml

- -d: run in background (detached).
- --name promtail: easy to reference later ( docker logs promtail ).
- -p 9080:9080: maps container’s port 9080 to the VM’s 9080 (Promtail’s HTTP status page).
- -v /etc/promtail:/etc/promtail: mount your config into the container.
- -v /var/log/suricata:/var/log/suricata:ro: mount Suricata logs read-only (:ro) so Promtail can read eve.json .
- -v /var/lib/promtail:/var/lib/promtail: mount the positions storage so progress persists across restarts.
- grafana/promtail:2.9.8: the Promtail image and version.
-  -config.file=...: tell Promtail where the config is inside the container (the mounted path).

**Question 3: What role does Promtail play compared to Loki?**
- Answer here later : )

**Question 4: Why does Promtail track a “position file”? What problem does it solve?**
- Answer here later : )


---
### **VI. Install LogCLI and Test Queries**
LogCLI is Grafana Loki’s command-line tool. It lets you query, view, and analyze logs directly
from your terminal. There is no web dashboard needed.
You’ll use it to verify that Promtail is successfully sending Suricata logs into Loki.
#### 1. Download and instalal LogCLI
    Commands:
    curl -L https://github.com/grafana/loki/releases/download/v2.9.8/logcli-linux-arm64.zip -o /tmp/logcli.zip
    sudo unzip -o /tmp/logcli.zip -d /usr/local/bin
    sudo mv /usr/local/bin/logcli-linux-arm64 /usr/local/bin/logcli
    sudo chmod +x /usr/local/bin/logcli
    logcli --version    

- curl -L https://github.com/grafana/loki/releases/download/v2.9.8/logcli-linux-arm64.zip -o /tmp/logcli.zip: downloads the  file from the url, saving it in the temp directory
- sudo unzip -o /tmp/logcli.zip -d /usr/local/bin: extracts the contents of the file
- sudo mv /usr/local/bin/logcli-linux-arm64 /usr/local/bin/logcli: renames the extracted file to logcli (for simplicity)
- sudo chmod +x /usr/local/bin/logcli: changes the file permissions to make it executable
- logcli --version : runs the newly installed logcli tool and asks it to print its version number


#### 2. Verify connectivity and list available labels
Tests if LogCLI can connect to the Loki server running on `localhost:3100.` It lists all labels Loki has seen so  far
    Command: logcli labels --addr=http://localhost:3100

#### 3. Query recent logs. Explain in your own words what the command below is doing.
    Command: logcli query --addr=http://localhost:3100 --limit=10 '{job="suricata"}'



**Question 5: What labels do you see attached to your logs?**
- Answer here later : )

**Question 6: How do labels differ from full-text indexes?**
- Answer here later : )


---
### **VII. Generate Alerts and Analyze**
Trigger a atest alert to verify that the SIEM pipeline works from end to end
#### 1. Add a custom Suricata rule to guarantee an alert:
    Commands:
    echo 'alert http any any -> any any (msg:"LAB UA hit"; http.user_agent; content:"CPS-NETSEC-LAB"; sid:9900001; rev:1;)' \
        | sudo tee -a /etc/suricata/rules/local.rules 


- echo 'alert ...': creates a custom Suricata detection rule that matches specific HTTP
requests. The rule means: “If any HTTP request (any source/destination) contains the User-Agent string CPS-NETSEC-LAB , generate an alert named ‘LAB UA hit.’”
- sid:9900001: gives the rule a unique signature ID (used to identify alerts).
- rev:1: the rule’s revision number (used if you edit the rule later).
- sudo tee -a: appends this rule to /etc/suricata/rules/local.rules , the file for your custom or local rules.

#### 2. Restart Suricata to load the new rule
    Command: sudo systemctl restart suricata

If it fails, check the rule syntax:
    
    Command: sudo suricata -T -c /etc/suricata/suricata.yaml -v



#### 3. Trigger the alert:
    Command: curl -A "CPS-NETSEC-LAB" http://example.com/ || true

- This command sends an HTTP request with the User-Agent header "CPS-NETSEC-LAB" .
- Because that header matches your custom rule, Suricata will immediately generate an alert in eve.json .



#### 4. Query alerts in Loki:
    logcli query --addr=http://localhost:3100 --limit=50 \
    '{job="suricata"} |= "event_type\":\"alert\"" | json | line_format "
    {{.alert.signature}}"'


**Question 7: What is the command above doing?**
- Answer here : )

**Question 8: What alert message appears?**
- Answer here : )

---
### **VIII. Correlation  Challange**
#### 1. Find top source IPs in the last 5 minutes:
    logcli query --addr=http://localhost:3100 --limit=1000 --since=5m \
    '{job="suricata"} |= "event_type\":\"alert\"" | json | line_format "
    {{.src_ip}} "' \
    | sort | uniq -c | sort -nr | head


**Question 9: What does this simple command illustrate about correlation and aggregation in SIEMs?**
- Answer here : )

**Question 10: How might a SOC use this information in an investigation?**
- Answer here : )


---
### **IX. Create and Test Your Own Custom Rule**
Write a custom Suricata rule that detects a specific pattern or condition different from the one in this lab, then trigger it and verify it appears in your logs and in Loki
#### 1. What condition did your rule detect?



#### 2. How did you test and confirm that it triggered correctly?



#### 3. How would you modify your rule to make it more specific (to reduce false positives)?



#### 4. Why is fine-tuning rules important in real-world intrusion detection?





---
### **X. Lab wrap-Up**

#### 1. When you're done, stop and remove your docker containers:

    Commands:
    sudo docker stop promtail loki
    sudo docker rm promtail loki
    sudo apt purge -y suricata
    sudo docker system prune -a -f

#### 2. Lab Summary
