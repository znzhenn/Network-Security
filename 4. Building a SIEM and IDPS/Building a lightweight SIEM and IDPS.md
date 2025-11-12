# Building a Lightweight SIEM and IDPS

### I. Key Concepts & Tools
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
### II. Section Header
#### 1. Focus

#### 2. Focus

#### 3. Focus

#### 4. Focus

#### 5. Focus

---
### III. Section Header
#### 1. Focus

#### 2. Focus

#### 3. Focus

#### 4. Focus

#### 5. Focus




---
### IV. Section Header
#### 1. Focus

#### 2. Focus

#### 3. Focus

#### 4. Focus

#### 5. Focus




---
### V. Section Header
#### 1. Focus

#### 2. Focus

#### 3. Focus

#### 4. Focus

#### 5. Focus




---
### VI. Section Header
#### 1. Focus

#### 2. Focus

#### 3. Focus

#### 4. Focus

#### 5. Focus




---
### VII. Section Header
#### 1. Focus

#### 2. Focus

#### 3. Focus

#### 4. Focus

#### 5. Focus





---
### VIII. Section Header
#### 1. Focus

#### 2. Focus

#### 3. Focus

#### 4. Focus

#### 5. Focus


