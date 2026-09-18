# Infrastructure Monitoring Setup with Nagios Core & NCPA

## Project Description
This project automates the deployment and configuration of an end-to-end infrastructure monitoring solution using **Nagios Core** and **Nagios Cross-Platform Agent (NCPA)**. It extends the Multi-VM development environment by establishing active monitoring across all 5 tier instances (Web, App, Database, Cache, and Message Queue).

It demonstrates essential DevOps practices including centralized metrics collection, active agent polling, proactive alerting thresholds, host-service dependency mapping, and infrastructure visibility.

---

## Architecture Overview
The monitoring architecture uses a centralized **Nagios Core** server that polls agent endpoints installed on each targeted node over encrypted traffic:

* **Nagios Core Server (Monitoring Host):** Central hub running the Nagios engine, Web UI dashboard, host definitions, and service check definitions.
* **Monitored Nodes (NCPA Agents):**
  * `web01` (Nginx): Tracks HTTP response status, CPU usage, and port 80/443 availability.
  * `app01` (Tomcat): Monitors Java process status, memory allocation, and port 8080.
  * `db01` (MariaDB): Checks MySQL daemon status, disk utilization, and port 3306.
  * `mc01` (Memcached): Tracks memory consumption, cache service availability, and port 11211.
  * `rmq01` (RabbitMQ): Monitors queue service status, process health, and port 5672.

### Monitoring Architecture Diagram
```text
                     ┌───────────────────────────┐
                     │    Nagios Core Server     │
                     │  (Dashboard & Alerts)     │
                     └─────────────┬─────────────┘
                                   │

              NRPE / NCPA Passive & Active Checks (Port 5693)
                                   │
      ┌──────────────┬─────────────┼─────────────┬──────────────┐
      ▼              ▼             ▼             ▼              ▼
  [ web01 ]      [ app01 ]     [ db01 ]      [ mc01 ]       [ rmq01 ]
   (Nginx)       (Tomcat)     (MariaDB)    (Memcached)    (RabbitMQ)


Tech Stack & Tools
Monitoring Engine: Nagios Core

Monitoring Agent: Nagios Cross-Platform Agent (NCPA) / NRPE Plugins

Target OS Environments: Ubuntu / CentOS Linux

Service Protocols: HTTPS, SSH, SNMP, NRPE

Automation & Scripting: Bash Shell Scripts

Dashboard & UI: Nagios Web Interface (Apache-backed)

Deployment & Setup Instructions
Prerequisites
The 5-VM environment (web01, app01, db01, mc01, rmq01) up and running.

Root or sudo access across all virtual machines.

1. Install NCPA Agent on Target VMs
Run the NCPA agent setup script on all 5 client virtual machines to enable metric reporting:

Bash
# SSH into a target node
vagrant ssh web01

# Execute the agent installation script
sudo bash scripts/install_ncpa.sh
Note: Ensure the NCPA token configured in ncpa.cfg matches your Nagios server check definition.

2. Deploy Nagios Core Monitoring Server
On the dedicated Nagios host machine:

Bash
# Clone the monitoring configuration repository
git clone [https://github.com/samahghazala/nagios-multivm-monitoring.git](https://github.com/samahghazala/nagios-multivm-monitoring.git)
cd nagios-multivm-monitoring

# Execute automated Nagios Core installation script
sudo bash scripts/install_nagios.sh
3. Apply Host and Service Configurations
Copy the pre-configured host and service definitions into Nagios:

Bash
sudo cp configs/hosts.cfg /usr/local/nagios/etc/objects/
sudo cp configs/services.cfg /usr/local/nagios/etc/objects/

# Verify configuration sanity
sudo /usr/local/nagios/bin/nagios -v /usr/local/nagios/etc/nagios.cfg

# Restart Nagios Core service
sudo systemctl restart nagios
4. Access the Dashboard
Open your browser and navigate to the Nagios Web Interface:

Plaintext
http://<NAGIOS_SERVER_IP>/nagios
Default User: nagiosadmin

Password: Configured during installation script setup.
